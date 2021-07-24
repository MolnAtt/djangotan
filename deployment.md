# Django applikáció telepítése Heroku-ra

## Az elnevezésekről
A csupa **nagybetűs kifejezések** mind "placeholderek" a számodra, tehát pl a PROJEKT kifejezés helyére a te projekted nevét írd majd, stb.

A következő változókat fogjuk használni:
- ``PROJEKT``: a Django projekt neve, ahol a ``settings.py`` is megtalálható.
- ``APP``:  A Django applikáció neve, ahol a ``views.py`` is megtalálható.
- ``HEROKUREMOTE``: Az applikáció leendő webes elérhetősége, a mi a http://HEROKUREMOTE.herokuapp.com/ címben is megtalálható lesz majd.
- ``GYÖKÉR``: Az egész fájlrendszer gyökérkönyvtára, ahol a virtuális környezet, a repo, és egyébként minden megtalálható.
- ``REPONEVE``: A git repository neve.
- ``ATEGITHUBFIÓKOD``: A GitHub-fiókod neve.
- ``VENV``: A virtuális környezet neve.

## Röviden
A Heroku egy távoli tárhely, ami ingyenes. Az adatok küldése-fogadása a gitre épül. Tehát úgy fogod feltölteni a legújabb változatot a felhőbe, hogy push-olsz egyet herokura.

Kapcsolódó ismeretek (ha van, szuper, ha nincs, nem akkora baj):
- Git és Github alapvető ismeretek (clone, add, commit, push).
- Legalább egy Django applikáción legyél már túl. (az mindegy, hogy az mennyire volt bonyolult)
- Python virtuális környezet

Én innen dolgoztam, mikor ezt összeraktam: 

https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Deployment

Én most 3.1.7-es Django-val dolgozom. Esetleg érdemes frissíteni a legújabb django-ra:

**Hol:** bárhol
```sh
python -m pip install -U Django
```

Ez nem feltétlen szükséges, de melegen ajánlott (ha véletlenül virtuális környezet nélkül csinálnál új appot, vagy ilyesmi... pl a django-admin parancs nem virtuális környezettől függ, bár van virtuáliskörnyezetfüggő verzió is rá.)

## GitHub
### Mi a Git és a GitHub?
#### Verziókezelés
A git egy verziókezelő program. Számos előnye van, manapság alapvető standard az iparban, csak nyerni lehet vele. Mi most nem vesszük végig az összes okot, hogy miért is érdemes gitezni, mit lehet vele elérni, mindössze egyetlen vonását fogjuk kihasználni, erről az egyetlen oldaláról írok most néhány sort.

Amikor egy játékprogramban rányomsz a mentésre. Létrejön egy mentés, ahova később vissza tudsz lépni, ha akarsz, ilyen értelemben képes vagy "visszautazni az időben". A git akkor jön nagyon jól, amikor az ember nem egy fájlt, hanem sok fájlt szerkeszt párhuzamosan egyszerre. A webprogramozás, különösen szerver-oldalon, ilyen műfaj. Telepítve a gitet a gépünkre képesek vagyunk pillanatképeket lőni a projektjeinkről úgy, hogy ezek ne egymás hegyén hátán, hanem a szemünk elől elrejtve, mégis végig nagyon rendszerezetten legyenek eltárolva. Ezek a pillanatképek tulajdonképpen egy bonyolult gráfot alkotnak. Ezt a tartalommal teli gráfot hívjuk repository-nak, vagy egyszerűen csak repónak. Ezen repók kezelése, szervezése tulajdonképpen a verziókezelés.

#### Git
A git egy programnyelv és egy program egyszerre. A git parancsokat könnyű lesz felismerni, mindig parancssorba írjuk őket és git-tel kezdődnek. Léteznek azonban ablakos alkalmazások is és a főbb fejlesztői környezetek és kódszerkesztők is beépített git-interfésszel rendelkeznek. Mi végig parancssort fogunk használni, mert az mindig a legáltalánosabb. Összesen 4 parancs lesz, nem kell ettől tartani.

