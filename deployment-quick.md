# Deployment Herokura 6 lépésben
Feltesszük, hogy a git és a python már installálva van. 

Általában mindenhol igaz, hogy a python-t terminálból a "py" paranccsal érjük el windowsból, és "python" paranccsal érjük el linuxból.

## 1. pip, virtualenv, Django frissítése/telepítése:
```sh
py -m pip install --upgrade pip
py -m pip install virtualenv
py -m pip install -U Django
```
## 2. GIT és VENV: 
1. Regisztrálj GitHub-ra: https://github.com/
2. Csináljuk egy új ``REPONEVE`` GitHub repót
	1. add .gitignore listből válasszuk ki a Python-t.
	2. init with README.md
	3. Másold ki vágólapra a git repo címét, amit a github repód Code-gombjára kattintva találsz meg, és valahogy így néz ki: 
	    ```
	    https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git
	    ```
3. Válassz egy ``GYÖKÉR`` könyvtárat, és ott:
	```sh
	git clone https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git
	virtualenv -p python3 VENV
	.\VENV\Scripts\activate
	py -m pip install --upgrade pip
	pip install django
	pip install gunicorn
	pip install dj-database-url
	pip install whitenoise
	cd REPONEVE
	``` 
	a virtuális környezet aktiválása Linuxon más:
	```sh
	source VENV/bin/activate
	```
## 3. Projekt-és adatbázisbeállítások, statikus fájlok kiszolgálása herokun
```sh
django-admin startproject PROJEKT
mv PROJEKT/*.* ./
mv PROJEKT/PROJEKT/* PROJEKT/
py manage.py migrate
py manage.py runserver
git add .
git commit -m "Django-projekt létrehozása"
git push origin main
py manage.py migrate
py manage.py createsuperuser
code .\PROJEKT\settings.py
```
	
1. a whitenoise-t a MIDDLEWARE-ben meghívni:
	```py
	MIDDLEWARE = [
	    'django.middleware.security.SecurityMiddleware',
	    'whitenoise.middleware.WhiteNoiseMiddleware',
	    'django.contrib.sessions.middleware.SessionMiddleware',
	    'django.middleware.common.CommonMiddleware',
	    'django.middleware.csrf.CsrfViewMiddleware',
	    'django.contrib.auth.middleware.AuthenticationMiddleware',
	    'django.contrib.messages.middleware.MessageMiddleware',
	    'django.middleware.clickjacking.XFrameOptionsMiddleware',
	]
	```

2. statikus fájlokhoz a végére:
	```py
	# Heroku: Update database configuration from $DATABASE_URL.
	import dj_database_url
	db_from_env = dj_database_url.config(conn_max_age=500)
	DATABASES['default'].update(db_from_env)

	# INNEN szedegeti össze azokat a statikus fájlokat, amelyek nem tartoznak egyetlen apphoz sem:
	STATICFILES_DIRS = [
	    BASE_DIR / 'static'
	]

	# IDE fogja collectelni a collectstatic
	STATIC_ROOT = BASE_DIR / 'staticfiles'  
	#régebbi django-hoz: 
	#STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

	# ITT fogja észlelni a böngésző
	STATIC_URL = '/static/'

	# itt a whitenoise alkalmazása
	STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
	```

3. TESZT & MENTÉS
	```sh
	py manage.py runserver
	git add .
	git commit -m "adatbázissal kapcsolatos beállítások a settings.py-ban"
	git push origin main
	```
## 4. APP
```sh
django-admin startapp APP
code .\PROJEKT\settings.py

mkdir APP/templates
echo "hello vilag" > .\APP\templates\ez.html
code .\APP\templates\ez.html
code .\APP\views.py
code .\PROJEKT\urls.py
```

Az ez.html kódolását váltsd át utf8-ra

A ``settings.py``-ba
```py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'APP',
]
```

A ``views.py``-ba
```py
def VIEW(request):
    return render(request, "ez.html", {})
```

Az ``urls.py``-ba
```py
from django.contrib import admin
from django.urls import path
from APP.views import VIEW
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', VIEW),
]
```

``GYÖKÉR/REPONEVE/``: TESZT & MENTÉS
```sh
py manage.py runserver
git add .
git commit -m "App és view létrehozása."
git push origin main
```
## 5. Heroku-fájlok
1. A https://devcenter.heroku.com/articles/python-support#supported-python-runtimes -n nézz utána, hogy milyen python verziót lehet használni, pl. 3.8.11.
2. És akkor:
	```sh
	echo "web: gunicorn PROJEKT.wsgi --log-file -" > Procfile
	echo python-3.8.11 > runtime.txt
	pip freeze > requirements.txt
	code Procfile
	code runtime.txt
	code requirements.txt
	```
1. A ``GYÖKÉR/REPONEVE/Procfile``-ban ne felejtsd el átírni a projektet!
2. A ``GYÖKÉR/REPONEVE/requirements.txt`` esetében legyenek ott ezek, esetleg más verziószámmal:
	```
	dj-database-url==0.5.0
	Django==3.1.7
	gunicorn==20.0.4
	psycopg2-binary==2.8.6
	whitenoise==5.2.0
	```

3. mindhárom fájlt konvertáld át utf8-ra!
	```sh
	py manage.py runserver
	git add .
	git commit -m "Heroku-fájlok: Procfile, requirements, runtime.txt"
	git push origin main
	```
## 6. Heroku
1. Regisztrálj Herokura: https://www.heroku.com/
2. Töltsd le és installáld a Heroku CLI-t: https://devcenter.heroku.com/articles/getting-started-with-python#set-up
3. 
	```sh
	heroku create HEROKUREMOTE
	code .\PROJEKT\settings.py
	mkdir static
	echo "bla" > static/nelegyenures.txt
	```

4. itt tudod tesztelni: http://HEROKUREMOTE.herokuapp.com/ 
5. Ezt írd át a ``settings.py``-ban: 
	```py
	# SECURITY WARNING: don't run with debug turned on in production!
	DEBUG = False

	ALLOWED_HOSTS = ['HEROKUREMOTE.herokuapp.com', '127.0.0.1']
	```
6.
	```sh
	git add .
	git commit -m "ALLOWED_HOSTS = ['HEROKUREMOTE.herokuapp.com', '127.0.0.1']"
	git push origin main
	git push heroku main
	heroku run python manage.py migrate
	heroku run python manage.py createsuperuser
	heroku open
	heroku logs --tail
	```
