"'Домашнее задание к занятию 1 «Disaster recovery и Keepalived»'" - `Копаческу Владимир`
Цель задания
В результате выполнения этого задания вы научитесь:

Настраивать отслеживание интерфейса для протокола HSRP;
Настраивать сервис Keepalived для использования плавающего IP
Чеклист готовности к домашнему заданию
Установлена программа Cisco Packet Tracer
Установлена операционная система Ubuntu на виртуальную машину и имеется доступ к терминалу
Сделан клон этой виртуальной машины, они находятся в одной подсети и имеют разные IP адреса
Просмотрены конфигурационные файлы, рассматриваемые на лекции, которые находятся по ссылке
Инструкция по выполнению домашнего задания
Сделайте fork репозитория c шаблоном решения к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
Выполните клонирование этого репозитория к себе на ПК с помощью команды git clone.
Выполните домашнее задание и заполните у себя локально этот файл README.md:
впишите вверху название занятия и ваши фамилию и имя;
в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
для корректного добавления скриншотов воспользуйтесь инструкцией «Как вставить скриншот в шаблон с решением»;
при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в инструкции по MarkDown.
После завершения работы над домашним заданием сделайте коммит (git commit -m "comment") и отправьте его на Github (git push origin).
Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
Любые вопросы задавайте в чате учебной группы и/или в разделе «Вопросы по заданию» в личном кабинете.

### Задание 1
Дана схема для Cisco Packet Tracer, рассматриваемая в лекции.
На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)
Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).
Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.
На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.

### Решение 1

`Скрин 1 к заданию 1`                                    
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/1.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/3.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/4.png)
![Схема](https://github.com/Replica63/Keepalived/blob/main/img/hsrp_advanced-kopacheskuvv.pkt)

---

### Задание 2
- Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции, используя пример конфигурационного файла.
- Настройте любой веб-сервер (например, nginx или simple python server) на двух виртуальных машинах
- Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.
- Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script
- На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html

### Решение 2

`Скрин двух ВМ:`
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.1.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.2.png)

`Конфигурация первого сервера "MASTER"`

```
 global_defs {
    enable_script_security
}

vrrp_script check_script {
      script "/home/kvv/keepalived/script.sh"
      interval 3
}

vrrp_instance www {
        state MASTER
        interface enp0s8
        virtual_router_id 4
        priority 255
        advert_int 1

        virtual_ipaddress {
             192.168.1.250/24
        }
}

```
`Конфигурация второго сервера "BACKUP"`

```
 global_defs {
    enable_script_security
}

vrrp_script check_script {
      script "/home/kvv/keepalived/script.sh"
      interval 3
}

vrrp_instance www {
        state BACKUP
        interface enp0s8
        virtual_router_id 4
        priority 222
        advert_int 1

        virtual_ipaddress {
             192.168.1.250/24
        }
}

```

`Скрипт файла script.sh`

```
#!/bin/bash
if [[ $(netstat -tuln | grep LISTEN | grep :80) ]] && [[ -f /var/www/html/index.html ]]; then
        exit 0
else
        sudo systemctl stop keepalived
fi
```

`Демонстрация keepalived:`

![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.3.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.4.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.5.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.6.png)
