1. 	
	node_exporter 
	-Cоздайть простой unit-файл для node_exporter:


[Unit]
Description=Node Exporter
Documentation=https://github.com/prometheus/node_exporter/blob/master/README.md
After=network-online.target

[Service]
EnvironmentFile=-/etc/default/node_exporter
ExecStart=/bin/bash -c /home/vagrant/node_exporter-1.3.0.linux-amd64/node_exporter* $OPTIONS
KillMode=process
Restart=on-failure
RestartSec=3
User=node_exporter
Group=node_exporter
Type=simple

[Install]
WantedBy=multi-user.target


Дописал пару параметров для загрузки файла из /etc/default/node_exporter
Запускаем bash c ключём -с

	-Поместите его в автозагрузку

sudo systemctl enable node_exporter - создаётся символическая ссылка.

Created symlink /etc/systemd/system/multi-user.target.wants/node_exporter.service → /etc/systemd/system/node_exporter.service.


	-Предусмотреть возможность добавления опций к запускаемому процессу через внешний файл	
	
Запуск через файл который находится тут - ExecStart=/usr/local/bin/node_exporter

	-Удостовериться, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.

Прописываем:

sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
sudo systemctl status node_exporter.service

Запуск, стоп и перезапуск выполняется корректно.

 node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; disabled; vendor preset: enabled)
     Active: active (running) since Mon 2022-01-10 20:35:50 UTC; 9s ago
   Main PID: 1581 (node_exporter)
      Tasks: 3 (limit: 467)
     Memory: 2.2M
     CGroup: /system.slice/node_exporter.service

	
2.	
	Опции node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.

Находим все метрики связаные с CPU - curl http://localhost:9100/metrics | grep node_cpu_seconds

node_cpu_seconds_total{cpu="0",mode="idle"} 4666.97
node_cpu_seconds_total{cpu="0",mode="iowait"} 3.45
node_cpu_seconds_total{cpu="0",mode="irq"} 0
node_cpu_seconds_total{cpu="0",mode="nice"} 0
node_cpu_seconds_total{cpu="0",mode="softirq"} 1.58
node_cpu_seconds_total{cpu="0",mode="steal"} 0
node_cpu_seconds_total{cpu="0",mode="system"} 16.32
node_cpu_seconds_total{cpu="0",mode="user"} 24.72
 
Находим все метрики связаные с memory - curl http://localhost:9100/metrics | grep node_memory | grep gauge

node_memory_Active_file_bytes gauge
node_memory_AnonHugePages_bytes gauge
node_memory_AnonPages_bytes gauge
node_memory_Bounce_bytes gauge
node_memory_Buffers_bytes gauge
node_memory_Cached_bytes gauge
node_memory_CmaFree_bytes gauge
node_memory_CmaTotal_bytes gauge
node_memory_CommitLimit_bytes gauge
node_memory_Committed_AS_bytes gauge
node_memory_DirectMap2M_bytes gauge
node_memory_DirectMap4k_bytes gauge
node_memory_Dirty_bytes gauge
node_memory_FileHugePages_bytes gauge
node_memory_FilePmdMapped_bytes gauge
node_memory_HardwareCorrupted_bytes gauge
node_memory_HugePages_Free gauge
node_memory_HugePages_Rsvd gauge
node_memory_HugePages_Surp gauge
node_memory_HugePages_Total gauge
node_memory_Hugepagesize_bytes gauge
node_memory_Hugetlb_bytes gauge
node_memory_Inactive_anon_bytes gauge
node_memory_Inactive_bytes gauge
node_memory_Inactive_file_bytes gauge
node_memory_KReclaimable_bytes gauge
node_memory_KernelStack_bytes gauge
node_memory_Mapped_bytes gauge
node_memory_MemAvailable_bytes gauge
node_memory_MemFree_bytes gauge
node_memory_MemTotal_bytes gauge
node_memory_Mlocked_bytes gauge
node_memory_NFS_Unstable_bytes gauge
node_memory_PageTables_bytes gauge
node_memory_Percpu_bytes gauge
node_memory_SReclaimable_bytes gauge
node_memory_SUnreclaim_bytes gauge
node_memory_ShmemHugePages_bytes gauge
node_memory_ShmemPmdMapped_bytes gauge
node_memory_Shmem_bytes gauge
node_memory_Slab_bytes gauge
node_memory_SwapCached_bytes gauge
node_memory_SwapFree_bytes gauge
node_memory_SwapTotal_bytes gauge
node_memory_Unevictable_bytes gauge
node_memory_VmallocChunk_bytes gauge
node_memory_VmallocTotal_bytes gauge
node_memory_VmallocUsed_bytes gauge