- ``git add .`` a git minden fájlt (a ``.gitignore`` fájlban felsorolt kivételektől eltekintve) felvesz a verziókezelendő fájlok közé. (Felteszi őket a "staging area"-ba (a pódiumra, ahol majd le fogja fényképezni őket))
- ``git commit -m "ez egy leírás"`` A git elmenti az előbb kijelölt fájlokat és létrehozza a repó legújabb verzióját. A commit tartalma és kommentje is nagyon fontos. Ne committoljunk se túl ritkán, se túl gyakran: a program fejlődése során a különböző logikailag jól elkülöníthető feladatok elvégzése után commitoljunk. Programok esetében jó irányelv az, ha úgy fejlesztesz, hogy a minden commit során a program szintaktikailag helyes legyen és le tudjon futni annak ellenére, hogy még sok mindent nem csinál.
- ``git push ... ...``: Feltölti a repót egy távoli repóba. Ezzel fogjuk majd feltölteni a távoli gépre is a programunk! De ez is része a folyamatos archiválásnak is: add-commit-push-add-commit-push-...
- ``git clone ...`` a könyvtárba lehív egy távoli repót. A könyvtár lehetőleg legyen üres, és még véletlenül se legyen verziókezelt!

#### GitHub
A gitHub egy felhőben lévő tárhely a repók számára. Regisztrálsz, bejelentkezel, feltöltöd a repód (gittel a gépről) és akkor ott van, távol, biztonságban, és te bárhova leszedheted később. Természetesen a GitHub számos más funkciót el tud még látni ezen felül, pl. itt is lehet repókat létrehozni, elemezni, stb., de mi ez alkalommal nem fogunk mást használni. 

Jelen pillanatban a GitHub-ra való regisztráció ingyenes, nyilvános repókat gyakorlatilag korlát nélkül lehet használni, másokkal megosztani. A privát repók (valószínűleg a szintén említésre méltó rivális GitLab nyomására) is korlátozott mértékben, de hozzáférhetők. Legfeljebb három kontribútor férhet hozzá a GitHub ingyenes verziójában egy privát repóhoz.

**A szervered a GitHub-on majd mindenképp privát repón legyen!** Meg lehet oldani azt is, hogy public repón dolgozz, de ahhoz némi tapasztalat kell (titkos adatok, secret key, email-beállítások, stb. külön fájlba, azok gitignore-ba, stb.)

Ha még nem regisztráltál gitre, itt az ideje. 
Magától értedődő a regisztráció. 

https://github.com/



#### Heroku és Git

A Heroku, ahova a szerverünket feltesszük majd, tulajdonképpen semmi mást nem csinál majd, mint egy ilyen repót üzemeletet. A repónak meg kell majd legyen a formája, stb., de ilyen egyszerű az egész. 

Tehát mire a végére érsz ennek a tutorialnak, a szervered három helyen lesz:
- a gépeden
- GitHub-on
- Heroku-n

### Új repó
Csináljunk egy GitHub repo-t, majd klónozzuk le a gépünkre. 

Részletek:
1. GitHub new repo. A továbbiakban a repó nevére ``REPONEVE``-vel hivatkozunk. 
2. add .gitignore listből válasszuk ki a Python-t.
3. init with README.md
4. Másold ki vágólapra a git repo címét, amit a github repód Code-gombjára kattintva találsz meg, és valahogy így néz ki: 
```https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git```

## Git
A könyvtárat, amibe dolgozol, nevezzük a továbbiakban ``GYÖKÉR``-nek. Természetesen nem kell ténylegesen így hívni, de én így fogok rá hivatkozni.

**Hol:** ``GYÖKÉR``

1. Nyisd meg a Git Bash-t, és írd be:
```sh
git clone https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git
```

