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
rsync -avz pi@192.168.24.13:/lib sysroot
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
ls sysroot/usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.1.0
ln -s sysroot/opt/vc/lib/libGLESv2.so sysroot/usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.1.0
ln -s sysroot/opt/vc/lib/libEGL.so sysroot/opt/vc/lib/libEGL.so.1
ln -s sysroot/opt/vc/lib/libGLESv2.so sysroot/opt/vc/lib/libGLESv2.so.2
wget https://raw.githubusercontent.com/riscv/riscv-poky/master/scripts/sysroot-relativelinks.py
chmod +x sysroot-relativelinks.py 
./sysroot-relativelinks.py sysroot
```




