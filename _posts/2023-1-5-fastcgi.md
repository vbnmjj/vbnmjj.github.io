## 什么是fastcgi

FastCGI（Fast Common Gateway Interface）是一种用于改进Web服务器与应用程序之间通信的协议。它旨在提高CGI（Common Gateway Interface）的性能，通过将常驻进程（persistent processes）引入到通信过程中，从而避免了为每个请求都启动新的进程的开销。

以下是FastCGI的一些关键特点和详细说明：

    持续进程（Persistent Processes）：
        在传统的CGI模型中，为每个请求启动一个新的进程，这在高流量情况下可能导致性能问题。FastCGI允许服务器保持一个或多个常驻进程，这些进程可以处理多个请求，减少了进程启动和关闭的开销。

    性能优化：
        由于FastCGI支持常驻进程，它能够更有效地管理系统资源，减少了每个请求的处理时间。这对于处理大量的并发请求非常有利。

    多协议支持：
        FastCGI可以通过不同的传输协议与Web服务器通信，如TCP/IP、Unix域套接字等。这使得它更加灵活，可以在各种环境中使用。

    并发处理：
        FastCGI服务器可以同时处理多个请求，这有助于提高服务器的并发性能。

    环境变量和标准输入/输出：
        与CGI类似，FastCGI通过环境变量和标准输入/输出进行通信。然而，由于它是基于持续进程的，它可以更高效地利用这些通信通道。

    多语言支持：
        FastCGI并不限定于特定的编程语言，因此可以与多种语言一起使用，如PHP、Python、Ruby等。

    配置和管理：
        FastCGI的配置通常在Web服务器上进行，可以通过配置文件或服务器设置进行调整。服务器管理员可以灵活地配置FastCGI服务器的参数，以满足特定需求。

总体而言，FastCGI通过引入常驻进程和采用更有效的通信方式，提高了Web服务器与应用程序之间的性能和效率，特别是在高流量和并发请求的场景下。

## fastcgi协议

FastCGI协议是Web服务器与FastCGI应用程序之间进行通信的协议。它定义了服务器和应用程序之间如何交换数据、传递参数以及处理请求的规范。以下是FastCGI协议的一些关键特点：

    通信方式：
        FastCGI使用一种持久连接的方式，通过套接字（Socket）或其他底层传输协议（如TCP/IP或Unix域套接字）与Web服务器通信。这与传统的CGI方式不同，后者每次请求都会启动一个新的进程。

    角色和身份：
        FastCGI协议定义了两种主要的角色：Web服务器（Server）和FastCGI应用程序（Responder）。Web服务器是负责接收HTTP请求并将其转发给FastCGI应用程序的实体，而FastCGI应用程序则是实际处理请求的部分。

    记录（Records）：
        FastCGI通信基于记录的概念。记录是协议中的基本数据单元，它包含一些标头信息和相关的数据。有不同类型的记录，例如请求记录（请求头部和环境变量等信息）、响应记录（应用程序的输出数据）等。

    多路复用：
        FastCGI支持多路复用，允许在单个连接上并发处理多个请求。这可以通过在记录中使用不同的请求ID（Request ID）来实现，确保服务器和应用程序正确地匹配请求和响应。

    错误处理：
        FastCGI协议定义了一套错误处理机制，用于在通信过程中处理各种错误情况。这包括错误记录，可以在协议层面上指示发生的错误。

    环境变量：
        类似于传统的CGI，FastCGI也使用环境变量传递请求的上下文信息，如请求方法、请求URI等。

    标准输入和输出：
        FastCGI协议通过标准输入和标准输出通信。请求记录中包含了请求的标准输入，而响应记录中包含了应用程序的标准输出。

    管理命令：
        FastCGI协议定义了一些管理命令，允许服务器与FastCGI应用程序进行交互，包括启动、关闭、重启应用程序等。

FastCGI协议的设计旨在提高性能和效率，通过引入持久连接和多路复用等机制，使得服务器和应用程序之间的通信更加高效，适用于高流量和并发请求的环境。

## 理解
2 4 8 16 32 64 128 256 . . . . . . . 65536

协议结构
    typedef struct {
        uchar version;
        uchar type; //被次recore 的类型
        uchar requestid1  ; // 本次record对应的请求id，占用两个字节
        uchar requestid0 ; //
        uchar contentLength1; //body体的大小,占用两个字节
        uchar contentLength0;
        uchar paddingLength; //额外块大小
        uchar reserved;

        /* body */
        uchar contentData[coententlength];
        uchar paddingData[paddinglength];
    }
    
    协议中，头 由8个字节组成，从头中读取body的长度后，从tcp流里读取大小等于该长度的数据，就是body体。

## php-fpm
fastcgi的进程管理器

    fpm其实是fastcgi的协议解析器，nginx等中间件将用户请求按照fastcgi的规则打包好，通过tcp传给fpm，fpm就会按照fastcgi协议格式，将tcp流解析为真正的数据了。

nginx解析请求格式

    用户访问http://127.0.0.1/index.php?a=1&b=2 ,果web目录是/var/www/html，那么Nginx会将这个请求变成如下key-value对：
    {
    'GATEWAY_INTERFACE': 'FastCGI/1.0',
    'REQUEST_METHOD': 'GET',
    'SCRIPT_FILENAME': '/var/www/html/index.php',
    'SCRIPT_NAME': '/index.php',
    'QUERY_STRING': '?a=1&b=2',
    'REQUEST_URI': '/index.php?a=1&b=2',
    'DOCUMENT_ROOT': '/var/www/html',
    'SERVER_SOFTWARE': 'php/fcgiclient',
    'REMOTE_ADDR': '127.0.0.1',
    'REMOTE_PORT': '12345',
    'SERVER_ADDR': '127.0.0.1',
    'SERVER_PORT': '80',
    'SERVER_NAME': "localhost",
    'SERVER_PROTOCOL': 'HTTP/1.1'
    }



