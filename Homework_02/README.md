## Lesson 2 Homework

1. Добавил два диска в Vagrantfile:

:sata5 => {
:dfile => './sata5.vdi', 
:size => 250, 
:port => 5 
},

:sata6 => {
:dfile => './sata6.vdi', 
:size => 250, 
:port => 6 
},
 
2. Запустил виртуалку с 6-ью дисками и создал raid 6 командой:

mdadm --create --verbose /dev/md0 -l 6 -n 5􀀄/dev/sd{b,c,d,e,f}

3. Создал каталог и выдал на него фулл права:

mkdir /etc/mdadm/
sudo chmod 777 /etc/mdadm/

4. Далее создал конфигурационный файл mdadm.conf: 

echo "DEVICE partitions" > /etc/mdadm/mdadm.conf
sudo mdadm --detail --scan --verbose | awk '/ARRAY/ {print}' >>
/etc/mdadm/mdadm.conf

5. Произвёл все оставшиеся действия по методичке.

6. Перезагрузил виртульную машину и убедился что рейд в порядке.

7. Создал Vagrantfile, который сразу собирает систему с подключенным рейдом.