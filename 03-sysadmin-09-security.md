#_Домашнее задание к занятию "3.9. Элементы безопасности информационных систем"_ #
##Выполнил  - Каплин Владимир ##



1. Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.

- регистрируемся

![Регистрация и сохранение пароля 1](Sec01.jpg)
- устанавливаем плагин в Chrome и сохраняем логин и пароль
![Регистрация и сохранение пароля 2](Sec02.jpg)

2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.
- Подключаем второй фактов, активируем опцию в настройках
![Активация второго фактора 3](Sec03.jpg)
- Устанавливаем второй фактор на телефон
![Устанавливаем второй фактор 4](Sec04.jpg)
- Проверяем вход
![Активация второго фактора 5](Sec05.jpg)

Второй фактор работает.

3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.

- Устанавливаем Apache
```
vagrant@vagrant:~$ sudo apt install apache2
Reading package lists... Done
Building dependency tree
Reading state information... Done
apache2 is already the newest version (2.4.41-4ubuntu3.10).
The following packages were automatically installed and are no longer required:
  at-spi2-core libatk-bridge2.0-0 libatspi2.0-0 libcolord2 libdrm-amdgpu1 libdrm-intel1 libdrm-nouveau2 libdrm-radeon1 libepoxy0 libfile-basedir-perl libfile-desktopentry-perl libfile-mimeinfo-perl
  libfontenc1 libfwupdplugin1 libgbm1 libgl1 libgl1-mesa-dri libglapi-mesa libglvnd0 libglx-mesa0 libglx0 libgtk-3-0 libgtk-3-bin libgtk-3-common libice6 libio-stringy-perl libipc-system-simple-perl
  libllvm12 libnet-dbus-perl libpciaccess0 librest-0.7-0 libsm6 libsoup-gnome2.4-1 libtie-ixhash-perl libu2f-udev libvulkan1 libwayland-client0 libwayland-cursor0 libwayland-egl1 libwayland-server0
  libx11-protocol-perl libx11-xcb1 libxaw7 libxcb-dri2-0 libxcb-dri3-0 libxcb-glx0 libxcb-present0 libxcb-randr0 libxcb-shape0 libxcb-sync1 libxcb-xfixes0 libxft2 libxkbcommon0 libxkbfile1 libxml-parser-perl
  libxml-twig-perl libxml-xpathengine-perl libxmu6 libxmuu1 libxpm4 libxshmfence1 libxt6 libxtst6 libxv1 libxxf86dga1 libxxf86vm1 mesa-vulkan-drivers x11-common x11-utils x11-xserver-utils xdg-utils
Use 'sudo apt autoremove' to remove them.
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```
- Активируем службу 
```
sudo systemctl enable apache2
Synchronizing state of apache2.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable apache2
```
- Служба активирована
```
vagrant@vagrant:~$  sudo systemctl status apache2
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2022-03-27 15:30:30 UTC; 4min 53s ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 25532 (apache2)
      Tasks: 7 (limit: 2278)
     Memory: 24.4M
     CGroup: /system.slice/apache2.service
             ├─25532 /usr/sbin/apache2 -k start
             ├─25563 /usr/sbin/apache2 -k start
             ├─25564 /usr/sbin/apache2 -k start
             ├─25565 /usr/sbin/apache2 -k start
             ├─25566 /usr/sbin/apache2 -k start
             ├─25567 /usr/sbin/apache2 -k start
             └─26747 /usr/sbin/apache2 -k start

Mar 27 15:30:30 vagrant systemd[1]: Starting The Apache HTTP Server...
Mar 27 15:30:30 vagrant apachectl[25531]: AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this mes>
Mar 27 15:30:30 vagrant systemd[1]: Started The Apache HTTP Server.
```

- Создаем католог для тестового  сайта и тестовую страницу.

```
vagrant@vagrant:~$ cd /var/www/
vagrant@vagrant:/var/www$ sudo mkdir test-site
vagrant@vagrant:/var/www/test-site$ sudo touch index.html
vagrant@vagrant:/var/www/test-site$ sudo vi index.html
vagrant@vagrant:/var/www/test-site$ cat index.html
<html>
<head>
  <title> Ubuntu rocks! </title>
</head>
<body>
  <p> I'm running this test website using HTTPS!
</body>
</html>
```
- скопируем файл настроек для тестового сайта и отредактируем его
```
vagrant@vagrant:/etc/apache2/sites-available$ sudo cp default-ssl.conf test-ssl.conf
vagrant@vagrant:/etc/apache2/sites-available$ sudo vi test-ssl.conf
```

- активируем модуль ssl
sudo a2enmod ssl
- 
4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).

- установим средство 

```
vagrant@vagrant:~$ git clone --depth 1 https://github.com/drwetter/testssl.sh.git
Cloning into 'testssl.sh'...
remote: Enumerating objects: 100, done.
remote: Counting objects: 100% (100/100), done.
remote: Compressing objects: 100% (93/93), done.
remote: Total 100 (delta 13), reused 32 (delta 6), pack-reused 0
Receiving objects: 100% (100/100), 8.57 MiB | 851.00 KiB/s, done.
Resolving deltas: 100% (13/13), done.
```
- и проверим новостной сайт на проникновение с ключом -u

