---
title: "CLI 우분투 UEFI 설치 메뉴얼 / How to install CLI Ubuntu UEFI"
date: 2021-07-19 12:37:00 +0900
categories: CLI 우분투 설치 Ubuntu install UEFI
---
우분투 16.04 (Xenial) 64비트 기준 CLI 버전 UEFI 설치 방법입니다.
<br><br>

Ubuntu Live USB 제작후 부팅
```
$ sudo su
# apt update && apt install -y openssh-server
# /etc/init.d/ssh restart
# passwd ubuntu
# ifconfig
PuTTY 연결
```
<br>

디스크 파티셔닝
```
$ sudo su
# parted -l
# parted /dev/sda
# (parted) mklabel gpt
# (parted) mkpart ESP fat32 1MiB 128MiB
# (parted) mkpart primary xfs 128MiB 100%
# (parted) set 1 boot on
# (parted) quit
# partprobe /dev/sda
# mkfs.vfat -F 32 /dev/sda1
# mkfs.ext4 /dev/sda2
# mount /dev/sda2 /mnt
```
<br>

베이스 설치
```
# apt update && apt install -y debootstrap
# debootstrap --arch amd64 xenial /mnt
```
<br>

apt 소스 설정
```
# cp /etc/apt/sources.list /mnt/etc/apt/sources.list
# nano /mnt/etc/apt/sources.list
deb http://archive.ubuntu.com/ubuntu/ xenial main restricted universe
deb-src http://archive.ubuntu.com/ubuntu/ xenial main restricted universe

deb http://security.ubuntu.com/ubuntu/ xenial-security main restricted universe
deb-src http://security.ubuntu.com/ubuntu/ xenial-security main restricted universe

deb http://archive.ubuntu.com/ubuntu/ xenial-updates main restricted universe
deb-src http://archive.ubuntu.com/ubuntu/ xenial-updates main restricted universe
```
<br>

네트워크 설정
```
# echo hostname > /mnt/etc/hostname
# nano /mnt/etc/hosts
127.0.1.1		hostname
127.0.0.1		localhost
::1			ip6-localhost ip6-loopback
ff02::1			ip6-allnodes
ff02::1			ip6-allrouters

# nano /mnt/etc/network/interfaces.d/eth0
auto eth0
iface eth0 inet static
address 172.16.0.xxx
netmask 255.255.255.0
gateway 172.16.0.1

auto lo
iface lo inet loopback

# nano /mnt/etc/resolvconf/resolv.conf.d/head
nameserver 8.8.8.8
nameserver 8.8.4.4
```
<br>

/etc/fstab 수정
```
# blkid
# nano /mnt/etc/fstab
PARTUUID=...[/dev/sda2]...	/	ext4	defaults	1	1
PARTUUID=...[/dev/sda1]...	/boot/efi	vfat	nofail,x-systemd.device-timeout=1	0	1
```
<br>

chroot
```
# sudo mkdir -p /mnt/boot/efi
# sudo mount /dev/sda1 /mnt/boot/efi
# sudo mount --bind /dev /mnt/dev
# sudo mount -t devpts /dev/pts /mnt/dev/pts
# sudo mount -t proc proc /mnt/proc
# sudo mount -t sysfs sysfs /mnt/sys
# sudo mount -t tmpfs tmpfs /mnt/tmp
# sudo chroot /mnt /bin/bash
# export HOME=/root
```
<br>

키보드 레이아웃 & 표준 시간 설정
```
# dpkg-reconfigure locales
en_US.UTF-8 UTF-8
# dpkg-reconfigure tzdata
Asia/Seoul
```
<br>

리눅스 커널, nano 설치
```
# apt update && apt install -y nano linux-image-generic linux-headers-generic
Continue without installing GRUB
```
<br>

root 패스워드 설정
```
# passwd
```
<br>

그룹 생성
```
# addgroup --system lpadmin
# addgroup --system lxd
# addgroup --system sambashare
```
<br>

tmpfs 활성화
```
# cp /usr/share/systemd/tmp.mount /etc/systemd/system/
# systemctl enable tmp.mount
```
<br>

GRUB 설치 & 설정 (UEFI)
```
# apt install -y grub-efi-amd64
# grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ubuntu --recheck --no-floppy
# update-grub
# update-initramfs -u -k all

# nano /etc/default/grub
#GRUB_HIDDEN_TIMEOUT=0
GRUB_TIMEOUT=5
GRUB_CMDLINE_LINUX_DEFAULT=""
GRUB_TERMINAL=console
```
<br>