2. Nyiss itt egy powershellt (vagy használd továbbra is a Git Bash-t, de én most powershellezni fogok) és üsd be, hogy ``tree /f``, amivel rálátsz a könyvtárfára. Ezt kéne látnod a ``GYÖKÉR`` könyvtárban:

**Hol:** ``GYÖKÉR``
```sh
C:.
└───REPONEVE
        .gitignore
        LICENSE
        README.md
```

## Virtuális környezet
### Minek virtuális környezet?

A virtuális környezet lényegében arra jó, hogy elszimulál egy számítógépes környezetet. Gondoljunk bele, hogy fejlesztesz Python 3.8.8-ban, Django 3.1.7-ben, ... egy adott applikációt, amit aztán kiraksz egy ilyen beállításokkal rendelkező szerverre. Majd másfél év után hozzá kell nyúlnod a **te gépeden**, hogy új feature-öket rakj bele, stb. Te azonban már rég túlléptél ezeken verziókon. Ilyenkor nem biztos, hogy érdemes az egész projektet áttenni fejlettebb verzióra, főleg, hogy a fent látható "..." nem 2-3 library-t, hanem inkább 20-40 library-t foglal magában.

Ilyenkor az lenne a jó, ha csak vissza tudnál utazni az időben, mikor még azok a verziójúk voltak ezekből a library-kből, amik akkor voltak, mikor a szervert fejlesztetted. A virtuális környezet, virtual environment, ezt a célt szolgálja.

A szerver fejlesztéséhez magához nincsen szükség virtuális környezetre, de ha az ember szeretne hosszú távon is eredményt elérni, akkor a projekt indítása előtt létrehoz egy virtuális környezetet, ami afféle buborékként szolgál majd. Ide olyan verzióban tesz fel mindent, amire csak szüksége van. És itt, ebben a "buborékban" fejleszt. Ez arra is jó, hogy egy régi szerverhez hozzá lehessen nyúlni, akkor is, ha nem te fejlesztetted: megnézed, hogy milyen library milyen verzióban van, és azt rakod a buborékba, imitálva a szerver környezetét.

Mi most nem fogunk nagyon komoly virtuális környezetekkel foglalkozni (pl. ott a Docker, ami komplett oprendszereket szimulál), hanem csak és kizárólag pythonon belül végezzük el ezt. 

Tehát: nem kötelező a virtuális környezet, de Heroku-t pl. sokkal egyszerűbb így konfigurálni, és egyébként is hosszú távon ez a jó praxis.

Tisztességes szerver-oldali programozó **virtuális környezet**ben dolgozik és minden lépését alaposan **git**eli.

### Új virtuális környezet létrehozása
 Csináljunk egy új virtuális környezetet a **GYÖKÉR** könyvtárban: Én ezt most ``VENV``-nek fogom hívni, de te nyugodtan hívd másként. Egy jó tanács: kezdődjön más betűvel, mint a név, amit a projektednek szánsz.

 **Hol:** ``GYÖKÉR/``
```sh
virtualenv -p python3 VENV
 ```

 ha most újra nyomnál powershell-ben egy ``tree /f``-et, valószínűleg golyózni kezdene szemed, mert a virtuális környezet létrejöttével rengeteg fájl fog megjelenni a **GYÖKÉR** könyvtárban. Elég egy ``tree``-vel ellenőrizni, hogy lett itt most sok minden. Nekünk a Scripts könyvtár lesz majd a fontos a **VENV** könyvtárban, mert innen lehet majd indítani a virtuális környezetet.

### Virtuális környezet elindítása
Indítsuk el a virtuális környezetet! 

Windows:

**Hol:** ``GYÖKÉR/``
```sh
.\VENV\Scripts\activate
``` 
Egyébként powershellben a ``VENV + TAB + S + TAB + a + TAB`` billentyűk lenyomásával szépen gyorsan kiegészül minden, szóval nem kell ezt ténylegesen megjegyezni. Azt kell megjegyezni, hogy a VENV könyvtárban S-betűvel kezdődő fájlban van az activate parancs... Tehát ha VENV más betűvel kezdődik, mint minden más, akkor elég az első betűt beütni.
 
