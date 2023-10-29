### Liste der Befehle, für die Konfiguration von Paperless-NGX

Das Video dazu gibt's auf [YouTube](https://www.youtube.com/watch?v=o4pfsee78m8)

---

# Installation von Paperless-NGX auf einem Proxmox LXC Container

Link: [Paperless-NGX Installation](https://www.youtube.com/watch?v=Yd_TSlLZDgU)


---


## Zugriff für WinSCP aktivieren
```sh
nano /etc/ssh/sshd_config
```
Folgende Befehlszeile bei dem Punke "Authentication" ergänzen
PermitRootLogin yes

Anschließend "ssh" neu starten
```sh
/etc/init.d/ssh restart
```

## Docker-Compose.env Datei anpassen
```sh
cd /paperless/paperless-ngx/
nano docker-compose.env

PAPERLESS_CONSUMER_POLLING=5

docker-compose up -d
```

## FTP Server installieren und konfigurieren
```sh
apt install proftpd-basic -y

nano /etc/proftpd/conf.d/ftp.conf

<Global>
    RequireValidShell off
</Global>
# If desired turn off IPv6
UseIPv6 off
# Default directory is ftpusers home
DefaultRoot ~ ftpuser
# Limit login to the ftpuser group
<Limit LOGIN>
    DenyGroup !ftpuser
</Limit>

systemctl restart proftpd

addgroup ftpuser
usermod -aG ftpuser paperless
```

## OpenSSL installieren und konfigurieren für ftps
```sh
apt install openssl -y

openssl req -x509 -newkey rsa:2048 -keyout /etc/ssl/private/proftpd.key -out /etc/ssl/certs/proftpd.crt -nodes -days 1460

nano /etc/proftpd/conf.d/tls.conf

<IfModule mod_tls.c>
    TLSEngine on
    TLSLog /var/log/proftpd/tls.log
    TLSProtocol TLSv1.2
    TLSRSACertificateFile /etc/ssl/certs/proftpd.crt
    TLSRSACertificateKeyFile /etc/ssl/private/proftpd.key
    TLSVerifyClient off
    TLSOptions NoSessionReuseRequired
    TLSRequired on
</IfModule>

nano /etc/proftpd/modules.conf
```




