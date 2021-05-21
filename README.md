### Для перехода на сайт воспользуйтесь *[ссылкой](http://92.63.106.66)*
-----------------------------------------------------------------------
  
## ***ergo.ini***
```
[uwsgi]
project = ergo  
uid = dimoka  
base = /home/%(uid)  
  
chdir = %(base)/%(project)  
home = %(base)/%(project)/env  
module = %(project).wsgi:application  
  
master = true  
processes = 5  
  
socket = /run/uwsgi/%(project).sock  
chown-socket = %(uid):www-data  
chmod-socket = 666   
vacuum = true  
```
## ***ergo.service***
```
[Unit]  
Description=ergo site  
  
[Service]  
ExecStartPre=/bin/bash -c 'mkdir -p /run/uwsgi; chown dimoka:www-data /run/uwsgi'  
ExecStart=/usr/local/bin/uwsgi --emperor /etc/uwsgi/sites  
Restart=always  
KillSignal=SIGQUIT  
Type=notify  
NotifyAccess=all  
  
[Install]  
WantedBy=multi-user.target 
```

## ***ergo nginx***
```
server {  
    listen 80;  
    server_name 92.63.106.66;  
  
    location = /favicon.ico { access_log off; log_not_found off; }  
    location /static/ {  
        root /home/dimoka/ergo;  
    }  
  
    location / {  
        include         uwsgi_params;  
        uwsgi_pass      unix:/run/uwsgi/ergo.sock;  
    }  
}  
```