우분투 설치
```
# CLI 버전
# apt update && apt install -y ubuntu-standard
# GUI 버전
# apt update && apt install -y ubuntu-desktop
```
<br>

SSH 설치
```
# apt install -y openssh-server
# nano /etc/ssh/sshd_config
PermitRootLogin no
PermitEmptyPasswords no
X11Forwarding yes
```
<br>

사용자 생성 
```
# adduser user
# cp -a /etc/skel/.[!.]* /home/user
# chown -R user:user /home/user
# usermod -a -G adm,cdrom,dip,lpadmin,plugdev,sambashare,sudo user
```
<br>

재부팅
```
# exit
# shutdown -r now
```
<br>

VMware Tools 설치 (가상머신일 경우)
```
$ sudo su
# mkdir /cdrom
# mount /dev/sr1 /cdrom
# mkdir /t
# cp /cdrom/VMwareTools-10.3.23-17030940.tar.gz /t/
# cd /t
# tar xzvf VMwareTools-10.3.23-17030940.tar.gz
# cd vmware-tools-distrib
# ./vmware-install.pl
# cd /
# umount -l /cdrom
# rm -rf /cdrom
# rm -rt /t
```
<br>

자동 업데이트 끄기 
```
# echo 'APT::Periodic::Enable "0";' > /etc/apt/apt.conf.d/10periodic
```
<br>

python3 -> python
```
# update-alternatives --install /usr/bin/python python /usr/bin/python3 1
```
<br>

net-tools, logrotate, tree, zip, unzip 설치
```
# apt install -y net-tools logrotate tree zip unzip
```
<br>

log2ram 설치
```
# git clone https://github.com/azlux/log2ram.git
# cd log2ram
# ./install.sh
# nano /etc/log2ram.conf
SIZE=128M
MAIL=false
LOG_DISK_SIZE=320M

# shutdown -r now
$ sudo su
# rm -rf log2ram
```
<br>

제대로 설치 되었는지 확인
```
# df -h --total
tmpfs, log2ram 확인
# dmesg | grep -i "EFI v"
UEFI 확인
```
<br>

임시 폴더 생성
```
# mkdir t
# cd t
```
<br>

유용한 툴들 설치
```
# curl https://sh.rustup.rs -sSf | sh
# wget https://github.com/ogham/exa/releases/download/v0.10.1/exa-linux-x86_64-v0.10.1.zip
# unzip exa-linux-x86_64-v0.10.1.zip -d exa-linux-x86_64-v0.10.1
# mv exa-linux-x86_64-v0.10.1/bin/exa /usr/local/bin/

# wget https://github.com/sharkdp/bat/releases/download/v0.18.2/bat_0.18.2_amd64.deb
# dpkg -i bat_0.18.2_amd64.deb

# wget https://github.com/sharkdp/fd/releases/download/v8.2.1/fd_8.2.1_amd64.deb
# dpkg -i fd_8.2.1_amd64.deb

# wget https://github.com/BurntSushi/ripgrep/releases/download/13.0.0/ripgrep_13.0.0_amd64.deb
# dpkg -i ripgrep_13.0.0_amd64.deb

# apt install snapd
# snap install procs
# nano /etc/environment
"...:/snap/bin"
# source /etc/environment

# curl https://raw.githubusercontent.com/scopatz/nanorc/master/install.sh | sh
# nano /etc/nanorc
set quiet
set brackets ""')>]}"
set linenumbers
set smooth
set softwrap
set tabsize 4
```
<br>

임시 폴더 삭제
```
# cd ..
# rm -rf t
```
<br>

ls & exa 출력 색상 변경
```
# nano /etc/environment
EXA_COLORS="da=36"

# nano ~/.bashrc
LS_COLORS=$LS_COLORS:"di=1;36:"
export LS_COLORS

# nano /etc/skel/.bashrc
LS_COLORS=$LS_COLORS:"di=1;36:"
export LS_COLORS

# exit
$ nano ~/.bashrc
LS_COLORS=$LS_COLORS:"di=1;36:"
export LS_COLORS
```
<br>

명령어 히스토리 자동 백업
```
$ mkdir command_history
$ cd command_history
$ nano backup_history.sh
#!/bin/bash
cd ~/command_history
tstamp=$(date +%y%m%d%H%M%S)
fname="backup_$tstamp"

HISTFILE=~/.bash_history
set -o history

history > "$fname"
tar czvf "$fname.tar.gz" "$fname"
rm -rf "$fname"
$ chmod +x backup_history.sh
$ crontab -e
# 매일 00시 00분에 백업
0 0 * * * ~/command_history/backup_history.sh
```