Linuxon ez kicsit más:
```sh
source VENV/bin/activate
```

Ha sikerült, a prompt előtt megjelenik a ``"(VENV)"``. Fontos, hogy bármit is csinálsz, ez mindig legyen ott! **A powershell/bash/... minden új indításakor ezt külön el kell indítani!**

### Django telepítése VENV-be
3. Mivel most egy virtuális környezetben vagyunk, itt még nincsen Django telepítve. Telepítsük hát. Hacsak nem adsz meg verziószámot, akkor ez a legfrissebb Django-t fogja telepíteni. Ha nem a legfrissebb Django van eleve a gépeden, akkor még az is előfordulhat, hogy ez újabb lesz.

**Hol:** mindegy, csak legyen ott a (VENV) a prompt elején!
```sh
pip install django
```

### Gunicorn telepítése VENV-be

Ugyanebben a virtuális környezetben feltelepítjük a Gunicorn-t. Ez lényegében az Apache server Python-os megfelelője, a Herokunál nagyon szeretik.

**Hol:** mindegy, csak legyen ott a (VENV) a prompt elején!
```sh
pip install gunicorn
```

### URL-es adatbáziskezelő django library telepítése VENV-be
Ez majd azért kell, hogy a Herokun jól működjön az adatbáziskezelés. 

**Hol:** mindegy, csak legyen ott a (VENV) a prompt elején!
```sh
pip install dj-database-url
```

### Whitenoise a statikus fájlok kiszolgálásához
### Whitenoise
A statikus fájlok szervírozásához ezt javasolja a Heroku:

**Hol:** mindegy, csak legyen ott a (VENV) a prompt elején!
```sh
pip install whitenoise
```


### ellenőrzés
A következő paranccsal ellenőrizhetjük, hogy miből áll pillanatnyilag a virtuális környezetünk:

**Hol:** mindegy, csak legyen ott a (VENV) a prompt elején!
```sh
pip freeze
```

## Django
### Projekt
**Hol:** ``GYÖKÉR/REPONEVE/``
Fontos, hogy nem a GYÖKÉR könyvtárban vagyunk, hanem azon belül. Szóval a ``tree /f``-fel most a következőt látod:

**Hol:** ``GYÖKÉR/REPONEVE/``
```sh
C:.
    .gitignore
    LICENSE
    README.md
```

Hozzunk létre egy django projektet (és kommitoljuk), amit én **PROJEKT**-nek fogok hívni (Mindenképp ellenőrizzük, hogy a virtualenv be van-e kapcsolva, különben tartósan kiszúrhatunk magunkkal!)
```sh
django-admin startproject PROJEKT
```

Egy ``tree /f``-re ekkor ezt látjuk:

**Hol:** ``GYÖKÉR/REPONEVE/``
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
Tehát a ``REPONEVE`` könyvtárban egymásba ágyazva szerepel egy projektről elnevezett könyvtár és a projekt könyvtára maga. 

Még mielőtt bármit csinálunk: a Heroku git repo-t vár, és a gyökérből szeretné futtatni a ``manage.py``-t, egy könyvtár mélységben fogja keresni a ``wsgy.py``-t, stb.

Szóval át kéne szervezni ezután a parancs után a könyvtárszerkezetet: Mozgasd át a felső PROJEKT könyvtár tartalmát a fölötte lévő könyvtárba. Ezt elintézni talán egyszerű intézőben a legegyszerűbb.

De ha érdekel a shell, itt a parancs: 

**Hol:** ``GYÖKÉR/REPONEVE``
```sh
mv PROJEKT/* .
```
(a mv a mozgatás, az első argumentuma azt mondja, hogy a PROJEKT/ könyvtár minden (\*) elemét átmozgatja az aktuális (.) könyvtárba.)


Ez után így kéne kinézzen a könyvtárszerkezet:

