# Deployment (Heroku)
Django-szerver telepítése Heroku-ra.

## Röviden
A Heroku egy távoli tárhely, ami ingyenes. Az adatok küldése-fogadása a gitre épül. Tehát úgy fogod feltölteni a legújabb változatot a felhőbe, hogy push-olsz egyet herokura.

Szükséges ismeretek
- Git és Github alapvető ismeretek (clone, add, commit, push).
- Legalább egy Django applikáción legyél már túl. (az mindegy, hogy az mennyire volt bonyolult)
- Python virtuális környezet

Én innen dolgoztam, mikor ezt összeraktam: 

https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Deployment

- én most 3.1.7-es Django-val dolgozom. Esetleg érdemes frissíteni a legújabb django-ra:
```sh
python -m pip install -U Django
```

Ez nem feltétlen szükséges, de melegen ajánlott (ha véletlenül virtuális környezet nélkül csinálnál új appot, vagy ilyesmi...)

## GitHub
hol: https://github.com/

Csináljunk egy GitHub repo-t, majd klónozzuk le a gépünkre. 

Részletek:
1. GitHub new repo. A továbbiakban a repó nevére **REPONEVE**-vel hivatkozunk.
2. add .gitignore listből válasszuk ki a Python-t.
3. init with README.md
4. Másold ki vágólapra a git repo címét, amit a github repód Code-gombjára kattintva találsz meg, és valahogy így néz ki: 
```https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git```

## Git
A könyvtárat, amibe dolgozol, nevezzük a továbbiakban ``GYÖKÉR``-nek. Természetesen nem kell ténylegesen így hívni, de én így fogok rá hivatkozni.

hol: ``GYÖKÉR``

1. Nyisd meg a Git Bash-t, és írd be:
```sh
git clone https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git
```

2. Nyiss itt egy powershellt (vagy használd továbbra is a Git Bash-t, de én most powershellezni fogok) és üsd be, hogy ``tree /f``, amivel rálátsz a könyvtárfára. Ezt kéne látnod a ``GYÖKÉR`` könyvtárban:
```sh
C:.
└───REPONEVE
        .gitignore
        LICENSE
        README.md
```

## Virtuális környezet
### Új virtuális környezet létrehozása
 Csináljunk egy új virtuális környezetet a **GYÖKÉR** könyvtárban: Én ezt most **VENV**-nek fogom hívni, de te nyugodtan hívd másként.
```sh
virtualenv -p python3 VENV
 ```
 hja most újra nyomnál powershell-ben egy ``tree /f``-et, valószínűleg golyózni kezdene szemed, mert a virtuális környezet létrejöttével rengeteg fájl fog megjelenni a **GYÖKÉR** könyvtárban. Elég egy ``tree``-vel ellenőrizni, hogy lett itt most sok minden. Nekünk a Scripts könyvtár lesz majd a fontos a **VENV** könyvtárban, mert innen lehet majd indítani a virtuális környezetet.

### Virtuális környezet elindítása
Indítsuk el a virtuális környezetet! (powershellben a 
 ```VENV + TAB + S + TAB + a + TAB```
 billentyűk lenyomásával szépen gyorsan kiegészül minden, szóval nem kell ezt ténylegesen megjegyezni. Azt kell megjegyezni, hogy a VENV könyvtárban S-betűvel kezdődő fájlban van az activate parancs...)
Windows:
```sh
.\VENV\Scripts\activate
``` 
	Linuxon ez kicsit más:
	```sh
	source herokuvenv/bin/activate
	```

Ha sikerült, a prompt előtt megjelenik a "(VENV)"

### Django telepítése VENV-be
3. Mivel most egy virtuális környezetben vagyunk, itt még nincsen Django telepítve. Telepítsük hát. Hacsak nem adsz meg verziószámot, akkor ez a legfrissebb Django-t fogja telepíteni. Ha nem a legfrissebb Django van eleve a gépeden, akkor még az is előfordulhat, hogy ez újabb lesz. Ez nem egyébként nem kellene problémát jelentsen.
```sh
pip install django
```

### Gunicorn telepítése VENV-be

Ugyanebben a virtuális környezetben feltelepítjük a Gunicorn-t. Ez lényegében az Apache server Python-os megfelelője, a Herokunál nagyon szeretik.
```sh
pip install gunicorn
```

### URL-es adatbáziskezelő django library telepítése VENV-be
Ez majd azért kell, hogy a Herokun jól működjön az adatbáziskezelés. 
```sh
pip install dj-database-url
```

