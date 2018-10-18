基于树莓派3B+Python3.5的OpenCV3.4的配置教程
https://www.cnblogs.com/Pyrokine/p/8921285.html

![pi](http://latex.codecogs.com/png.latex?\frac{1}{\pi}=\frac{2\sqrt{2}}{9801}\sum_{k=0}^\infty\frac{(4k)!(1103%2B26390k)}{(k!)^4396^{4k}})


https://blog.csdn.net/leaves_joe/article/details/67656340
/** CMAKE_BUILD_TYPE是编译方式
* CMAKE_INSTALL_PREFIX是安装目录
* OPENCV_EXTRA_MODULES_PATH是加载额外模块
* INSTALL_PYTHON_EXAMPLES是安装官方python例程
* BUILD_EXAMPLES是编译例程（这两个可以不加，不加编译稍微快一点点，想要C语言的例程的话，在最后一行前加参数INSTALL_C_EXAMPLES=ON \）
**/

sudo cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D OPENCV_EXTRA_MODULES_PATH=/home/dolphin/Download/opencv_contrib-3.4.1/modules \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D INSTALL_C_EXAMPLES=OFF \
    -D BUILD_EXAMPLES=OFF \
    -D WITH_LIBV4L=ON ..

sudo apt-get install synaptic

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

sudo cmake -D CMAKE_BUILD_TYPE=RELEASE \
           -D CMAKE_INSTALL_PREFIX=/usr/local \
           -D INSTALL_C_EXAMPLES=OFF \
           -D INSTALL_PYTHON_EXAMPLES=OFF \
           -D OPENCV_EXTRA_MODULES_PATH=/home/dolphin/Downloads/opencv_contrib/modules \
           -D BUILD_EXAMPLES=OFF \
           -D WITH_LIBV4L=ON PYTHON3_EXECUTABLE=/usr/bin/python3.5 PYTHON_INCLUDE_DIR=/usr/include/python3.5 PYTHON_LIBRARY=/usr/lib/arm-linux-gnueabihf/libpython3.5m.so PYTHON3_NUMPY_INCLUDE_DIRS=/home/dolphin/.local/lib/python3.5/site-packages/numpy/core/include ..  

sudo make && sudo make install
