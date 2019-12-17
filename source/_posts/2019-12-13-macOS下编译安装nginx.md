mac下编译安装nginx

操作系统：macOS Catalina 10.15.1

nginx：nginx-1.16.1

openssl：github master分支

```
~ ./Configure --prefix=/opt/openssl darwin64-x86_64-cc shared enable-ec_nistp_64_gcc_128 no-ssl2 no-ssl3 no-comp --openssldir=/usr/local/ssl/macos-x86_64
make depend
sudo make install
```

参考

[OpenSSL - Compilation and Installation](https://wiki.openssl.org/index.php/Compilation_and_Installation#OS_X)

编译：

```
./conure --prefix=/opt/nginx --with-stream --with-threads \
--with-http_ssl_module --with-http_v2_module --without-http_fastcgi_module \
--build="renzheng build on macos at `date +%Y%m%d`" --with-debug \
--with-openssl=/Users/renz2048/work/openssl

Configuration summary
  + using threads
  + using system PCRE library
  + using OpenSSL library: /Users/renz2048/work/openssl
  + using system zlib library

  nginx path prefix: "/opt/nginx"
  nginx binary file: "/opt/nginx/sbin/nginx"
  nginx modules path: "/opt/nginx/modules"
  nginx configuration prefix: "/opt/nginx/conf"
  nginx configuration file: "/opt/nginx/conf/nginx.conf"
  nginx pid file: "/opt/nginx/logs/nginx.pid"
  nginx error log file: "/opt/nginx/logs/error.log"
  nginx http access log file: "/opt/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
  
~ sudo make
sudo make install
/Library/Developer/CommandLineTools/usr/bin/make -f objs/Makefile install
test -d '/opt/nginx' || mkdir -p '/opt/nginx'
test -d '/opt/nginx/sbin' \
		|| mkdir -p '/opt/nginx/sbin'
test ! -f '/opt/nginx/sbin/nginx' \
		|| mv '/opt/nginx/sbin/nginx' \
			'/opt/nginx/sbin/nginx.old'
cp objs/nginx '/opt/nginx/sbin/nginx'
test -d '/opt/nginx/conf' \
		|| mkdir -p '/opt/nginx/conf'
cp conf/koi-win '/opt/nginx/conf'
cp conf/koi-utf '/opt/nginx/conf'
cp conf/win-utf '/opt/nginx/conf'
test -f '/opt/nginx/conf/mime.types' \
		|| cp conf/mime.types '/opt/nginx/conf'
cp conf/mime.types '/opt/nginx/conf/mime.types.default'
test -f '/opt/nginx/conf/fastcgi_params' \
		|| cp conf/fastcgi_params '/opt/nginx/conf'
cp conf/fastcgi_params \
		'/opt/nginx/conf/fastcgi_params.default'
test -f '/opt/nginx/conf/fastcgi.conf' \
		|| cp conf/fastcgi.conf '/opt/nginx/conf'
cp conf/fastcgi.conf '/opt/nginx/conf/fastcgi.conf.default'
test -f '/opt/nginx/conf/uwsgi_params' \
		|| cp conf/uwsgi_params '/opt/nginx/conf'
cp conf/uwsgi_params \
		'/opt/nginx/conf/uwsgi_params.default'
test -f '/opt/nginx/conf/scgi_params' \
		|| cp conf/scgi_params '/opt/nginx/conf'
cp conf/scgi_params \
		'/opt/nginx/conf/scgi_params.default'
test -f '/opt/nginx/conf/nginx.conf' \
		|| cp conf/nginx.conf '/opt/nginx/conf/nginx.conf'
cp conf/nginx.conf '/opt/nginx/conf/nginx.conf.default'
test -d '/opt/nginx/logs' \
		|| mkdir -p '/opt/nginx/logs'
test -d '/opt/nginx/logs' \
		|| mkdir -p '/opt/nginx/logs'
test -d '/opt/nginx/html' \
		|| cp -R html '/opt/nginx'
test -d '/opt/nginx/logs' \
		|| mkdir -p '/opt/nginx/logs'
```