### Whitenoise a statikus fájlok kiszolgálásához
### Whitenoise
A statikus fájlok szervírozásához ezt javasolja a Heroku:
```sh
pip install whitenoise
```

## Django
### Projekt
hol: ``GYÖKÉR/REPONEV/``
fontos, hogy nem a GYöKé könyvtárban vagyunk, hanem azon belül. A heroku arra számít, hogy a git-es fájlokkal egy magasságban helyezkedik el django projekt, szóval még ezen is alakítani kell majd
Szóval a ``tree /f``-fel a következőt látod:
C:.
    .gitignore
    LICENSE
    README.md
Hozzunk létre egy django projektet (és kommitoljuk), amit én **PROJEKT**-nek fogok hívni
```sh
django-admin startproject PROJEKT
```

> (Fontos, hogy ez a parancs nem a virtuális környezetet használja! Tehát ha a két django-telepítés eltérő verziószámú, akkor itt lehetnek majd kisebb összezörrenések)

Egy ``tree /f``-re ekkor ezt látjuk:
```sh
C:.
│   .gitignore
│   LICENSE
│   README.md
│
└───PROJEKT
    │   manage.py
    │
    └───PROJEKT
            asgi.py
            settings.py
            urls.py
            wsgi.py
            __init__.py
```
Tehát a **REPONEV** könyvtárban egymásba ágyazva szerepel egy projektről elnevezett könyvtár és a projekt könyvtára maga. 


Nézzük meg, működik-e:
```sh
py PROJEKT/manage.py runserver
``` 
Csodáljuk meg a ``127.0.0.1:8000``-en, majd CTRL+C-vel állítsuk le.

Ha jól működött, ideje kommittolni (így illik + gyakorlás + paranoia). 

Ehhez a Git Bash-ben lépj be a **REPONEV** könyvtárba, mert odakint nincs verziókezelés, csak odabent. Onnan lehet látni, hogy bekapcsol a verziókezelés, hogy megjelenik a prompt végén a "(main)".

```sh
cd REPONEV
git add .
git commit -m "Django-projekt létrehozása."
git push origin main
```

Eddigi eredményeink ezután már a githubon is megtekinthetők

#### lokális adatbázis
A következő kettőnek nincs nagy jelentősége, mert a Heroku úgyis más adatbázist használ majd. Ettől függetlenül jó, ha megvannak, mert a tesztelés során is kellhet pl. az admin site és társai.

Alapvető adatbázisos dolgokat migráljuk le.
```sh
py manage.py migrate
``` 

Hozzunk létre admin felhasználót
```sh
py manage.py createsuperuser
```


### App
hol: ``GYÖKÉR/REPONEV/PROJEKT/``
Csináljunk egy appot, erre én APP-ként fogok hivatkozni.
```sh
django-admin startapp APP
```
Ekkor egy ``tree /f `` után a következőt látod:

```sh
C:.
│   db.sqlite3
│   manage.py
│
├───APP
│   │   admin.py
│   │   apps.py
│   │   models.py
│   │   tests.py
│   │   views.py
│   │   __init__.py
│   │
│   └───migrations
│           __init__.py
│
└───PROJEKT
    │   asgi.py
    │   settings.py
    │   urls.py
    │   wsgi.py
    │   __init__.py
    │
    └───__pycache__
            settings.cpython-38.pyc
            urls.cpython-38.pyc
            wsgi.cpython-38.pyc
            __init__.cpython-38.pyc˙
```

Gyorsan hozzunk létre egy view-t, hogy amikor már felraktuk heroku-ra a dolgokat, akkor meg tudjuk nézni, jól működnek-e a dolgok.

Ehhez a lépések röviden:
- PROJEKT/PROJEKT/settings.py: regisztráljuk az appot
```py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_toc',
]
```
- PROJEKT/PROJEKT/settings.py: regisztrálunk egy "templates" könyvtárat
```py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```
picivel régebbi Django-verziókban még nem volt "/" magic method stringek közt, így ebben az esetben ``BASE_DIR / 'templates'`` helyett a következő kell:
```py
os.path.join(BASE_DIR, 'templates')
```
Ebben az esetben kell egy 
```py
import os
```
is valahova előre, de ez általában már ott van. Az adatbázis beállításainál meg lehet nézni, hogy ez ott hogy van, azt érdemes másolni.

