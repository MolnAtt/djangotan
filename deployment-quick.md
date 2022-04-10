# Saját honlap Herokun négynél több lépésben.
Feltesszük, hogy a git és a python már installálva van. 

Általában mindenhol igaz, hogy a python-t terminálból a "py" paranccsal érjük el windowsból, és "python" paranccsal érjük el linuxból.

## 0. Regisztráció
1. Regisztrálj Herokura: https://www.heroku.com/
1. Regisztrálj GitHub-ra: https://github.com/


## 1. TELEPÍTÉS
1. 
	```sh
	py -m pip install --user --upgrade pip
	py -m pip install --user virtualenv
	py -m pip install --user -U Django
	```
2. Töltsd le és installáld a Heroku CLI-t: https://devcenter.heroku.com/articles/getting-started-with-python#set-up
3. HEROKUREMOTE az oldalad neve lesz. Itt derül ki, hogy foglalt-e.
	```sh
	heroku create HEROKUREMOTE
	```
4. Itt tudod tesztelni: http://HEROKUREMOTE.herokuapp.com/ 

## 2. GITHUB 
	1. A github.com-on csináljunk egy új ``REPONEVE`` GitHub repót
	2. add .gitignore listből válasszuk ki a Python-t.
	3. init with README.md
	4. Másold ki vágólapra a git repo címét, amit a github repód Code-gombjára kattintva találsz meg, és valahogy így néz ki: 
	    ```
	    https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git
	5. Ahova ezt írod, ott lesz a helyi repo-d.
	```sh
	git clone https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git
	```

## 3. VENV 
3. Ugyanitt, tehát a repo-n kívül: 
	```sh
	py -m virtualenv -p python3 VENV
	.\VENV\Scripts\activate
	py -m pip install --upgrade pip
	py -m pip install django
	py -m pip install gunicorn
	py -m pip install dj-database-url
	py -m pip install whitenoise
	py -m pip install django-rest-framework
	cd REPONEVE
	``` 
	a virtuális környezet aktiválása Linuxon más:
	```sh
	source VENV/bin/activate
	```
## 4. PROJEKT
1. A https://devcenter.heroku.com/articles/python-support#supported-python-runtimes -n nézz utána, hogy milyen python verziót lehet használni, pl. 3.8.11. Ez kell majd a következő parancsok közepe környékén.
2. 
	```sh
	django-admin startproject PROJEKT
	mv PROJEKT/*.* ./
	mv PROJEKT/PROJEKT/* PROJEKT/

	mkdir static
	echo "bla" > static/nelegyenures.txt

	echo "web: gunicorn PROJEKT.wsgi --log-file -" > Procfile
	echo python-3.8.11 > runtime.txt
	pip freeze > requirements.txt
	code .
	```
	
3. a whitenoise-t a MIDDLEWARE-ben meghívni:
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

4. Hosztok engedélyezésénél HEROKUREMOTE név helyett azt írd, amire majd a 
	```py
	# SECURITY WARNING: don't run with debug turned on in production!
	DEBUG = True

	ALLOWED_HOSTS = ['HEROKUREMOTE.herokuapp.com', '127.0.0.1']
	```

5. statikus fájlokhoz a végére:
	```py
	# Heroku: Update database configuration from $DATABASE_URL.
	import dj_database_url
	db_from_env = dj_database_url.config(conn_max_age=500)
	DATABASES['default'].update(db_from_env)

	# IDE fogja collectelni a collectstatic
	STATIC_ROOT = BASE_DIR / 'static'  
	#régebbi django-hoz: 
	#STATIC_ROOT = os.path.join(BASE_DIR, 'static')

	# ITT fogja észlelni a böngésző
	STATIC_URL = '/static/'

	# itt a whitenoise alkalmazása
	STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
	```
6. A ``GYÖKÉR/REPONEVE/Procfile``-ban ne felejtsd el átírni a projektet!
7. A ``GYÖKÉR/REPONEVE/requirements.txt`` esetében legyenek ott ezek, esetleg más verziószámmal:
	```
	dj-database-url==0.5.0
	Django==3.1.7
	gunicorn==20.0.4
	psycopg2-binary==2.8.6
	whitenoise==5.2.0
	```

8. mindhárom fájlt konvertáld át utf8-ra!
	```sh
	git add .
	git commit -m "PROJEKT (Heroku-kompatibilis)"
	git remote add heroku "https://git.heroku.com/HEROKUREMOTE.git"
	git push origin main
	git push heroku main
	heroku run python manage.py migrate
	heroku run python manage.py createsuperuser
	```
9. 	```sh
	heroku open
	```


## 4+1. APP 
1.
	```sh
	django-admin startapp APP
	mkdir APP/templates
	mkdir APP/static

	code .\APP\templates\teszt.html
	code .\APP\static\teszt.css
	code .\APP\views.py
	code .\PROJEKT\urls.py
	code .\PROJEKT\settings.py
	```
2. A ``teszt.html``-nek legyen ez a tartalma:
	```html
	{% load static %}
	<!DOCTYPE html>
	<head>
	    <link rel="stylesheet" href="{% static 'teszt.css' %}">
	</head>
	<body>
	    <h1>Ha ez zöld, </h1>
	    akkor működik a statikus fájlok kiszolgálása.
	</body>
	</html>
	```
3. A ``teszt.css``-nek legyen ez a tartalma
	```css
	h1{ color:green; }
	```
4.  A ``views.py``-ba
	```py
	def VIEW(request):
	    return render(request, "teszt.html", {})
	```
5. Az ``urls.py``-ba
	```py
	from django.contrib import admin
	from django.urls import path
	from APP.views import VIEW
	urlpatterns = [
	    path('admin/', admin.site.urls),
	    path('', VIEW),
	]
	```
6. A ``settings.py``-ba
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
7. 
	```sh
	git add .
	git commit -m "APP (+template+css)."
	git remote add heroku "https://git.heoku.com/HEROKUREMOTE.git"
	git push origin main
	git push heroku main
	heroku open
	heroku logs --tail
	```
