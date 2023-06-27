## Tips for linux command line

### PC's tips

**Burn bootable iso to usb by dd**

`sudo dd if=ubuntu.iso of=/dev/sdh bs=1024k status=progress && sync`

**Conver vector files using inkscape**

For .emf files. Pay attention filenames without spaces:

`for i in *.emf; do inkscape --file $i --export-plain-svg $i.svg; done`

**Conver RGB to CMYK color space in PDF file**

`gs -dSAFER -dBATCH -dNOPAUSE -dNOCACHE -sDEVICE=pdfwrite -sColorConversionStrategy=CMYK -dProcessColorModel=/DeviceCMYK -sOutputFile=output_CMYK.pdf Input.pdf`


### GIS' tips

**Transformation unref vector layer to ref one**

Coordinates legend: ungeoref_x ungeoref_y georef_x georef_y elevation:

`ogr2ogr -progress -f "GPKG" -a_srs EPSG:4326 -tps -gcp 4.407 -4,487 155 61.333333333 -gcp 8.22951 -678.36087 155 60.666666667 -gcp 497.170404 -3.639216 156 61.333333333 -gcp 506.459434 -676.671875 156 60.66666667 Tectonic_schema.gpkg Tectonic_schema_noref.gpkg`


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

**a multi-threading download**

`axel -a -n [Num_of_Thread] link1 link2 link3 ...`
