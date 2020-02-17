## Настройка fail2ban для добавления "плохих клиентов" в ipset для бана на определенное время.

### Базовая настройка
Основной конфигурационный файл находится по пути `/etc/fail2ban/jail.local`

Настройки находятся в секции `[nginx-req-limit]`:

    [nginx-req-limit]
    enabled = true
    ignoreip = 127.0.0.1/8 10.0.0.0/8
    usedns = no
    backend = pyinotify
    filter = nginx-req-limit
    action = iptables-ipset-proto4[name=fuckers, port="http,https"]
    logpath = /var/log/nginx/error.log
    findtime = 60
    bantime = 600
    maxretry = 10
    logencoding = ascii

#### где:
- `ignoreip` - игнорировать защиту, если запросы приходят с перечисленных адресов;
- `action` -  действия, которое будет выполняться, если fail2ban обнаружит активность, соответствующую критериям поиска;
- `maxretry` -  количество действий, которые разрешено совершить до бана.
- `findtime` -  время в секундах, в течение которого учитывается `maxretry`;
- `bantime` - время, на которое будет блокироваться IP-адрес;

В нашей конфигурации если в течении одной минуты (60) будет найдено 11 строк (10), содержащих критерии фильтра, fail2ban создаст в iptables правило с именем fuckers и заблокирует доступ для IP-адреса, с которого идет подключение, на 10 минут (600) на порты http и https.
