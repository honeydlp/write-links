#闭包(clouser)——javascript闭包理解：
    https://juejin.im/post/5a290f276fb9a0452341c921
#代理(nginx)——前端必会的nginx代理配置，跨域再也不用求后端小锅锅了:
    https://juejin.im/post/5a2a6e136fb9a0452405bf8d
    前端必会的nginx代理配置，跨域再也不用求后端小锅锅了~~
    No1. nginx下载和安装：http://nginx.org/en/download.html 选择相应版本下载，下载好后，解压安装包，一路可视化界面操作，一般选择是，balabala,结束。
    No2. nginx运行，找到nginx.exe 双击，当然还要命令操作，nginx start ...详细见官方api
    No3. 配置 conf/nginx.conf (就前端而言，需要代理接口，文件用本地)
          做个假设：本地文件路径（C:/Users/admin/Desktop/webProject）接口请求(http://www.test.com/api)
          1、配置本地代码的代理相关代码：
            location ^~ / {
              root C:/Users/admin/Desktop/webProject;

            }
          2、配置接口跨域代理相关代码：
            location ^~ /apis {
              rewrite ^.+apis/?(.*)$ /$1 break;       # rewrite是重写，正则匹配到apis开始，$1代表括号里匹配到的路径，重写成括号里匹配到的路径；
              add_header Access-Control-Allow-Origin *;
              proxy_pass http://www.test.com/api;
            }
    No4. 启动服务。双击nginx.exe,每次配置修改都需要重启服务。浏览器地址栏里输入localhost:80或127.0.0.1:80或本机ip:80 即可访问C:/Users/admin/Desktop/webProject目录，但是需手动输入该文件夹下的index.html或者xxx.html,看到你写的页面，说明配置本地代码的服务已成功，如果一切接口请求正常，说明配置接口服务成功。
    No5. 注意点
        1.代码中ajax请求路径 /apis/xxx/yyy,实现代理的接口http://www.test.com/api/xxx/yyy
        2.nginx默认端口是80，确保端口不被占用。
        3.有疑惑信息，可以看日志logs/ 下的文件
    No6. 缺点 需要在前端代码请求路径里多添加层/apis 或者自己定义的，不过写个baseUrl,分线上线下也就以下子解决了。
    No7. 全部配置（可直接使用，替换nginx.conf）

        #user  nobody;
        worker_processes  1;

        #error_log  logs/error.log;
        #error_log  logs/error.log  notice;
        #error_log  logs/error.log  info;

        #pid        logs/nginx.pid;
        # taskkill /F /IM nginx.exe /T

        events {
            worker_connections  1024;
        }

        http {
            #include ../v_hostconf/*.conf;
            include       mime.types;
            default_type  application/octet-stream;
            access_log off;
            sendfile        on;
            #tcp_nopush     on;

            #keepalive_timeout  0;
            keepalive_timeout  65;

            server_names_hash_bucket_size  64;

            #gzip  on;
            
            server {
                listen 80;
                add_header Access-Control-Allow-Origin *;
                add_header Access-Control-Allow-Headers X-Requested-With;
                add_header Access-Control-Allow-Methods GET,POST,OPTIONS;
                location ^~ /apis {
                  rewrite ^.+apis/?(.*)$ /$1 break;
                  add_header Access-Control-Allow-Origin *;

            
                  # include uwsgi_params;

                    proxy_pass http://www.test.com/api;
                }
              location ^~ / {
                    root C:/Users/admin/Desktop/webProjectn;
              }	
            }
        }
     