```
vagrant@vagrant:~/testssl.sh$ ./testssl.sh -U --sneaky https://www.eurointegration.com.ua/

###########################################################
    testssl.sh       3.1dev from https://testssl.sh/dev/
    (90c6134 2022-03-16 15:25:06 -- )

      This program is free software. Distribution and
             modification under GPLv2 permitted.
      USAGE w/o ANY WARRANTY. USE IT AT YOUR OWN RISK!

       Please file bugs @ https://testssl.sh/bugs/

###########################################################

 Using "OpenSSL 1.0.2-chacha (1.0.2k-dev)" [~183 ciphers]
 on vagrant:./bin/openssl.Linux.x86_64
 (built: "Jan 18 17:12:17 2019", platform: "linux-x86_64")


 Start 2022-03-27 20:24:22        -->> 130.211.29.18:443 (www.eurointegration.com.ua) <<--

 rDNS (130.211.29.18):   18.29.211.130.bc.googleusercontent.com.
 Service detected:       HTTP


 Testing vulnerabilities

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), no heartbeat extension
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK)
 ROBOT                                     Server does not support any cipher suites that use RSA key transport
 Secure Renegotiation (RFC 5746)           supported (OK)
 Secure Client-Initiated Renegotiation     not vulnerable (OK)
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
 BREACH (CVE-2013-3587)                    potentially NOT ok, "br" HTTP compression detected. - only supplied "/" tested
                                           Can be ignored for static pages or if no secrets in the page
 POODLE, SSL (CVE-2014-3566)               not vulnerable (OK)
 TLS_FALLBACK_SCSV (RFC 7507)              Downgrade attack prevention supported (OK)
 SWEET32 (CVE-2016-2183, CVE-2016-6329)    not vulnerable (OK)
 FREAK (CVE-2015-0204)                     not vulnerable (OK)
 DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                           no RSA certificate, thus certificate can't be used with SSLv2 elsewhere
 LOGJAM (CVE-2015-4000), experimental      not vulnerable (OK): no DH EXPORT ciphers, no DH key detected with <= TLS 1.2
 BEAST (CVE-2011-3389)                     TLS1: ECDHE-ECDSA-AES128-SHA ECDHE-ECDSA-AES256-SHA
                                           VULNERABLE -- but also supports higher protocols  TLSv1.1 TLSv1.2 (likely mitigated)
 LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS. Check patches
 Winshock (CVE-2014-6321), experimental    not vulnerable (OK)
 RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


 Done 2022-03-27 20:24:48 [  31s] -->> 130.211.29.18:443 (www.eurointegration.com.ua) <<--```

```
5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.

- Установим и запустим SSH сервер
```
vagrant@vagrant:/usr/share/doc/apache2$ sudo apt install openssh-server
vagrant@vagrant:/usr/share/doc/apache2$ sudo systemctl enable sshd
vagrant@vagrant:/usr/share/doc/apache2$ sudo systemctl status sshd
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2022-03-27 15:30:28 UTC; 1 day 3h ago
       Docs: man:sshd(8)
             man:sshd_config(5)
   Main PID: 25414 (sshd)
      Tasks: 1 (limit: 2278)
     Memory: 6.3M
     CGroup: /system.slice/ssh.service
             └─25414 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups

Mar 27 15:30:28 vagrant systemd[1]: Starting OpenBSD Secure Shell server...
Mar 27 15:30:28 vagrant sshd[25414]: Server listening on 0.0.0.0 port 22.
Mar 27 15:30:28 vagrant sshd[25414]: Server listening on :: port 22.
Mar 27 15:30:28 vagrant systemd[1]: Started OpenBSD Secure Shell server.
Mar 27 18:45:34 vagrant sshd[50662]: Accepted publickey for vagrant from 10.0.2.2 port 54129 ssh2: RSA SHA256:nNgKDDftYRRTDnkKM2SQC6t8xRdpiUn1djVnn793m1s
Mar 27 18:45:34 vagrant sshd[50662]: pam_unix(sshd:session): session opened for user vagrant by (uid=0)
Mar 27 20:11:05 vagrant sshd[51200]: Accepted publickey for vagrant from 10.0.2.2 port 56373 ssh2: RSA SHA256:nNgKDDftYRRTDnkKM2SQC6t8xRdpiUn1djVnn793m1s
Mar 27 20:11:05 vagrant sshd[51200]: pam_unix(sshd:session): session opened for user vagrant by (uid=0)
```

- сгенерируем ключ

vagrant@vagrant:~$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa): kapli
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in kapli
Your public key has been saved in kapli.pub
The key fingerprint is:
SHA256:fnpKrCcOeJg9PgqCkPa4OJw6BHwNqK3QNii72IGv56g vagrant@vagrant
The key's randomart image is:
+---[RSA 3072]----+
|  .              |
| . .             |
|o+  o            |
|*o=. .           |
|**..    S        |
|B.+=   o         |
|B==o=   + .      |
|B==o.o.o.o.      |
|EBo..ooooo       |
+----[SHA256]-----+

отправим ключ на физический хост