**Hol:** ``GYÖKÉR/REPONEVE/``
```sh
C:.
│   .gitignore
│   LICENSE
│   manage.py
│   README.md
│
└───PROJEKT
        asgi.py
        settings.py
        urls.py
        wsgi.py
        __init__.py
```

Nézzük meg, működik-e:

**Hol:** ``GYÖKÉR/REPONEVE``
```sh
py manage.py runserver
``` 
Csodáljuk meg a ``127.0.0.1:8000``-en, majd CTRL+C-vel állítsuk le.

Ha jól működött, ideje kommittolni (így illik + gyakorlás + paranoia). 

Ehhez a Git Bash-ben lépj be a **REPONEV** könyvtárba, mert odakint nincs verziókezelés, csak odabent. Onnan lehet látni, hogy bekapcsol a verziókezelés, hogy megjelenik a prompt végén a "(main)".

**Hol:** ``GYÖKÉR/REPONEVE``-n belül bárhol
```sh
git add .
git commit -m "Django-projekt létrehozása."
git push origin main
```

Eddigi eredményeink ezután már a githubon is megtekinthetők

#### lokális adatbázis
A következő kettőnek nincs nagy jelentősége, mert a Heroku úgyis más adatbázist használ majd. Ettől függetlenül jó, ha megvannak, mert a tesztelés során is kellhet pl. az admin site és társai.

Alapvető adatbázisos dolgokat migráljuk le.

**Hol:** ``GYÖKÉR/REPONEVE``
```sh
py manage.py migrate
``` 

Hozzunk létre admin felhasználót

**Hol:** ``GYÖKÉR/REPONEVE``
```sh
py manage.py createsuperuser
```


### App
Csináljunk egy appot, erre én APP-ként fogok hivatkozni.

**Hol:** ``GYÖKÉR/REPONEVE``
```sh
django-admin startapp APP
```
Ekkor egy ``tree /f `` után a következőt látod:

**Hol:** ``GYÖKÉR/REPONEVE/``
```sh
C:.
│   .gitignore
│   db.sqlite3
│   LICENSE
│   manage.py
│   README.md
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

Ehhez a lépések röviden: regisztráljuk az appot

**Hol:** ``GYÖKÉR/REPONEVE/PROJEKT/settings.py``
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
regisztrálunk egy "templates" könyvtárat

**Hol:** ``GYÖKÉR/REPONEVE/PROJEKT/settings.py``
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

**Hol:** ``GYÖKÉR/REPONEVE/``

Létrehozunk egy ``template`` könyvtárat, abban egy **APP** könyvtárat és abban egy akármilyen html(-template) fájlt, mondjuk ``EZ.html``-t. Mindegy, mi van benne.

**Hol:** ``GYÖKÉR/REPONEVE/APP/`` 

A views.py-ba létrehozzuk a view-t, pl. én VIEW-nak fogom nevezni:
```py
def VIEW(request):
	return render(request, "APP/EZ.html", {})
