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

Python3.5的OpenCV3.4的配置教程:
sudo apt-get install build-essential git cmake pkg-config -y
sudo apt-get install libjpeg8-dev -y
sudo apt-get install libtiff5-dev -y
sudo apt-get install libjasper-dev -y
sudo apt-get install libpng12-dev -y
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev -y
sudo apt-get install libgtk2.0-dev -y
sudo apt-get install libatlas-base-dev gfortran -y

sudo cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D INSTALL_C_EXAMPLES=ON -D INSTALL_PYTHON_EXAMPLES=ON -D OPENCV_EXTRA_MODULES_PATH=/home/dolphin/Downloads/opencv_contrib/modules -D BUILD_EXAMPLES=ON -D WITH_LIBV4L=ON PYTHON3_EXECUTABLE=/usr/bin/python3.5 PYTHON_INCLUDE_DIR=/usr/include/python3.5 PYTHON_LIBRARY=/usr/lib/arm-linux-gnueabihf/libpython3.5m.so PYTHON3_NUMPY_INCLUDE_DIRS=/home/dolphin/.local/lib/python3.5/site-packages/numpy/core/include ..  

sudo make && sudo make install
