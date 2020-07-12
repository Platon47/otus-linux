Определить алгоритм с наилучшим сжатием
---------------------------------------
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

Убеждаемся что у нас выключен DKMS и включен KMOD:

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


Определить настройки pool’a
---------------------------
Заливаем файл на нашу виртуальную машину:

vagrant upload /home/admin/cert/zfs_task1.tar.gz

Распаковываем файлы:

tar -xzvf zfs_task1.tar.gz 

Импортируем zpool:

zpool import -o readonly=on -d ${PWD}/zpoolexport/ otus

Определяем параметры:

Размер хранилища:

zpool list -o name,size

NAME   SIZE

otus   480M

Тип пула - mirror-0:

zpool status -v
  pool: otus
 state: ONLINE
  scan: none requested
config:

        NAME                                 STATE     READ WRITE CKSUM
        otus                                 ONLINE       0     0     0
          mirror-0                           ONLINE       0     0     0
            /home/vagrant/zpoolexport/filea  ONLINE       0     0     0
            /home/vagrant/zpoolexport/fileb  ONLINE       0     0     0

Значения recordsize,compression,checksum:

zfs get recordsize,compression,checksum

NAME            PROPERTY     VALUE      SOURCE

otus            recordsize   128K       local

otus            compression  zle        local

otus            checksum     sha256     local

otus/hometask2  recordsize   128K       inherited from otus

otus/hometask2  compression  zle        inherited from otus

otus/hometask2  checksum     sha256     inherited from otus

Найти сообщение от преподавателей
---------------------------------

Заливаем файл на нашу виртуальную машину:

vagrant upload /home/admin/cert/otus_task2.file

Удаляем ранее импортированный пул, т.к. он доступен только для чтения:

zpool destroy otus -f

Создаём новый zpool:

zpool create otus sdb sdc

Восстанавливаем локально снепшот:

zfs receive otus/storage@task2 < otus_task2.file

Сообщение в файле secret_message:

cat /otus/storage/task1/file_mess/secret_message 

https://github.com/sindresorhus/awesome

