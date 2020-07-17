# Django-production-server
Django project with production server

### First need to make an upgrade
```
apt-get update && apt-get upgrade -y
```

### Install required software package
```
apt-get install python3-pip -y          # Install pip
apt-get install python3-venv y          # Install virtual environment
apt-get install nginx -y                # Install nginx
apt-get install supervisor -y
```

### Install virtual environment
```
python3 -m venv /opt/myenv              # Creation virtual environment
source opt/myenv/bin/activate           # Activation virtual environment
```

### Install required software package in virtual environment
```
pip3 install --upgrade pip              # Update pip
pip install Django                      # Install Django
pip install gunicorn                    # Install Gunicorn
```

### Creating Django project
```
source opt/myenv/bin/activate           # Activation virtual environment
cd /opt/myenv
django-admin.py startproject myproject  # Creatin Django project with name - -myproject
cd /opt/myenv/myproject/myproject                          # Go to the project folder
nano settings.py
    ALLOWED_HOSTS = ['ip_server']
```

### Gunicorn test
Start gunicorn from folder where manage.py:
```
cd /opt/myenv/myproject
gunicorn myproject.wsgi:application --bind ['ip_server']:8000
```
Now in your browser type ['ip_server']:8000
Should appear Django startup page "It worked!"

### Settings Django static files
```
cd /opt/myenv/myproject/myproject 
nano settings.py
    STATIC_ROOT = 'static'
    STATIC_URL = '/static/'
cd /opt/myenv/myproject
python manage.py collectstatic
```

### Settings nginx
Config file for nginx:
```
cd /etc/nginx/sites-available/
nano default
    server {
    listen 80;
    server_name ['ip_server'];
    access_log  /var/log/nginx/example.log;

    location /static/ {
        root /opt/myenv/myproject/;
        expires 30d;
    }

    location / {
        proxy_pass http://127.0.0.1:8000; 
        proxy_set_header Host $server_name;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
  }
```
Rights changes for db file:
```
chown root:root /opt/myenv/myproject/db.sqlite3
chmod ugo+rw- /opt/myenv/myproject/db.sqlite3
chmod o+w /opt/myenv/myproject
```

### Testing production server
```
service nginx restart
cd /opt/myenv/myproject
source opt/myenv/bin/activate
gunicorn myproject.wsgi:application
```
In your browser type ['ip_server']:8000
Should appear Django startup page "It worked!"

### Settings supervisor
So that our application starts after any unexpected reboot or crash
Creating config file for gunicorn:
```
cd /opt/myenv/myproject/myproject
touch gunicorn.conf.py
nano gunicorn.conf.py
    bind = '127.0.0.1:8000'
    workers = 3
    user = "nobody"
```
Creating config file for supervisor:
```
cd /etc/supervisor/conf.d/
touch myproject.conf
nano myproject.conf
    [program:myproject]
    command=/opt/myenv/bin/gunicorn myproject.wsgi:application -c /opt/myenv/myproject/myproject/gunicorn.conf.py
    directory=/opt/myenv/myproject
    user=nobody
    autorestart=true
    redirect_stderr=true
```
Start supervisor:
```
supervisorctl reread
supervisorctl update
supervisorctl status myproject
supervisorctl restart myproject
```
In your browser type ['ip_server']
Should appear Django startup page "It worked!"
