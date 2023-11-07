```javascript
yum install lrzsz --downloadonly --downloaddir=/root/rpm
```



```javascript
mkdir -p /var/www/html
yum install docker-ce --downloadonly --downloaddir=/var/www/html
createrepo -v /var/www/html
chmod -R 755 /var/www/html

docker run -itd --name yum --restart=always -p 9999:80 -v /var/www/html/:/usr/local/apache2/htdocs/ httpd:latest
iptables  -F
mkdir /etc/yum.repos.d/backup
mv /etc/yum.repos.d/*.repo  /etc/yum.repos.d/backup
vi /etc/yum.repos.d/test.repo
[TEST]
name=Test Repository
baseurl=http://127.0.0.1:9999
gpgcheck=0
enabled=1

yum clean all
yum repolist
yum makecache
```
