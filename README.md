基于树莓派3B+Python3.5的OpenCV3.4的配置教程
https://www.cnblogs.com/Pyrokine/p/8921285.html

ubuntu 安装pip
# 安装Pip
sudo apt-get install python3-pip

# 检查 pip 是否安装成功
pip -V

sudo mkdir /mnt/share
sudo mount -t drvfs '\\192.168.199.1\vr' /mnt/share
sudo umount /mnt/share

mount -t cifs -o username=root,pass=860118dolphin  //192.168.199.1/vr data/
mount -t cifs -o username=root,pass=860118dolphin  //192.168.199.1/vr data/
 
sudo apt-get install cifs-utils
mount -t nfs -o rw 192.168.55.88:/var/ftp /mnt/nfs/

apt-get install curlftpfs

curlftpfs -o codepage=utf8 ftp://root:860118dolphin@192.168.199.1/sda1/ /mnt/ftp

mount -t nfs -o rw 192.168.55.88:/var/ftp /mnt/nfs/

echo "curlftpfs -o codepage=utf8 ftp://root:860118dolphin@192.168.199.1/sda1/ /mnt/ftp fuse allow_other,uid=0,gid=0 0 0" >> /etc/fstab

fusermount -u /ftp

