Describes how to install Django with all other supported technologies to scale a web service. 

<!--more-->


### Configuring Python Environment

  virtualenv venv
  source venv/bin/activate

Then create a `requirements.txt` file to store your environment state. It is always good to add version numbers `name==version`, so an upgrade wont crash your environment.

    django==1.6.3
    south==0.8.4
    celery==3.1.11
    django-celery==3.1.10
    psycopg2==2.5.2




### Configuring Django Setttings for PostgreSQL
    'default': {
        'ENGINE':'django.db.backends.postgresql_psycopg2',
      	'NAME': 'sampleDb',
        'USER': 'sampleUser',
        'PASSWORD': 'sample',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }



### Configuring PostgreSQL

    $ psql
    psql (9.3.1)
    Type "help" for help.

    cevaris=# create database sampleDb;
    CREATE DATABASE

    cevaris=# create user sampleUser;
    CREATE ROLE

    cevaris=# GRANT ALL PRIVILEGES ON DATABASE sampleDb TO sampleUser;
    GRANT

    cevaris=# ALTER USER sampleUser WITH PASSWORD 'sample';
    ALTER ROLE