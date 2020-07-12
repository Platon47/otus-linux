
Определяем версию CentOS:
sudo cat /etc/redhat-release
CentOS Linux release 7.5.1804 (Core) 

Добавляем репозиторий для нашей версии:
sudo yum install http://download.zfsonlinux.org/epel/zfs-release.el7_5.noarch.rpm

Installed:
  zfs-release.noarch 0:1-5.el7.centos                                                                                                                                                                                
Complete!

sudo gpg --quiet --with-fingerprint /etc/pki/rpm-gpg/RPM-GPG-KEY-zfsonlinux
sudo yum-config-manager --enable zfs-kmod
sudo yum-config-manager --disable zfs

Убеждаемся что у нас выключен DKMS и включен KMOD
sudo cat /etc/yum.repos.d/zfs.repo

Теперь мы можем установить ZFS:
sudo yum install zfs -y

Перезагружаем виртуальную машину:
sudo reboot

Проверяем запущено ли ядро ZFS:
sudo lsmod | grep zfs

Загружаем ядро ZFS:
sudo modprobe zfs

Убеждаемся что оно загружено:
sudo lsmod | grep zfs

Создаём пулы:
sudo zpool create gzippool mirror sdb sdc
sudo zpool create lz4pool mirror sdd sde
sudo zpool create lzjbpool mirror sdf sdg
sudo zpool create zlepoo mirror sdh sdi

Включаем сжатие:
sudo zfs set compression=gzip gzippool
sudo zfs set compression=lz4 lz4pool
sudo zfs set compression=lzjb lzjbpool
sudo zfs set compression=zle zlepool 

Скачиваем файл War_and_Peace.txt на каждый из пулов:
wget -O War_and_Peace.txt http://www.gutenberg.org/ebooks/2600.txt.utf-8

Проверяем степень сжатия:
sudo  zfs get -r compression,compressratio
NAME      PROPERTY       VALUE     SOURCE
gzippool  compression    gzip      local
gzippool  compressratio  1.08x     -
lz4pool   compression    lz4       local
lz4pool   compressratio  1.08x     -
lzjbpool  compression    lzjb      local
lzjbpool  compressratio  1.07x     -
zlepool   compression    zle       local
zlepool   compressratio  1.08x     -
Наихудший из представленных алгоритмов - lzjbpool.

