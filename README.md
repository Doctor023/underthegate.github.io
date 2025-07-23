Для кого эта статья?

Я старался описать максимально емкую инструкцию, чтобы человек далекий от IT смог самостоятельно установить VPN. Статья писалась на скорую руку, поэтому будет дорабатываться в процессе

Инструкция:

1. Арендуйте любой VPS сервер, буквально так и пишем в гугле, ищем любой хостинг, главное, чтобы сервер был не в России, ОС выбирайте ubuntu или debian.

2. Получите данные от ssh, это юзернейм (чаще всего root) и пароль

3.Подключитесь консолью по ssh к серверу. А именно вбейте WIN + R, введите "cmd", откройте консоль, подключитесь к серверу командой ssh username@IPсервера
Если пишет "Are you sure you want to continue connecting (yes/no/[fingerprint])?", просто введите yes

4. Далее вводим
```
 apt update -y

 bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install
```
5. Создаем необходимые для конфига данные
```
 xray uuid
 xray x25519
```

uuid - это условный аккаунт
xray x25519 - генерирует 2 ключа, приватный и публичный, просто запомним это

6. Пишем
```
nano /usr/local/etc/xray/config.json
```
Вставляем туда
```
{
"log": {
"loglevel": "debug",

"access": "/var/log/xray/access.log",
 "error": "/var/log/xray/error.log"
 },
 "inbounds": [
 {
 "tag": "proxy",
 "port": 443,
 "protocol": "vless",
 "settings": {
 "clients": [
 {
 "id": "ВАШ UUID",
 "flow": "xtls-rprx-vision"
 }
 ],
 "decryption": "none"
 },
 "streamSettings": {
 "network": "tcp",
 "security": "reality",
 "realitySettings": {
 "dest": "google.com:443",
 "serverNames": [
 "google.com"
 ],

"privateKey": "ВАШ ПРИВАТНЫЙ КЛЮЧ",
"shortIds": [
 "aabbccdd"
 ]
 }
 },
"sniffing": {
 "enabled": true,
 "destOverride": [
 "http",
 "tls",
 "quic"
 ],
 "routeOnly": true
 }
 }
 ],
 "outbounds": [
 {
 "protocol": "freedom",

"tag": "direct"
 }
 ]
 }
```
7. ctrl + O, - сохраняем документ,
Enter - подтверждаем,
ctrl + X - выходим из документа

8. Перезапускаем процесс
```
 systemctl restart xray
```

9. Смотрим статус программы
```
 systemctl status xray
```

Если Active: `active (running)`, значит все ок

Устанавливаем nekobox https://github.com/MatsuriDayo/nekoray/releases

Открываем nekobox, ПКМ по свободному полю и выбираем создать новый профиль
<img width="332" height="335" alt="image" src="https://github.com/user-attachments/assets/0e960bb2-827d-4468-a5b5-b17c6f097220" />


12. Заполняем по форме


Помните, что 1 профиль = одно подключение. Т.е. одновременно подключиться к нему может только 1 устройство, каждый профиль имеет уникальный UUID

13. Чтобы включить VPN устанавливаем 2 галочки


ПКМ по строке подключения и запускаем


14. Проверяем на 2ip.ru получили ли мы иностранный IP адрес, если видим иностранный, значит VPN работает

Как добавить несколько точек подключений?

Помним, что один uuid - это одна точка подключения, вы не можете сидеть с одного идентификатора с пк и с телефона, ровно так же, как и вы и ваш друг не сможете сидеть с двух пк по одному идентификатору одновременно

Допустим, нам нужно 3 точки подключения, генерируем 3 раза uuid командой xray uuid
Меняем конфиг в nano /usr/local/etc/xray/config.json
Меняем следующие значения:
```
"clients": [

{

"id": "ВАШ ID",

"flow": "xtls-rprx-vision"

}, // здесь запятая стоит
 {

"id": "ВАШ ID",

"flow": "xtls-rprx-vision"

}, // и тут стоит
 {

"id": "ВАШ ID",

"flow": "xtls-rprx-vision"

} // тут запятой нет, т.к. это последний элемент
```
После внесения изменений в конфиг обязательно перезагружаем xray командой 
```
systemctl restart xray
```
Смотрим 
```
systemctl restart xray
```
Если Active: `active (running)`, значит все ок
