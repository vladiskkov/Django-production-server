# Django-production-server
Django project with production server

#### First need to make an upgrade:
```
apt-get update && apt-get upgrade -y
```

#### Install required software package:
```
apt-get install python3-pip -y    # Install pip
apt-get install python3-venv y    # Install virtual environment
apt-get install nginx -y          # Install nginx
```

#### Install virtual environment:
```
python3 -m venv /opt/myenv        # Creation virtual environment
source myenv/bin/activate         # Activation virtual environment
```

#### Install required software package in virtual environment:
```
pip3 install --upgrade pip        # Update pip
pip install Django                # Install Django
pip install gunicorn              # Install Gunicorn
```