- PROJEKT/ létrehozunk egy ``template`` könyvtárat, abban egy **APP** könyvtárat és abban egy akármilyen html(-template) fájlt, mondjuk ``EZ.html``-t. Mindegy, mi van benne.
- PROJEKT/APP/a views.py-ba létrehozzuk a view-t, pl. én VIEW-nak fogom nevezni:
```py
def VIEW(request):
	return render(request, "APP/EZ.html", {})
```
- PROJEKT/PROJEKT/urls.py: importáljuk az app views.py-ját, majd hozzárendelünk a meghívott view-hoz egy url-t.
```py
from django.contrib import admin
from django.urls import path
from APP.views import VIEW
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', VIEW),
]
```

Teszteljük, és ha jó, akkor kommitolunk:
```sh
git add .
git commit -m "App és view létrehozása."
git push origin main
```

### Adatbázis
Sajnos az SQLite-nak itt a vége. A Heroku dyno-based, ami azzal jár, hogy ún. ephemeral fájlrendszert használ. Erről most elég annyit tudni, hogy minden alkalommal törlődik/újratöltődik minden, amikor újraindítjuk az appot (De általában naponta újraindul az egész, szóval...). Mivel az SQLite egy fájlba ment mindent, az is törlődne ott, szóval muszáj találnunk egy nem sqlite alapú adatbázist, tehát új alapokra kell helyezni a Django memóriáját...

Akit érdekel, itt lehet olvasgatni arról, hogy mik azok a dyno-k: 
https://dev.to/milandhar/what-are-heroku-dynos-3b1p

#### URL-es adatbáziskezelő konfigurálása:
hol: ``GYÖKÉR/REPONEV/PROJEKT/PROJEKT/settings.py``

mehet a settings.py legalájra:
```py
# Heroku: Update database configuration from $DATABASE_URL.
import dj_database_url
db_from_env = dj_database_url.config(conn_max_age=500)
DATABASES['default'].update(db_from_env)
```
ez még nem jelenti azt, hogy végleg otthagytuk volna az SQLite-ot: a development server ugyanúgy sqlite-ozni fog, mert a DATABASE_URL-t most még nem állítottuk be.

#### psycopg2 
Python Postgres adatbázistámogatás: át lehet térni devserveren is a postgresre, lásd a mozilla tutorialt! (bár ott csak linuxról írnak!)

### Statikus fájlok kiszolgálása

három fontos változó van:

```STATIC_URL```
Ez az URL-je annak, ahonnan a statikus fájlok szervírozva lesznek, pl egy CDN (content delivery network) címe. A djangoban használt ``load static`` használja igazából ezt.

```STATIC_ROOT``` Ez az abszolút elérési útvonala annak a könyvtárnak, ahol a Django ``collectstatic`` parancsa összeszedi az összes template-ek által hivatkozott statikus fájlt. Azután, hogy összeszedte, egy csapatban tölti fel őket oda, ahova hostolni kell. 

```STATICFILES_DIRS```
További könyvtárak, ahonnan a ``collectstatic``-nak kutakodnia kellene.

Szóval irány a...
hol: ``GYÖKÉR/REPONEV/PROJEKT/PROJEKT/settings.py``


Másoljuk be ezeket a sorokat. A ``STATIC_URL`` valószínűleg már ott van, szóval ha kétszer szerepelne, akkor a régit töröljük...

```py
STATIC_ROOT = BASE_DIR / 'staticfiles'  
#régebbi django-hoz: 
#STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

STATIC_URL = '/static/'
```

#### whitenoise
aztán a ``settings.py``-ba pakoljuk be a MIDDLEWARE-ek közé:
```py
..., 'whitenoise.middleware.WhiteNoiseMiddleware', ...
```

Opcionálisan ezt is a ``settings.py`` végére lehet írni, hogy hatékonyabban dolgozzon:

```py 
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```
### Mentés

```sh
git add .
git commit -m "adatbázissal kapcsolatos beállítások a settings.py-ban"
git push origin main
```

## Heroku-fájlok
hol: ``GYÖKÉR/REPONEV/``

### Procfile
hol: ``GYÖKÉR/REPONEV/``

- új fájl: "Procfile", kiterjesztés nem kell, benne ez: 
```sh
web: gunicorn project_heroku.wsgi --log-file -
```

Ezt igazából powershellel is kiírathatod, csak majd ne felejtsd el majd átállítani utf8-ra:

