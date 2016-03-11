https://docs.djangoproject.com/en/1.9/intro/tutorial01/

    django-admin startproject pysec_project

pysec_project
├── manage.py
└── pysec_project
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py

    python manage.py runserver

Create the database. Sqlite3 is created by default from the settings.py file.
We then move to the parent pysec_project dir and run the following command to initialize the database

    python manage.py migrate

.
├── README.md
├── db.sqlite3
├── manage.py
└── pysec_project
    ├── __init__.py
    ├── __init__.pyc
    ├── settings.py
    ├── settings.pyc
    ├── urls.py
    ├── urls.pyc
    └── wsgi.py

we note that our file structure has changed.


In order to create the models we must first create an app, this is performed as follows:

    python manage.py startapp <app_name>

Your apps can live anywhere on your Python path. In this tutorial, we’ll create our poll app right next to your manage.py file so that it can be imported as its own top-level module, rather than a submodule of mysite.

In this example I used the app_name pysec. The tree is as follows:

.
├── README.md
├── db.sqlite3
├── manage.py
├── pysec
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
└── pysec_project
    ├── __init__.py
    ├── __init__.pyc
    ├── settings.py
    ├── settings.pyc
    ├── urls.py
    ├── urls.pyc
    └── wsgi.py

We skip the views and urls portion of the tutorial.

We are now going to create models, note that if we want to use the PYSEC models
they will have to be moved into this new directory. I'll give this a go by simply
copy and pasting in the code into the models.py file. NOTE: that in order for the model to work there are various supporting files that needed to be added:

- xbrl.py
- xbrl_fundamentals.py

The model we copy pasted is "Index". according to the tutorial we need to update the settings.py file under the INSTALLED_APPS

the polls app is written as follows: 'polls.apps.PollsConfig'
given that I don't understand the syntax I'll try adding
'pysec.apps.PysecConfig' recalling that our app is called pysec

now that django knows about the app

    python manage.py makemigrations pysec

    Jonathans-MacBook-Air:pysec_project jonbruno$ python manage.py makemigrations pysec
    Migrations for 'pysec':
      0001_initial.py:
        - Create model Index

this looks like it was a successful run!

There’s a command that will run the migrations for you and manage your database schema automatically - that’s called migrate, and we’ll come to it in a moment - but first, let’s see what SQL that migration would run. The sqlmigrate command takes migration names and returns their SQL:

    python manage.py sqlmigrate pysec 0001

now we can run the following to create the model tables in our database

    python manage.py migrate

    Jonathans-MacBook-Air:pysec_project jonbruno$ python manage.py migrate
    Operations to perform:
      Apply all migrations: admin, pysec, contenttypes, auth, sessions
    Running migrations:
      Rendering model states... DONE
      Applying pysec.0001_initial... OK

this also looks like success!

if we need to make future changes, remember the three-step guide to making model changes:

Change your models (in models.py).
Run python manage.py makemigrations to create migrations for those changes
Run python manage.py migrate to apply those changes to the database.


lets see if we can import the SEC database, first we have to bring over all the files in the pysec/pysec/management folder
the tree now looks like the following:

.
├── README.md
├── cof-20131231.xml
├── db.sqlite3
├── manage.py
├── pysec
│   ├── __init__.py
│   ├── __init__.pyc
│   ├── admin.py
│   ├── admin.pyc
│   ├── apps.py
│   ├── apps.pyc
│   ├── example.py
│   ├── management
│   │   ├── __init__.py
│   │   └── commands
│   │       ├── __init__.py
│   │       ├── sec_import_index.py
│   │       └── sec_xbrl_to_csv.py
│   ├── migrations
│   │   ├── 0001_initial.py
│   │   ├── 0001_initial.pyc
│   │   ├── __init__.py
│   │   └── __init__.pyc
│   ├── models.py
│   ├── models.pyc
│   ├── tests.py
│   ├── views.py
│   ├── xbrl.py
│   ├── xbrl.pyc
│   ├── xbrl_fundamentals.py
│   └── xbrl_fundamentals.pyc
└── pysec_project
    ├── __init__.py
    ├── __init__.pyc
    ├── settings.py
    ├── settings.pyc
    ├── urls.py
    ├── urls.pyc
    └── wsgi.py

    python manage.py sec_import_index

This creates the Index() model. To download any filing, call .download() on that model instance. To get its XBRL attributes if it's an XBRL filing, call .xbrl() on it and look at the .fields attribute of the returned model.




we can try and see if the PYSEC files work with a sample xml file, for test purposed I downloaded the file 'cof-20131231.xml'

in the pysec_project dir "/Users/jonbruno/Documents/Python/pysec_project"

run the following commands:
    import pysec.xbrl
    x = pysec.xbrl.XBRL('cof-20131231.xml')
    print x.fields #a dict of the most important values

from the PYSEC README file:
To get any XBRL term:
x.GetFactValue(XMBL TAG, "Duration" or "Instant" (depending on if it's a year-long or snapshot value))

For an example of generating a CSV of a list of companies, see management/commands/xbrl_to_csv.py