```

**Hol:** ``GYÖKÉR/REPONEVE/PROJEKT/urls.py``

Importáljuk az app views.py-ját, majd hozzárendelünk a meghívott view-hoz egy url-t.
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

**Hol:** ``GYÖKÉR/REPONEVE/``-n belül bár**Hol:**
```sh
git add .
git commit -m "App és view létrehozása."
git push origin main
```

### Adatbázis
Sajnos az SQLite-nak itt a vége. A Heroku dyno-based, ami azzal jár, hogy ún. ephemeral fájlrendszert használ. Erről most elég annyit tudni, hogy minden alkalommal törlődik/újratöltődik minden, amikor újraindítjuk az appot (De általában naponta újraindul az egész, szóval...). Mivel az SQLite egy fájlba ment mindent, az is törlődne ott, szóval muszáj találnunk egy nem sqlite alapú adatbázist, tehát új alapokra kell helyezni a Django memóriáját...

Akit érdekel, itt lehet olvasgatni arról, hogy mik ezek a Heroku dyno-k: 
https://dev.to/milandhar/what-are-heroku-dynos-3b1p

#### URL-es adatbáziskezelő konfigurálása:

**Hol:** ``GYÖKÉR/REPONEVE/PROJEKT/settings.py`` legaljára
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

Statikus fájloknak nevezzük azon fájlokat, amelyek nem szükségesek a html template rendereléséhez (a lyukasztott html kontextussal való behelyettesítésének elvégzéséhez). Ilyen pl az összes kép, css, js, stb. 

(Ha mégis valamely része a stíluslapnak vagy scriptnek függne a kontextustól, be tudod rakni embedded scriptbe a html templatebe, és ott tudsz "lyukasztani".)

A Heroku-n közvetlenül a push (=deployment) után lefut majd egy collectstatic parancs. Ez azt csinálja, hogy 
1. összegyűjti az összes regisztrált app összes statikus fájljait, és még azokat az app-független statikus fájlokat, amelyeket megtalál ``STATICFILES_DIRS`` változóban (lista) tárolt könyvtárak között.
2. bemásolja őket egy ``STATIC_ROOT`` változóban (string) tárolt könyvtárba.
3. ahonnan majd a ``STATIC_URL`` változóban (string) tárolt helyre fogja kiszolgálni a fájlokat. A böngészőben F12-vel is ezt látjuk majd, hogy itt vannak a statikus fájlok.

Szóval irány a...

**Hol:** ``GYÖKÉR/REPONEVE/PROJEKT/settings.py``

Másoljuk be ezeket a sorokat. A ``STATIC_URL`` valószínűleg már ott van, szóval ha kétszer szerepelne, akkor a régit töröljük...

```py
# INNEN szedegeti össze azokat a statikus fájlokat, amelyek nem tartoznak egyetlen apphoz sem:
STATICFILES_DIRS = [
    BASE_DIR/'static'
]

# IDE fogja collectelni a collectstatic
STATIC_ROOT = BASE_DIR / 'staticfiles'  
#régebbi django-hoz: 
#STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

# ITT fogja észlelni a böngésző
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

**Hol:** ``GYÖKÉR/REPONEVE/``-n belül bárhol
```sh
git add .
git commit -m "adatbázissal kapcsolatos beállítások a settings.py-ban"
git push origin main
```

## Heroku-fájlok
**Hol:** ``GYÖKÉR/REPONEVE/``

### Procfile
**Hol:** ``GYÖKÉR/REPONEVE/``

- új fájl: "Procfile", kiterjesztés nem kell, benne ez: 
```sh
web: gunicorn PROJEKT.wsgi --log-file -
```

Ezt igazából powershellel is kiírathatod, csak majd ne felejtsd el majd átállítani utf8-ra:

```sh
echo web: gunicorn PROJEKT.wsgi --log-file - > Procfile
```

### requirements.txt
Ez a repo gyökerében kell legyen és a szükséges applikációkat kell tartalmazza. A Heroku ez alapján installál akkor, amikor újraépíti a környezetet.

Most szuper a powershell, mert csak ki kell nyomtatni a pip freeze-ből:

**Hol:** ``GYÖKÉR/REPONEVE/``
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
Meg kell adni, hogy a heroku milyen python-nal dolgozzon. Ezt tartalmazza majd a ``runtime.txt``.

Per pillanat én most a következő verziókat látom itt: https://devcenter.heroku.com/articles/python-support#supported-python-runtimes
```
python-3.9.2
python-3.8.8
python-3.7.10
python-3.6.13
```
Mivel nekem most 3.8.1-esem van még, a 3.8.8-at választom.

**Hol:** ``GYÖKÉR/REPONEVE/``
```sh
echo python-3.8.8 > runtime.txt
```
Egyébként ha egyébként rosszat írsz be, a Heroku úgy is ignorálja majd.

