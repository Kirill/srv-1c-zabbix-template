# Мониторинг сервера приложений 1С:Предприятия с помощью Zabbix

В репозитории пример шаблона и конфигурационные файлы для zabbix-agent для windows и linux.

## Мониторинг сервера приложений 1с в Linux

Мониторинг выполняется с помощью консольных утилит ras и rac через UserParameters (см. пример в `onec-srv-lin.conf`)

ras - должен быть запущен всегда. Пример Unit для Linux https://gist.github.com/Kirill/aedc342cca286bf8d45f45d267f7678a

rac - обращается к ras за запрошенными данными

Шаблон для импорта: srv-1c-zabbix-temlate.xml

В шаблоне 3 макроса:
- ```{$ONEC.ARCH.TYPE}``` - определение разрядности установленого сервера приложения 1С. Не путать с разрядностью самой Операционной системы.
  - ```x86_64``` - для Linux; 1С x64 - файл rac расположен /opt/1C/v8.3/x86_64/
  - ```i386``` - для Linux; 1С x32 - файл rac расположен /opt/1C/v8.3/i386/
  - '' - (пустая строка, без каычек) для Windows; 1С x64 - файл rac расположен c:\program files\1cv8\8.3.x.y\bin\rac
  - ' (x86)' - (без кавычек, перед открывающейся скобкой обязательно пробел) для Windows; 1С x32 - файл rac расположен c:\program files (x86)\1cv8\8.3.x.y\bin\rac
- ```{$ONEC.VERSION}``` - версия установленной 1С Предприятие на сервере. Требуется только для Windows.
- ```{$ONEC.CLUSTER.ID}``` - UUID кластера на сервере 1С Предприятие. Можно получить запустив на сервере команду ```rac cluster list```

Реализовано пять параметров и один график


## Для windows

Установка службы

    sc create "1C:Enterprise RAS" binpath= "C:\Program Files\1cv8\Х.Х.Х.ХХХХ\bin\ras.exe cluster --service" displayname= "1C:Enterprise RAS" start= auto 
    net start "1C:Enterprise RAS"

Удаление службы

    sc delete "1C:Enterprise RAS"

настройка zabbix agent

    UserParameter=onec-session,"C:\Program Files\1cv8\8.3.9.1850\bin\rac.exe" session --cluster=<uuid> list --infobase=<uuid> |  find /c "1CV8C"
    UserParameter=onec-bgj,"C:\Program Files\1cv8\8.3.9.1850\bin\rac.exe" session --cluster=<uuid> list --infobase=<uuid> | find /c "BackgroundJob"

Графики
![screen01](https://raw.githubusercontent.com/kirill/srv-1c-zabbix-template/master/images/screen-01.png)

TODO

1. Нужно правила обнаружения.
2. Подобрать подходящие (время выполнения/нагрузка на сервер) интервалы опроса параметров.
