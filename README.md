```javascript
#第一种方法：把所有版本的依赖都下载下来,试用麒麟系统,--releasever=8代表centos8
for pkg in nvidia-docker2 libnvidia-container1 libnvidia-container-tools nvidia-container-toolkit nvidia-container-toolkit-base; do
  yum list "$pkg" --showduplicates \
    | awk -v p="$pkg" '$1 ~ "^"p"." {print $1,$2}' \
    | sed 's/\.[^.]* /-/' \
    | xargs -n1 -I{} yum download --resolve --installroot=/tmp/fake-root --releasever=8 {} --destdir=/root/test
done

#命令适用于Tlinux系统
for pkg in curl lzo lzo-devel snappy zlib zlib-devel which httpd mysql expect openssl openssl-devel bzip2-libs lz4-devel asciidoc cyrus-sasl cyrus-sasl-devel cyrus-sasl-gssapi gcc gcc-c++ libxml2-devel libxslt-devel sqlite-devel gmp-devel cyrus-sasl-plain R dstat lrzsz redhat-lsb-core python2-devel python3-rpm-macros python3-pip python3-libs python3-setuptools python3 python3-rpm-generators python3-devel rsyslog libffi libffi-devel kernel-headers re2c autoconf make sudo python2-pip python2-wheel python3-mysqlclient openldap-servers sshpass docker-ce-cli docker-ce ; do   yum list "$pkg" --showduplicates     | awk -v p="$pkg" '$1 ~ "^"p"." {print $1,$2}'     | sed 's/\.[^.]* /-/'     | xargs -n1 -I{} yum download --resolve {} --destdir=/root/R; done



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
