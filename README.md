## Домашнее задание

PostgreSQL
Цель: Студент получил навыки работы сhot_standby.

- Настроить hot_standby репликацию с использованием слотов
- Настроить правильное резервное копирование

Для сдачи работы присылаем ссылку на репозиторий, в котором должны обязательно быть Vagranfile и плейбук Ansible, конфигурационные файлы postgresql.conf, pg_hba.conf и recovery.conf, а так же конфиг barman, либо скрипт резервного копирования. Команда "vagrant up" должна поднимать машины с настроенной репликацией и резервным копированием. Рекомендуется в README.md файл вложить результаты (текст или скриншоты) проверки работы репликации и резервного копирования.

## Решение

Проверка репликации
на ведущем: ps aux | grep sender
на ведущем: su postgres -c 'psql -c "select \* from pg_stat_replication;"'
на ведомом: ps aux | grep receiver

Создание бэкапа
barman backup pg-db-server

Создание бэкапа для проверки настроено на каждую минуту

```
    /etc/crontab
      * * * * * barman /usr/bin/barman backup pg-db-server
      * * * * * barman /usr/bin/barman cron
```

Вывод списка резервных копий
barman list-backup pg-db-server

Вывод более детальной информации по резервной копии
barman show-backup pg-db-server $(barman list-backup pg-db-server|awk 'NR == 1 {print $2}')

Список попадающих файлов в резервную копию
barman list-files pg-db-server $(barman list-backup pg-db-server|awk 'NR == 1 {print $2}')
