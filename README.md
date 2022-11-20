# Qt5.14.2RPI4
make Rpi4 ready to code with Qt.14.2

in this project the host and target connect to eatch other using SSH over wifi:<br />
> host ip: 192.168.1.112. and hostname: `mahdidesk` <br />
> target ip: 192.168.24.13, and targetname: `pi`

# Install Raspbian 
Download the raspbian and install it using balena eatcher
```
wget https://downloads.raspberrypi.org/raspbian/images/raspbian-2020-02-14/2020-02-13-raspbian-buster.zip
```
go to the preferences and make below changes into the os:


## 0. open Target command

### 0.1 uncomment the source page download 
```
sudo vi /etc/apt/sources.list
```

### 0.2 run the following commands
```
sudo apt update
sudo apt full-upgrade
sudo reboot
sudo rpi-update
sudo reboot

sudo apt-get build-dep qt5-qmake
sudo apt-get build-dep libqt5webengine-data

sudo apt-get install libboost1.58-all-dev libudev-dev libinput-dev libts-dev libmtdev-dev libjpeg-dev libfontconfig1-dev 
sudo apt-get install libssl-dev libdbus-1-dev libglib2.0-dev libxkbcommon-dev libegl1-mesa-dev libgbm-dev libgles2-mesa-dev mesa-common-dev
sudo apt-get install libasound2-dev libpulse-dev gstreamer1.0-omx libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev  gstreamer1.0-alsa
sudo apt-get install libvpx-dev libsrtp0-dev libsnappy-dev libnss3-dev
sudo apt-get install "^libxcb.*"
sudo apt-get install flex bison libxslt-dev ruby gperf libbz2-dev libcups2-dev libatkmm-1.6-dev libxi6 libxcomposite1
sudo apt-get install libfreetype6-dev libicu-dev libsqlite3-dev libxslt1-dev libavcodec-dev libavformat-dev libswscale-dev 
sudo apt-get install libgstreamer0.10-dev gstreamer-tools libraspberrypi-dev libx11-dev libglib2.0-dev 
sudo apt-get install freetds-dev libsqlite0-dev libpq-dev libiodbc2-dev firebird-dev libjpeg9-dev libgst-dev libxext-dev libxcb1 libxcb1-dev libx11-xcb1 
sudo apt-get install libx11-xcb-dev libxcb-keysyms1 libxcb-keysyms1-dev libxcb-image0 libxcb-image0-dev libxcb-shm0 libxcb-shm0-dev libxcb-icccm4 libxcb-icccm4-dev 
sudo apt-get install libxcb-sync1 libxcb-sync-dev libxcb-render-util0 libxcb-render-util0-dev libxcb-xfixes0-dev libxrender-dev libxcb-shape0-dev libxcb-randr0-dev 
sudo apt-get install libxcb-glx0-dev libxi-dev libdrm-dev libssl-dev libxcb-xinerama0 libxcb-xinerama0-dev
sudo apt-get install libatspi-dev libssl-dev libxcursor-dev libxcomposite-dev libxdamage-dev libfontconfig1-dev 
sudo apt-get install libxss-dev libxtst-dev libpci-dev libcap-dev libsrtp0-dev libxrandr-dev libnss3-dev libdirectfb-dev libaudio-dev

sudo mkdir /usr/local/qt5pi
sudo chown pi:pi /usr/local/qt5pi
```

## 1. Host libs
```
sudo apt update
sudo apt upgrade 
ping 192.168.24.13
sudo apt install gcc git bison python gperf pkg-config -y
sudo apt install make libclang-dev build-essential -y
sudo mkdir /opt/qt5pi
chown mahdidesk:mahdidesk /opt/qt5pi
cd /opt/qt5pi
```

## 2. Toolchain

```
wget https://releases.linaro.org/components/toolchain/binaries/latest-7/arm-linux-gnueabihf/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf.tar.xz
tar xf gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf.tar.xz 
export PATH=$PATH:/opt/qt5pi/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf/bin
```

## 3. Qt everywhare

```
wget https://download.qt.io/official_releases/qt/5.15/5.15.2/single/qt-everywhere-src-5.15.2.tar.xz
tar xf qt-everywhere-src-5.15.2.tar.xz 
cat qt-everywhere-src-5.15.2/qtbase/mkspecs/linux-arm-gnueabi-g++/qmake.conf
```
## 4. sync target with host (using rsync)
```
ping 192.168.24.13
rsync -avz pi@192.168.24.13:/lib sysroot
rsync -avz pi@192.168.24.13:/usr/include sysroot/usr
rsync -avz pi@192.168.24.13:/usr/lib sysroot/usr
rsync -avz pi@192.168.24.13:/opt/vc sysroot/opt
```

## 5. Link the Required libs
```
mv sysroot/usr/lib/arm-linux-gnueabihf/libEGL.so.1.1.0 sysroot/usr/lib/arm-linux-gnueabihf/libEGL.so.1.1.0_backup
ln -s sysroot/opt/vc/lib/libEGL.so sysroot/usr/lib/arm-linux-gnueabihf/libEGL.so.1.1.0
mv sysroot/usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.1.0 sysroot/usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.1.0_backup
ln -s sysroot/opt/vc/lib/libGLESv2.so sysroot/usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.1.0
mv sysroot/usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.1.0 sysroot/usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.1.0_backup
ln -s sysroot/opt/vc/lib/libEGL.so sysroot/opt/vc/lib/libEGL.so.1
ln -s sysroot/opt/vc/lib/libGLESv2.so sysroot/opt/vc/lib/libGLESv2.so.2
wget https://raw.githubusercontent.com/riscv/riscv-poky/master/scripts/sysroot-relativelinks.py
chmod +x sysroot-relativelinks.py 
./sysroot-relativelinks.py sysroot
```
## 5. sync again the host and target
```
rsync -avz pi@192.168.24.13:/lib sysroot
rsync -avz pi@192.168.24.13:/usr/include sysroot/usr
rsync -avz pi@192.168.24.13:/usr/lib sysroot/usr
rsync -avz pi@192.168.24.13:/opt/vc sysroot/opt
```

## 6. make Qt developement folder
first make qt folder and then make the qmake in it.
```
mkdir qt5build
cd qt5build/../qt-everywhere-src-5.15.2/configure -opengl es2 -device linux-rasp-pi4-v3d-g++ -device-option CROSS_COMPILE=/opt/qt5pi/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf/bin/arm-linux-gnueabihf- -sysroot /opt/qt5pi/sysroot -prefix /usr/local/qt5pi -opensource -confirm-license -skip qtscript -skip qtwayland -skip qtdatavis3d -nomake examples -make libs -pkg-config -no-use-gold-linker -v
make -j8   // it take about the 1 hour for me
make install
cd /opt/qt5pi/
```
## 7. sync generated folder to target 
```
rsync -avz sysroot/usr/local/qt5pi pi@192.168.24.13:/usr/local
```









