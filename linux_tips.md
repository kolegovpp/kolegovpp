## Tips for linux command line

**Burn bootable iso to usb by dd**

`sudo dd if=ubuntu.iso of=/dev/sdh bs=1024k status=progress && sync`

**RAID in my PC**

Get from https://www.dmosk.ru/miniinstruktions.php?mini=mdadm

`apt-get install mdadm` - устанавливаем mdadm.

`mdadm --zero-superblock --force /dev/sd{b,c}` - убиваем суперблоки у дисков.

Если есть старые данные, то - `wipefs --all --force /dev/sd{b,c}`.

`mdadm --create --verbose /dev/md0 -l 1 -n 2 /dev/sd{b,c}`, где `-l` - уровень рейда (1 - зеркалить два диска),
`-n 2` - количество дисков, `/dev/sd{b,c}` - диски которые объединяем.

`mdadm --detail --scan --verbose | awk '/ARRAY/ {print}' >> /etc/mdadm/mdadm.conf` добавляем в секцию `DEVICE partitions`
(через sydo ругалось в отказе доступа, внес вручную).

Далее форматируем RAID - `mkfs.ext4 /dev/md0`.

`cat /proc/mdstat` - проверяем состояние и синхронизацию дисков.


Для восстановления:

удаление сломаного - `mdadm /dev/md0 --remove /dev/sdc`.

`mdadm /dev/md0 --add /dev/sde` - добавляем новый. Должа начаться автоматическая синхронизация.

Увеличить скорость синхронизации - `echo '10000' > /proc/sys/dev/raid/speed_limit_min`. По дефолту min - 1000 Kb, max - 200 Mb.


Пересборка массива:

`mdadm --assemble --scan` - поиск необходимой конфигурации и восстановление RAID.

Указываем диски для пересборки - `mdadm --assemble /dev/md0 /dev/sdb /dev/sdc`.

