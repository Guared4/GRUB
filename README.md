# GRUB
 
Загрузка системы. Работа с загрузчиком

Задание
Включить отображение меню Grub.
Попасть в систему без пароля несколькими способами.
Установить систему с LVM, после чего переименовать VG.

Задание выполнял в гостевой системе Ubuntu 22.04

1. Включил отображение меню Grub

root@ubuntu-dz:/home/guared# nano /etc/default/grub

Закомментировал строку, скрывающую меню и установил задержку для выбора пункта меню в 10 секунд.
#GRUB_TIMEOUT_STYLE=hidden
GRUB_TIMEOUT=10

Обновил конфигурацию загрузчика и перезагрузися для проверки.
root@ubuntu-dz:/home/guared# update-grub
Sourcing file `/etc/default/grub'
Sourcing file `/etc/default/grub.d/init-select.cfg'
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-5.15.0-127-generic
Found initrd image: /boot/initrd.img-5.15.0-127-generic
Warning: os-prober will not be executed to detect other bootable partitions.
Systems on them will not be added to the GRUB boot configuration.
Check GRUB_DISABLE_OS_PROBER documentation entry.
done

root@ubuntu-dz:/home/guared# reboot

см. скриншот
GNU GRUB_.jpg

2. Пробую попасть в систему без пароля несколькими способами
Для получения доступа необходимо запустить виртуальную машину и при выборе ядра для загрузки нажать e - в данном контексте edit. Попадаю в окно, где можно изменить параметры загрузки, см. скриншот:

GNU GRUB_edit.jpg

Способ 1. init=/bin/bash
В конце строки, начинающейся с linux, добавляю init=/bin/bash (см. скриншот: GNU GRUB_init=bin_bash.jpg) и нажимаю сtrl-x для загрузки в систему (см. скриншот: GNU GRUB_boot_root.jpg). В целом на этом все, я попал в систему. Но есть один нюанс. Рутовая файловая система при этом монтируется в режиме Read-Only. Чтобы перемонтировать ее в режим Read-Write, нужно воспользоваться командой:
mount -o remount,rw /
#см. скриншот: 
#GNU GRUB_root_rw.jpg

После чего убедился, прочитав вывод команды:
mount | grep root

Способ 2. Recovery mode
В меню загрузчика на первом уровне выбрал второй пункт (Advanced options…), далее загрузилось меню с указанием recovery mode в названии. 
Получил меню режима восстановления.
см. скриншот:
boot_recovery_mode.jpg

В этом меню сначала включаю поддержку сети (network) для того, чтобы файловая система перемонтировалась в режим read/write.
Далее выбираю пункт root и попадаю в консоль с пользователем root. 
В этой консоли можно производить любые манипуляции с системой.
см. скриншот 
root.jpg

3. Установить систему с LVM, после чего переименовать VG
Я установил систему Ubuntu 22.04 со стандартной разбивкой диска с использованием  LVM.
Первым делом посмотрю текущее состояние системы (список Volume Group):

root@ubuntu-dz:/home/guared# vgs
  VG        #PV #LV #SN Attr   VSize    VFree
  ubuntu-vg   1   1   0 wz--n- <148.76g <74.38g

Меня интересует вторая строка с именем Volume Group. Приступаю к переименованию:

root@ubuntu-dz:/home/guared# vgrename ubuntu-vg ubuntu-otus
  Volume group "ubuntu-vg" successfully renamed to "ubuntu-otus"

Далее правлю /boot/grub/grub.cfg. Везде заменяя старое название VG на новое (в файле дефис меняется на два дефиса ubuntu--vg ubuntu--otus).
После чего можно перезагружаться и, если все сделано правильно, успешно загружаюсь с новым именем Volume Group и проверяю:

root@ubuntu-dz:/home/guared# vgs
  VG          #PV #LV #SN Attr   VSize    VFree
  ubuntu-otus   1   1   0 wz--n- <148.76g <74.38g

----------------
end