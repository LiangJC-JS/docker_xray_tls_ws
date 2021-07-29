### 使用docker手动快速搭建xray+tls

#### 一、安装
* docker https://docs.docker.com/engine/install/ubuntu/
* git clone https://github.com/LiangJC-JS/docker_xray_tls_ws.git 

#### 二、申请免费ssl证书
* curl https://get.acme.sh | sh -s email=root@admin.com
* acme.sh --issue -d domian.com --standalone -k ec-256
* acme.sh --installcert -d domian.com --fullchainpath /etc/xray/tls/domian.com.crt --keypath /etc/xray/tls/domian.com.key --ecc
* acme.sh --upgrade --auto-upgrade
* chmod -R 755 /etc/xray/tls 

#### 三、构建docker镜像
* cd ~/docker_xray_tls_ws/templet
* unzip docker_xray_tls.zip
* cd docker_xray_tls
* docker build -t xray:1.4.2 .

#### 四、配置相关文件
* cd ~/docker_xray_tls_ws/templet
* unzip xray_templet.zip -d /
* unzip nginx_templet.zip -d /

#### 五、编辑配置文件
* vim /etc/xray/vless_tcp_inbounds.json	根据提示修改
* vim /etc/xray/vless_ws_inbounds.json	根据提示修改
* vim /etc/nginx/conf.d/alone.conf	把domian.com替换为你的域名

#### 六、创建网站
* groupadd -g 101 -S nginx && useradd -S -D -H -u 101 -h /var/cache/nginx -s /sbin/nologin -G nginx -g nginx nginx
* mkdir /var/www/
* git clone https://github.com/DIYsay/mikutap.git /var/www/html/
* chown -R nginx:nginx /var/www/html

#### 七、启动容器
* docker run -d --name xray --restart always -p 80:80 -p 443:443 -v /etc/xray:/etc/xray -v /etc/nginx:/etc/nginx -v /var/www/html:/usr/share/nginx/html/ xray:1.4.2

enjoy！