Az echo átírányításával kapott fájlok kódolását konvertáljuk át utf8-ra! Notepad++!

#### Mentés...
Teszteljük le újra a site-ot, működik-e. Ha megy minden, akkor mehet a git:

### Mentés

**Hol:** ``GYÖKÉR/REPONEVE/``-n belül bárhol
```sh
git add .
git commit -m "Heroku-fájlok: Procfile, requirements, runtime.txt"
git push origin main
```

## Heroku
### Regisztráció
**Hol:**
Magától értetődő. Ne felejtsük el az ingyenest választani.
### Kliens installálása
**Hol:** https://devcenter.heroku.com/articles/getting-started-with-python#set-up

mehet minden tokkal-vonóval.

A végén érdemes újraindítani a powershell-t gitbash-t, mivel új programok kerültek a PATH-ra, amiket majd használni fogunk.

### Kliens (CLI) használata
**Hol:** ``GYÖKÉR/REPONEVE/``
találd ki, hogy mi legyen a herokus repo neve, én ezt REMOTENEV-nek fogom hívni. Annyi jelentősége talán még lehet ennek, hogy a default url neve is ez lesz majd.

#### Heroku remote létrehozása
**Hol:** ``GYÖKÉR/REPONEVE/``
```sh
heroku create HEROKUREMOTE
```
Ha hibaüzenet van, az azért van, mert újra kéne indítani a powershellt. (új program került a PATH-hoz!)


Gyorsan próbáld is ki, a http://HEROKUREMOTE.herokuapp.com/ címen megtalálod a repo-t. Valami olyasmit ír majd, hogy 
```
Heroku | Welcome to your new app!
Refer to the documentation if you need help deploying.
```

Ha kész, az azt jelenti, hogy mostantól két remote repod lesz.
Erre rá is lehet nézni gitben:

```sh
git remote -v
```
ezt fogja mutatni:
```
heroku  https://git.heroku.com/HEROKUREMOTE.git (fetch)
heroku  https://git.heroku.com/HEROKUREMOTE.git (push)
origin  https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git (fetch)
origin  https://github.com/ATEGITHUBFIÓKOD/REPONEVE.git (push)
```
#### Allowed hosts

DE még mielőtt pusholjuk herokura: egy production server nem működik, csak ott, ahol ezt neki megengedték.

Irány a settings.py, azon belül is az allowed hosts. Ott egy üres listát találsz, ami tökéletesen funkcionál a ```127.0.0.1/8000```-rel, de ennek most már van rende címe is. Ezt kéne beírni ÉS a 127.0.0.1-et, mivel az app fejlesztése otthon történik, így az asztali számítógépeden is működnie kellene a dev-szervernek.

Egyben itt az ideje annak is, hogy kikapcsoljuk a DEBUG-módot. (fejlesztés közben kapcsold be nyugodtan, de ne pusholj ``DEBUG=True``-s settings.py-t herokura, mert az tömérdek mennyiségű bizalmas információt (program struktúrája, felépítése, bizalmas adatbázisbeli adatok) ír ki a honlapra látogató felhasználónak, ha véletlenül bugra akad.)

**Hol:** ``GYÖKÉR/REPONEVE/PROJEKT/settings.py``
```py
# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = False

ALLOWED_HOSTS = ['HEROKUREMOTE.herokuapp.com', '127.0.0.1']
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

#### Végső simítások Herokun.

Kell egy migrálás a remote-on

**Hol:** ``GYÖKÉR/REPONEVE``
```sh
heroku run python manage.py migrate
```

csináljunk admint...

**Hol:** ``GYÖKÉR/REPONEVE``
```sh
heroku run python manage.py createsuperuser
```

shellel inicializálhatjuk programozottan az adatbázisunkat...

**Hol:** ``GYÖKÉR/REPONEVE``
```sh
heroku run python manage.py shell
```
És végül: ezzel nyitja meg az applikációt

**Hol:** ``GYÖKÉR/REPONEVE``
```sh
heroku open
```
