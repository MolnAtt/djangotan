# Deployment Herokura 42 lépésben
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
	```
8. CMD: ``GYÖKÉR/``:  
	```sh
	virtualenv -p python3 VENV
	```
9. CMD: ``GYÖKÉR/``: 
	```sh
	.\VENV\Scripts\activate
	``` 
	vagy linuxon  
	```sh
	source VENV/bin/activate
	```
10. CMD: telepítések a VENV-re  
	```sh
	pip install django
	pip install gunicorn
	pip install dj-database-url
	pip install whitenoise
	```
14. CMD:  ``GYÖKÉR/REPONEVE/``
	```sh
	django-admin startproject PROJEKT
	```
15. CMD:  ``GYÖKÉR/REPONEVE/``
	```sh
	mv PROJEKT/PROJEKT/* PROJEKT/
	```
17. CMD: ``GYÖKÉR/REPONEVE/``: TESZT & MENTÉS
	```sh
	py manage.py runserver
	git add .
	git commit -m "Django-projekt létrehozása"
	git push origin main
	```
18. CMD: ``GYÖKÉR/REPONEVE/``:  
	```sh
	py manage.py migrate
	```
19. CMD: ``GYÖKÉR/REPONEVE/``:  
	```sh
	py manage.py createsuperuser
	```
20. CMD: ``GYÖKÉR/REPONEVE/``:  
	```sh
	django-admin startapp APP
	```
21. VSCODE: ``GYÖKÉR/REPONEVE/PROJEKT/settings.py/``: 
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
22. CMD: ``GYÖKÉR/REPONEVE/APP/templates/``: (könyvtár létrehozása)
23. CMD: ``GYÖKÉR/REPONEVE/APP/templates/EZ.html``: (teszthtml fájl létrehozása)
24. VSCODE: ``GYÖKÉR/REPONEVE/APP/views.py``:
	```py
	def VIEW(request):
	    return render(request, "EZ.html", {})
	```
25. VSCODE: ``GYÖKÉR/REPONEVE/PROJEKT/urls.py``: 
	```py
	from django.contrib import admin
	from django.urls import path
	from APP.views import VIEW
	urlpatterns = [
	    path('admin/', admin.site.urls),
	    path('', VIEW),
	]
	```
17. CMD: ``GYÖKÉR/REPONEVE/``: TESZT & MENTÉS
	```sh
	py manage.py runserver
	git add .
	git commit -m "App és view létrehozása."
	git push origin main
	```
27. VSCODE: ``GYÖKÉR/REPONEVE/PROJEKT/settings.py``: 
	```py
	# Heroku: Update database configuration from $DATABASE_URL.
	import dj_database_url
	db_from_env = dj_database_url.config(conn_max_age=500)
	DATABASES['default'].update(db_from_env)
	```
29. VSCODE: ``GYÖKÉR/REPONEVE/PROJEKT/settings.py``: a whitenoise-t a MIDDLEWARE-ben meghívni:
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
17. CMD: ``GYÖKÉR/REPONEVE/``: TESZT & MENTÉS
	```sh
	py manage.py runserver
	git add .
	git commit -m "adatbázissal kapcsolatos beállítások a settings.py-ban"
	git push origin main
	```
32. CMD: ``GYÖKÉR/REPONEVE/``: 
	```sh
	echo "web: gunicorn PROJEKT.wsgi --log-file" - > Procfile
	```
33. NOTEPAD++: ``GYÖKÉR/REPONEVE/Procfile``: kódolást áttenni UTF8-ra
34. CMD: ``GYÖKÉR/REPONEVE/``: 
	```sh
	pip freeze > requirements.txt
	```
35. NOTEPAD++: ``GYÖKÉR/REPONEVE/requirements.txt``: kódolást áttenni UTF8-ra
36. NOTEPAD++: ``GYÖKÉR/REPONEVE/requirements.txt``: Legyenek ott ezek, esetleg más verziószámmal:
	```dj-database-url==0.5.0
	Django==3.1.7
	gunicorn==20.0.4
	psycopg2-binary==2.8.6
	whitenoise==5.2.0
	```
37. CMD: ``GYÖKÉR/REPONEVE/runtime.txt``: https://devcenter.heroku.com/articles/python-support#supported-python-runtimes -n nézz utána, hogy milyen python verziót lehet ideírni. Pl.:
	```sh
	echo python-3.8.8 > runtime.txt
	```
38. NOTEPAD++: ``GYÖKÉR/REPONEVE/runtime.txt``: kódolást tedd át UTF8-ra
17. CMD: ``GYÖKÉR/REPONEVE/``: TESZT & MENTÉS
	```sh
	py manage.py runserver
	git add .
	git commit -m "Heroku-fájlok: Procfile, requirements, runtime.txt"
	git push origin main
	```
40. BROWSER - HEROKU: Regisztrálj Herokura: https://www.heroku.com/
41. BROWSER - HEROKU: Töltsd le és installáld a Heroku CLI-t: https://devcenter.heroku.com/articles/getting-started-with-python#set-up
42. CMD: ``GYÖKÉR/REPONEVE/``: 
	```sh
	heroku create HEROKUREMOTE
	```
43. BROWSER - HEROKUREMOTE: Teszteld le, hogy sikerült-e:  http://HEROKUREMOTE.herokuapp.com/ 

44. CMD: ``GYÖKÉR/REPONEVE/``: Teszteld le, hogy látja-e a git a herokun lévő remote-ot!
	```sh
	git remote -v
	```

45. VSCODE: ``GYÖKÉR/REPONEVE/PROJEKT/settings.py``: 
	```py
	# SECURITY WARNING: don't run with debug turned on in production!
	DEBUG = False

	ALLOWED_HOSTS = ['HEROKUREMOTE.herokuapp.com', '127.0.0.1']
	```

46. CMD: ``GYÖKÉR/REPONEVE/``: 
	```sh
	git push heroku main
	``` 
47. CMD: ``GYÖKÉR/REPONEVE/`` 
	```sh
	heroku run python manage.py migrate
	```
48. CMD: ``GYÖKÉR/REPONEVE/`` 
	```sh
	heroku run python manage.py createsuperuser
	```
49. CMD: ``GYÖKÉR/REPONEVE/`` 
	```sh
	heroku open
	```
