# VSCode + GDB  
Package Cortex-debug (basic version) must be installed in VSCode.  
Last version of OpenOCD must be installed for support of L and G family of STM32. Official repos have outdated version.  
`launch.json`  
```json  
{  
    "version": "0.2.0",  
    "configurations": [  
        {  
            "type": "cortex-debug",  
            "request": "launch",  
            "servertype": "openocd",  
            "cwd": "${workspaceRoot}",  
            "executable": "${workspaceRoot}/build/alohal_dev.elf",  
            "name": "GDB + OpenOCD",  
            "device": "STM32L432CE",  
            "configFiles": [  
                "interface/stlink-v2-1.cfg",  
                "target/stm32l4x.cfg"  
            ]  
        }  
    ]  
}  
```  

# OpenOCD build and install  
repo: https://github.com/ntfreak/openocd  
Dependencies:  
 - make  
 - libtool  
 - pkg-config  
 - autoconf  
 - automake  
 - texinfo  
 - libusb-1.0-0-dev

```bash  
./bootstrap  
./configure --enable-stlink  
make  
sudo make install  
```  
location of openocd scripts: `/usr/share/openocd/scripts/`  

#### Settings for flashing STM MCUs

```makefile  
flash:
	openocd -f interface/stlink.cfg -f target/stm32g0x.cfg -c "program build/$(TARGET).bin verify reset exit 0x08000000"

reset:
	openocd -f interface/stlink.cfg -f target/stm32g0x.cfg -c "reset run"
```

# arm-none-eabi-gdb --with-python support  
Run script below for installation of GDB for ARM with python support  
`build-gdb.sh`  
```bash  
#!/usr/bin/env sh  

export TARGET=arm-none-eabi  
export VERSION=9.1  

export PREFIX=$HOME/.local  
export PATH=$PATH:$PREFIX/bin  

export GDB=gdb-$VERSION  

rm -rf $GDB  

# Get archives  
wget http://ftp.gnu.org/gnu/gdb/$GDB.tar.gz  

# Extract archives  
tar xzvf $GDB.tar.gz  

mkdir build-gdb  
cd build-gdb  
../$GDB/configure --target=$TARGET --prefix=$PREFIX --enable-interwork --enable-multilib --with-python  
make  
make install  
```  
source: https://gist.github.com/JayKickliter  

# GDB init script for enabling of pretty printer output  
Create file .gdbinit on your home folder, will import python pretty printer module into gdb  
`.gdbinit`  
```python  
python  
import sys  
sys.path.insert(0, '/usr/share/gcc-8/python')  
sys.path.insert(0, '/usr/share/gdb')  
from libstdcxx.v6.printers import register_libstdcxx_printers  
register_libstdcxx_printers (None)  
end  
```  
source: http://tomszilagyi.github.io/2018/03/Remote-gdb-with-stl-pp  

# STM32CubeProgrammer JavaFX fix  
```bash  
sudo apt install openjfx=8u161-b12-1ubuntu2 libopenjfx-java=8u161-b12-1ubuntu2 libopenjfx-jni=8u161-b12-1ubuntu2 openjfx-source=8u161-b12-1ubuntu2  
sudo apt-mark hold libopenjfx-java libopenjfx-jni openjfx openjfx-source  
# select JDK8  
sudo update-alternatives --config java  
```  
source: https://github.com/wykys/STM32-tools  
