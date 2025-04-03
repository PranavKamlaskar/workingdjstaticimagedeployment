follow https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu#step-3-creating-a-python-virtual-environment-for-your-project link 
https://chatgpt.com/share/67ee54d8-90f4-8005-89a8-1f66bd524ea1
https://chatgpt.com/share/67ee552c-4638-8005-9191-c593099f0438

Most important files are gunicorn.service ,nginx config file ,follow them carefully , 
for example in nginx config file 
    location /static/ {
        root /home/ubuntu/django-course/lesson07/myproject;
    }
while writing this file make sure python manage.py collectstatic is already run 


in settings.py check
ALLOWED_HOSTS = ['*']

INSTALLED_APPS = [
    'whitenoise.runserver_nostatic',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'posts'
]

MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "whitenoise.middleware.WhiteNoiseMiddleware",

    MEDIA_URL = 'media/'

MEDIA_ROOT = os.path.join(BASE_DIR, 'media')


STATIC_ROOT = os.path.join(BASE_DIR, 'statics/')



in myproject/urls.py check 

urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)


sudo apt update
sudo apt install python3-venv python3-dev libpq-dev nginx curl -y

mkdir ~/myprojectdir
cd ~/myprojectdir

python3 -m venv myprojectenv

source myprojectenv/bin/activate


pip install django gunicorn psycopg2-binary Pillow Whitenoise

django-admin startproject myproject ~/myprojectdir

nano ~/myprojectdir/myproject/settings.py>>>  follow settings.py file from this repo 


~/myprojectdir/manage.py collectstatic


~/myprojectdir/manage.py runserver 0.0.0.0:8000  to run devserver 

In your web browser, visit your server’s domain name or IP address followed by :8000:

http://server_domain_or_IP:8000
'

cd ~/myprojectdir
gunicorn --bind 0.0.0.0:8000 myproject.wsgi


deactivate


sudo nano /etc/systemd/system/gunicorn.socket>>>>>>> follow backup/socket


sudo nano /etc/systemd/system/gunicorn.service>>>>>>>> follow backup/service   



sudo systemctl start gunicorn.socket
sudo systemctl enable gunicorn.socket


file /run/gunicorn.sock


curl --unix-socket /run/gunicorn.sock localhost





sudo nano /etc/nginx/sites-available/myproject>>>>>>> follow backup/myproject


sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled

sudo nginx -t



sudo chmod +x /home/ubuntu
sudo chmod +x /home/ubuntu/myprojectdir
(you were stucck here for so long because you did not resolve the permission issue regarding nginx)

sudo systemctl daemon-reload
sudo systemctl restart gunicorn
sudo systemctl restart nginx


