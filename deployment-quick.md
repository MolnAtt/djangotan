# Deployment Herokura 17 lépésben
Feltesszük, hogy a git és a python már installálva van. 

Általában mindenhol igaz, hogy a python-t terminálból a "py" paranccsal érjük el windowsból, és "python" paranccsal érjük el linuxból.

1. CMD: pip, virtualenv, Django frissítése/telepítése:
	```sh
	py -m pip install --upgrade pip
	py -m pip install virtualenv
	py -m pip install -U Django
	```
2. BROWSER - GITHUB: Regisztrálj GitHub-ra: https://github.com/
3. BROWSER - GITHUB: Csináljuk egy új ``REPONEVE`` GitHub repót
    1. add .gitignore listből válasszuk ki a Python-t.
    2. init with README.md
    3. Másold ki vágólapra a git repo címét, amit a github repód Code-gombjára kattintva találsz meg, és valahogy így néz ki: 
	    ```
	    https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git
	    ```
4. CMD vagy INTÉZŐ: ``GYÖKÉR`` könyvtár létrehozása
5. CMD: ``GYÖKÉR/``: 
	```sh
	git clone https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git
	virtualenv -p python3 VENV
	.\VENV\Scripts\activate
	``` 
	vagy utóbbi Linuxon  
	```sh
	source VENV/bin/activate
	```
10. CMD: telepítések a VENV-re  
	```sh
	py -m pip install --upgrade pip
	pip install django
	pip install gunicorn
	pip install dj-database-url
	pip install whitenoise
	```
14. CMD:  ``GYÖKÉR/REPONEVE/`` Projekt létrehozása és elrendezése (egy szinttel visszahúzzuk a git repo szintjére)
	```sh
	django-admin startproject PROJEKT
	mv PROJEKT/*.* ./
	mv PROJEKT/PROJEKT/* PROJEKT/
	py manage.py migrate
	py manage.py runserver
	git add .
	git commit -m "Django-projekt létrehozása"
	git push origin main
	```
18. CMD: ``GYÖKÉR/REPONEVE/``:  
	```sh
	py manage.py migrate
	py manage.py createsuperuser
	```
20. CMD: ``GYÖKÉR/REPONEVE/``:  
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
27. VSCODE: ``GYÖKÉR/REPONEVE/PROJEKT/settings.py``: 
	```sh
	code .\PROJEKT\settings.py
	```
	
	a végére:
	```py
	# Heroku: Update database configuration from $DATABASE_URL.
	import dj_database_url
	db_from_env = dj_database_url.config(conn_max_age=500)
	DATABASES['default'].update(db_from_env)
	```
	
	a whitenoise-t a MIDDLEWARE-ben meghívni:
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
28. VSCODE: ``GYÖKÉR/REPONEVE/PROJEKT/settings.py``: 
	```py
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

	``GYÖKÉR/REPONEVE/``: TESZT & MENTÉS
	```sh
	py manage.py runserver
	git add .
	git commit -m "adatbázissal kapcsolatos beállítások a settings.py-ban"
	git push origin main
	```
32. CMD: ``GYÖKÉR/REPONEVE/``: A https://devcenter.heroku.com/articles/python-support#supported-python-runtimes -n nézz utána, hogy milyen python verziót lehet használni, pl. 3.8.11. És akkor:
	```sh
	echo "web: gunicorn PROJEKT.wsgi --log-file -" > Procfile
	echo python-3.8.8 > runtime.txt
	pip freeze > requirements.txt
	code Procfile
	code runtime.txt
	code requirements.txt
	```
	A ``GYÖKÉR/REPONEVE/Procfile``-ban ne felejtsd el átírni a projektet!
	A ``GYÖKÉR/REPONEVE/requirements.txt`` esetében legyenek ott ezek, esetleg más verziószámmal:
	```
	dj-database-url==0.5.0
	Django==3.1.7
	gunicorn==20.0.4
	psycopg2-binary==2.8.6
	whitenoise==5.2.0
	```
	
	mindhárom fájlt konvertáld át utf8-ra!
	
	``GYÖKÉR/REPONEVE/``: TESZT & MENTÉS
	```sh
	py manage.py runserver
	git add .
	git commit -m "Heroku-fájlok: Procfile, requirements, runtime.txt"
	git push origin main
	```
40. BROWSER - HEROKU: 
	Regisztrálj Herokura: https://www.heroku.com/

	Töltsd le és installáld a Heroku CLI-t: https://devcenter.heroku.com/articles/getting-started-with-python#set-up
43. CMD: ``GYÖKÉR/REPONEVE/``: 
	```sh
	heroku create HEROKUREMOTE
	```
	itt tudod tesztelni: http://HEROKUREMOTE.herokuapp.com/ 
45. VSCODE: ``GYÖKÉR/REPONEVE/PROJEKT/settings.py``: 
	```py
	# SECURITY WARNING: don't run with debug turned on in production!
	DEBUG = False

	ALLOWED_HOSTS = ['HEROKUREMOTE.herokuapp.com', '127.0.0.1']
	```
45. CMD: ``GYÖKÉR/REPONEVE/``: static mappa és MENTÉS és PUSH GitHubra és Herokura is!
	```sh
	mkdir static
	echo "bla" > static/nelegyenures.txt
	git add .
	git commit -m "ALLOWED_HOSTS = ['HEROKUREMOTE.herokuapp.com', '127.0.0.1']"
	git push origin main
	git push heroku main
	``` 
47. CMD: ``GYÖKÉR/REPONEVE/`` 
	```sh
	heroku run python manage.py migrate
	heroku run python manage.py createsuperuser
	heroku open
	heroku logs --tail
	```
