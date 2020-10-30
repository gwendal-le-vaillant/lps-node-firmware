# LPS node firmware  [![Build Status](https://api.travis-ci.org/bitcraze/lps-node-firmware.svg)](https://travis-ci.org/bitcraze/lps-node-firmware)

This project contains the source code for the Local positioning System node firmware. 

See the [Bitcraze documentation](https://www.bitcraze.io/documentation/repository/lps-node-firmware/master/) for more information.

## Dependencies

You'll need to use either the Crazyflie VM, install some ARM toolchain or the Bitcraze docker builder image. If you install a toolchain, the [arm embedded gcc](https://launchpad.net/gcc-arm-embedded) toolchain is recomended.

Frameworks for unit testing are pulled in as git submodules. To get them when cloning

```bash
git clone --recursive https://github.com/bitcraze/lps-node-firmware.git
```
        
or if you already have a cloned repo and want the submodules
 
```bash
git submodule init        
git submodule update        
```

### OS X
```bash
brew tap PX4/homebrew-px4
brew install gcc-arm-none-eabi
```

### Debian/Ubuntu linux

Download, extract and put in your path the compiler from https://launchpad.net/gcc-arm-embedded. For example:
```
mkdir -p ~/opt/
cd ~/opt
wget https://launchpad.net/gcc-arm-embedded/5.0/5-2016-q3-update/+download/gcc-arm-none-eabi-5_4-2016q3-20160926-linux.tar.bz2
tar xvf gcc-arm-none-eabi-5_4-2016q3-20160926-linux.tar.bz2
mv gcc-arm-none-eabi-5_4-2016q3 gcc-arm-none-eabi
rm gcc-arm-none-eabi-5_4-2016q3-20160926-linux.tar.bz2
echo "export PATH=\$PATH:\$HOME/opt/gcc-arm-none-eabi/bin" >> ~/.bashrc
```

On 64Bit Linux you also need to install some 32Bit libs:
```
sudo apt-get install libncurses5:i386
```

If all works you will be able to execute ```arm-none-eabi-gcc```:
```
$ arm-none-eabi-gcc --version
arm-none-eabi-gcc (GNU Tools for ARM Embedded Processors) 5.4.1 20160919 (release) [ARM/embedded-5-branch revision 240496]
Copyright (C) 2015 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```
### Arch Linux

```bash
sudo pacman -S community/arm-none-eabi-gcc community/arm-none-eabi-gdb community/arm-none-eabi-newlib
```

### Windows

> `TODO: Please share!`

## Compiling

`make`

or 

`docker run --rm -v ${PWD}:/module bitcraze/builder ./tools/build/compile`

or 

`tools/do compile`

## Folder description:

> `TODO: Please share!`

# Make targets:
```
all        : Shortcut for build
flash      : Flash throgh jtag
openocd    : Launch OpenOCD
dfu        : Flash throgh DFU 
```

## Unit testing

We use [Unity](https://github.com/ThrowTheSwitch/unity) and [cmock](https://github.com/ThrowTheSwitch/CMock) for unit testing.

To run all tests 

`./tools/do test`

## Modif IRISIB

Le makefile a été modifié pour pouvoir choisir si l'on veut compiler un firmware de typer ancre ou un firmware de type tag.
- Si la ligne 6 est décommentée, le STM enverra des données vers le port UART (indiqué FTDI sur les nodes Bitcraze, relié sur le port UART du RPI sur le shield). Il faut donc la décommenter pour recevoir les infos de sniffing du shield.
- Si la ligne 7 est décommentée, le firmware sera compilée en mode sniffer. Sinon, il sera compilée en mode ancre TDoA3

Le fichier main.cpp a donc été modifié pour effectuer l'une ou l'autre configuration. Voir ligne 134 jusqu'à 141.

En particulier, pour régler l'adresse d'une ancre, il faut modifier la ligne 140 et donc recompiler le firmware avant de vouloir flasher ladite ancre, car l'ID ne sera plus modifiable. Ou alors simplement enlever cette ligne, afin de pouvoir régler l'ID comme on règle la postion de l'ancre.


## Comment mettre à jour le firmware des ancres et des tags

Avant de flasher, une fois le code modifiée, il faut le recompiler. Pour cela, exécuter les commandes `make clean` puis `make`.

### Ancres maison

Pour mettre à jour le firmware des ancres maison, il faut utiliser le mode DFU. Pour faire démarrer les ancres en mode DFU, il faut enfoncer le bouton du PCB avant de le brancher en USB.

pour mettre à jour le firmware, il faut lancer la commande `make dfu`.

### Shield Tag

Pour mettre à jour le tag du shield RPI, il faut le flasher via SWD. Pour cela, il faut utiliser un ST-LINK v2.1 (par exemple celui d'un nucleo) et un adapteur de ce type : https://www.bitcraze.io/products/debug-adapter-kit/, en utuilisant le cable JST à 6 contacts.

une fois l'ordinateur relié au shield via le ST-LINK et l'adapteur, lancer la commande `make flash`.

### Nodes Bitcraze

Pour les nodes Bitcraze, les deux méthodes sont utilisables. Le flash via SWD a l'avantage d'être beaucoup plus rapide, et de ne pas nécessiter de redémarrer le STM pour qu'il démarre en mode DFU.