Находим все метрики связаные с memory - curl http://localhost:9100/metrics | grep node_disk | grep counter

node_disk_discarded_sectors_total counter
node_disk_discards_completed_total counter
node_disk_discards_merged_total counter
node_disk_io_time_seconds_total counter
node_disk_io_time_weighted_seconds_total counter
node_disk_read_bytes_total counter
node_disk_read_time_seconds_total counter
node_disk_reads_completed_total counter
node_disk_reads_merged_total counter
node_disk_write_time_seconds_total counter
node_disk_writes_completed_total counter
node_disk_writes_merged_total counter

Находим все метрики связаные с memory - curl http://localhost:9100/metrics | grep network | grep counter

node_network_carrier_down_changes_total counter
node_network_carrier_up_changes_total counter
node_network_receive_bytes_total counter
node_network_receive_compressed_total counter
node_network_receive_drop_total counter
node_network_receive_errs_total counter
node_network_receive_fifo_total counter
node_network_receive_frame_total counter
node_network_receive_multicast_total counter
node_network_receive_packets_total counter
node_network_transmit_bytes_total counter
node_network_transmit_carrier_total counter
node_network_transmit_colls_total counter
node_network_transmit_compressed_total counter
node_network_transmit_drop_total counter
node_network_transmit_errs_total counter
node_network_transmit_fifo_total counter
node_network_transmit_packets_total counter

curl http://localhost:9100/metrics | grep network | grep gauge

node_network_carrier gauge
node_network_device_id gauge
node_network_dormant gauge
node_network_flags gauge
node_network_iface_id gauge
node_network_iface_link gauge
node_network_iface_link_mode gauge
node_network_info gauge
node_network_mtu_bytes gauge
node_network_net_dev_group gauge
node_network_protocol_type gauge
node_network_speed_bytes gauge
node_network_transmit_queue_length gauge
node_network_up gauge

3. 
	Установить Netdata заменить 
sudo apt install -y netdata - Установил.
заменил localhost в конфигурационном файле /etc/netdata/netdata.conf на bind to = 0.0.0.0,
	
	Добавьте в Vagrantfile проброс порта Netdata и сделайте vagrant reload:
Добавил и перезагрузил - заработало.
config.vm.network "forwarded_port", guest: 19999, host: 19999

Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
Можно:
dmesg | grep virt
[    0.008758] CPU MTRRs all blank - virtualized system.
[    0.232929] Booting paravirtualized kernel on KVM
[    4.659946] systemd[1]: Detected virtualization oracle.

+ Потребление и колличество ресурсов при сравнении с хостовой машиной различаются.

5.
	Как настроен sysctl fs.nr_open на системе по-умолчанию?


sysctl -n fs.nr_open
Это обозначает максимальное количество файловых дескрипторов, которые может выделить процесс. 
Значение по умолчанию — 1024*1024 (1048576).
Фактический лимит зависит от лимита ресурсов RLIMIT_NOFILE.

ulimit -a

core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 1559
max locked memory       (kbytes, -l) 65536
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 1559
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited


ulimit -n
1024 
	Узнайть, что означает этот параметр.  
Максимальное количество открытых файловых дескрипторов
	Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)?	
Системный


6. Запустить любой долгоживущий процесс в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. 
Для простоты работайте в данном задании под root (sudo -i).

root@Ubuntu-nginx:~# ps
    PID TTY          TIME CMD
   1451 pts/1    00:00:00 bash
   1464 pts/1    00:00:00 ps
root@Ubuntu-nginx:~# unshare -f --pid --mount-proc /bin/bash
root@Ubuntu-nginx:~# screen
root@Ubuntu-nginx:~# ps
    PID TTY          TIME CMD
      1 pts/1    00:00:00 bash
      8 pts/1    00:00:00 ps
root@Ubuntu-nginx:~# sleep 1h

exit
ps aux

