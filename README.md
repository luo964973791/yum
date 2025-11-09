```javascript
#第一种方法：把所有版本的依赖都下载下来
for pkg in createrepo bind lrzsz wget nvidia-docker2 libnvidia-container1 libnvidia-container-tools nvidia-container-toolkit nvidia-container-toolkit-base; do
  yum list "$pkg" --showduplicates \
    | awk -v p="$pkg" '$1 ~ "^"p"." {print $1,$2}' \
    | sed 's/\.[^.]* /-/' \
    | xargs -n1 -I{} yum download --resolve --installroot=/tmp/fake-root --releasever=8 {} --destdir=/root/test
done

mkdir -p /mnt/local-rpm
createrepo /mnt/local-rpm
cat > /etc/yum.repos.d/local-test.repo <<'EOF'
[local-test]
name=Local RPM Repo
baseurl=file:///mnt/local-rpm
enabled=1
gpgcheck=0
EOF



#第二种方法：只下载最新版本的，如果没有网络的服务器上面安装过老版本,而这条命令下载的是最新版本的yum源,这些源拿到没有网络的服务器上面有可能会有冲突
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