```sh
echo web: gunicorn project_heroku.wsgi --log-file - > Procfile
```

### requirements.txt
hol: ``GYÖKÉR/REPONEV/``

Ez a repo gyökerében kell legyen és a szükséges applikációkat kell tartalmazza. A Heroku ez alapján installál akkor, amikor újraépíti a környezetet.

Most szuper a powershell, mert csak ki kell nyomtatni a pip freeze-ből:

```sh
pip freeze > requirements.txt
```
Állítsd át a kódolást utf-8-ra.

Ezután módosítsuk úgy, hogy ezek legyenek ott (esetleg más verziószámmal):

```
dj-database-url==0.5.0
Django==3.1.7
gunicorn==20.0.4
psycopg2-binary==2.8.6
whitenoise==5.2.0
```

Psycopg2 legyen ott akkor is, ha nem raktad végül fel, mert a postgres adatbázishoz a herokun már mindenképp kelleni fog!
Én egy asgiref-et, pytz, sqlparse-ot töröltem most. Annak lehet utána kéne nézni, hoyg a psycopg2-ból a 2.8.6 rendjén van-e, de gondolom igen...

Amikor kézzel írod be a psycopg2-t, ne feledd a dupla egyenlőségjelet!


#### runtime.txt
hol: ``GYÖKÉR/REPONEV/``

Meg kell adni, hogy a heroku milyen python-nal dolgozzon. Ezt tartalmazza majd a ``runtime.txt``.

Per pillanat én most a következő verziókat látom itt: https://devcenter.heroku.com/articles/python-support#supported-python-runtimes
```
python-3.9.2
python-3.8.8
python-3.7.10
python-3.6.13
```
Mivel nekem most 3.8.1-esem van még, a 3.8.8-at választom.

```sh
echo python-3.8.8 > runtime.txt
```
Egyébként ha egyébként rosszat írsz be, a Heroku úgy is ignorálja majd.

Az echo átírányításával kapott fájlok kódolását konvertáljuk át utf8-ra! Notepad++!

#### Mentés...
Teszteljük le újra a site-ot, működik-e. Ha megy minden, akkor mehet a git:

### Mentés

```sh
git add .
git commit -m "Heroku-fájlok: Procfile, requirements, runtime.txt"
git push origin main
```

## Heroku
### Regisztráció
hol:
Magától értetődő. Ne felejtsük el az ingyenest választani.
### Kliens installálása
hol: https://devcenter.heroku.com/articles/getting-started-with-python#set-up

mehet minden tokkal-vonóval.

A végén érdemes újraindítani a powershell-t gitbash-t, mivel új programok kerültek a PATH-ra, amiket majd használni fogunk.

### Kliens (CLI) használata
hol: ``GYÖKÉR/REPONEV/``
találd ki, hogy mi legyen a herokus repo neve, én ezt REMOTENEV-nek fogom hívni. Annyi jelentősége talán még lehet ennek, hogy a default url neve is ez lesz majd.

#### Heroku remote létrehozása
```sh
heroku create HEROKUREMOTE
```

Gyorsan próbáld is ki, a http://HEROKUREMOTE.herokuapp.com/ címen megtalálod a repo-t. Valami olyasmit ír majd, hogy 
```
Heroku | Welcome to your new app!
Refer to the documentation if you need help deploying.
```

Ha hibaüzenet van, az azért van, mert újra kéne indítani a powershellt. (új program került a PATH-hoz!)


Ha kész, az azt jelenti, hogy mostantól két remote repod lesz.
Erre rá is lehet nézni gitben:
```sh
git remote -v
```
szóval heroku-val fogsz tudni pusholni. 

#### Push Herokura

Tegyük is fel:
```sh
git push heroku main
```

összesen tehát három helyen lesz megtalálható az applikációd.
1. Számítógépeden
2. GitHubon
3. Heroku-n

#### Adatbázis kezelése Herokun

Kell egy migrálás a remote-on
```sh
heroku run python manage.py migrate
```

Nálam ez elsőre nem ment, mert nem itt volt defaultból, de ez már ment:
```sh
heroku run python project_heroku/manage.py migrate
```

csináljunk admint...
```sh
heroku run python project_heroku/manage.py createsuperuser
```

csináljunk shellel inicializálhatjuk programozottan az adatbázisunkat...
```sh
heroku run python project_heroku/manage.py shell
```

És végül: ezzel nyitja meg az applikációt
```heroku open```