netdata      704  1.3  5.4 180828 26512 ?        Ssl  19:50   0:07 /usr/sbin/netdata -D
node_ex+     705  0.0  2.2 715708 11192 ?        Ssl  19:50   0:00 /usr/local/bin/node_exporter
root         726  0.0  0.6   9412  2960 ?        Ss   19:50   0:00 /usr/sbin/cron -f
root         728  0.0  1.5  12176  7424 ?        Ss   19:50   0:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
daemon       729  0.0  0.4   3792  2296 ?        Ss   19:50   0:00 /usr/sbin/atd -f
root         739  0.0  0.3   8428  1852 tty1     Ss+  19:50   0:00 /sbin/agetty -o -p -- \u --noclear tty1 linux
root         814  0.0  0.5 295412  2672 ?        Sl   19:50   0:00 /usr/sbin/VBoxService --pidfile /var/run/vboxadd-service.sh
netdata      846  1.4  0.6  53144  3136 ?        S    19:50   0:07 /usr/lib/netdata/plugins.d/apps.plugin 1
netdata      848  0.1  0.6   4028  3120 ?        S    19:50   0:00 bash /usr/lib/netdata/plugins.d/tc-qos-helper.sh 1
netdata      850  0.0  0.1   4608   768 ?        S    19:50   0:00 /usr/lib/netdata/plugins.d/nfacct.plugin 1
root        1036  0.0  0.0      0     0 ?        I<   19:50   0:00 [cfg80211]
vagrant     1090  0.0  1.9  18376  9508 ?        Ss   19:50   0:00 /lib/systemd/systemd --user
vagrant     1091  0.0  0.6 103068  3244 ?        S    19:50   0:00 (sd-pam)
systemd+    1258  0.0  1.6  26736  7956 ?        Ss   19:51   0:00 /lib/systemd/systemd-networkd
root        1382  0.0  1.8  13800  8988 ?        Ss   19:51   0:00 sshd: vagrant [priv]
vagrant     1422  0.0  1.2  13932  6228 ?        S    19:51   0:00 sshd: vagrant@pts/0
vagrant     1423  0.0  0.8   9836  4052 pts/0    Ss   19:51   0:00 -bash
root        1438  0.0  0.9  11856  4560 pts/0    S    19:52   0:00 sudo -i
root        1440  0.0  0.8   9836  4004 pts/0    S    19:52   0:00 -bash
root        1449  0.0  0.6   9420  2944 pts/0    S+   19:52   0:00 screen							<----
root        1450  0.0  0.5   9716  2616 ?        Ss   19:52   0:00 SCREEN
root        1451  0.0  0.8   9836  4020 pts/1    Ss   19:52   0:00 /bin/bash
root        1465  0.0  0.1   8080   520 pts/1    S    19:53   0:00 unshare -f --pid --mount-proc /bin/bash			<----
root        1466  0.0  0.8   9836  3944 pts/1    S    19:53   0:00 /bin/bash							<----
root        1495  0.0  0.1   8076   592 pts/1    S+   19:58   0:00 sleep 1h							<----
root        1502  0.0  1.8  13800  9120 ?        Ss   19:59   0:00 sshd: vagrant [priv]
vagrant     1542  0.2  1.2  13932  6108 ?        S    19:59   0:00 sshd: vagrant@pts/2
vagrant     1543  0.0  0.8   9836  3984 pts/2    Ss   19:59   0:00 -bash
root        1550  0.0  0.9  11856  4624 pts/2    S    19:59   0:00 sudo -i
root        1552  0.0  0.8   9836  3976 pts/2    S    19:59   0:00 -bash
root        1561  0.0  0.6  11492  3396 pts/2    R+   19:59   0:00 ps uax


7.
	Найти информацию о том, что такое :(){ :|:& };:. 


Команда является логической бомбой. Она оперирует определением функции с именем ‘:‘, 
Она продолжает своё выполнение снова и снова, пока система не зависнет.


	Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?

функция вызывает сама себя дважды по имени: один раз на переднем плане и один раз в фоне. 
Ограничение по процессам можно посмотреть:
ulimit -u
Изменить можно: 
ulimit -u 1560


	Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 


	вызов dmesg расскажет, какой механизм помог автоматической стабилизации. 
Не знаю точно, но должны была срабоать ограницение по создание файлов 
dmesg >> /tmp/logg.md

cat /tmp/logg.md |greep cgroup

Log показывает:

 dmesg | grep cgroup
[    0.306511] *** VALIDATE cgroup1 ***
[    0.306512] *** VALIDATE cgroup2 ***
[14849.409604] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-11.scope
