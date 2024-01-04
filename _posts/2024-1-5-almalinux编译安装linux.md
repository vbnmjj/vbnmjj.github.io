# alimalinux 编译安装python12

## 下载
    wget https://www.python.org/ftp/python/3.12.1/Python-3.12.1.tgz
## 
    tar -zxvf Python-3.12.1.tgz
## 配置编译选项
    cd Python-3.12.1
    ./configure --enable-optimization  #根据系统设置默认选项

## 编译源代码 && 安装
    make && make install

## 验证安装
    python -V


