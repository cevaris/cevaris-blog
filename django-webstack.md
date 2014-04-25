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


### Configuring PostgreSQL

    $ psql
    psql (9.3.1)
    Type "help" for help.

    cevaris=# create database sampledb;
    CREATE DATABASE

    cevaris=# create user sampleuser;
    CREATE ROLE

    cevaris=# GRANT ALL PRIVILEGES ON DATABASE sampledb TO sampleuser;
    GRANT

    cevaris=# ALTER USER sampleuser WITH PASSWORD 'sample';
    ALTER ROLE



### Configuring Django Setttings for PostgreSQL
    
    DATABASES = {
        'default': {
            'ENGINE':'django.db.backends.postgresql_psycopg2',
            'NAME': 'sampledb',
            'USER': 'sampleuser',
            'PASSWORD': 'sample',
            'HOST': '127.0.0.1',
            'PORT': '5432',
        }
    }


Also update the `INSTALLED_APPS` dict to link the code
    
    INSTALLED_APPS = (
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',

        # Third party
        'south',

        # Apps
        'polls',
    )


### Create an Polling Application

First `cd` into wherever your `manage.py` script is located and execute the following to generate the `polls` app code for you.

    python manage.py startapp polls


Now that the code is generated and linked, generate teh SQL for Polls.

    (venv)$ python manage.py sql polls
    BEGIN;
    CREATE TABLE "polls_poll" (
        "id" serial NOT NULL PRIMARY KEY,
        "question" varchar(200) NOT NULL,
        "pub_date" timestamp with time zone NOT NULL
    )
    ;
    CREATE TABLE "polls_choice" (
        "id" serial NOT NULL PRIMARY KEY,
        "poll_id" integer NOT NULL REFERENCES "polls_poll" ("id") DEFERRABLE INITIALLY DEFERRED,
        "choice_text" varchar(200) NOT NULL,
        "votes" integer NOT NULL
    )
    ;

    COMMIT;

Now it is time to sync up your new schema state to the database. You can do this with the `syncdb` command. 

    (venv)$ python manage.py syncdb
    Syncing...
    Creating tables ...
    Creating table django_admin_log
    Creating table auth_permission
    Creating table auth_group_permissions
    Creating table auth_group
    Creating table auth_user_groups
    Creating table auth_user_user_permissions
    Creating table auth_user
    Creating table django_content_type
    Creating table django_session
    Creating table south_migrationhistory
    Creating table polls_poll
    Creating table polls_choice

    You just installed Django's auth system, which means you don't have any superusers defined.
    Would you like to create one now? (yes/no): yes
    Username (leave blank to use 'cevaris'):    
    Email address: cev@cevaris.com          
    Password: 
    Password (again): 
    Superuser created successfully.
    Installing custom SQL ...
    Installing indexes ...
    Installed 0 object(s) from 0 fixture(s)

    Synced:
     > django.contrib.admin
     > django.contrib.auth
     > django.contrib.contenttypes
     > django.contrib.sessions
     > django.contrib.messages
     > django.contrib.staticfiles
     > south
     > polls

    Not synced (use migrations):
     - 
    (use ./manage.py migrate to migrate these)

Execute the `syncdb` command again to see all is up to date.

    (venv)$ python manage.py syncdb
    Syncing...
    Creating tables ...
    Installing custom SQL ...
    Installing indexes ...
    Installed 0 object(s) from 0 fixture(s)

    Synced:
     > django.contrib.admin
     > django.contrib.auth
     > django.contrib.contenttypes
     > django.contrib.sessions
     > django.contrib.messages
     > django.contrib.staticfiles
     > south
     > polls

    Not synced (use migrations):
     - 
    (use ./manage.py migrate to migrate these)
    (venv)engr2-10-51-dhcp:sample cevaris$ 


## Starting the Server

Time to see if things are up and running now, start the server with the following command. Take notice, you can pass in a number for the port which you want the server to run on; for example, 3000. 

    (venv)$ python manage.py runserver 3000
    Validating models...

    0 errors found
    April 25, 2014 - 18:21:28
    Django version 1.6.3, using settings 'sample.settings'
    Starting development server at http://127.0.0.1:3000/
    Quit the server with CONTROL-C.


Now open up the web page to see a greeting.

    http://127.0.0.1:3000/

Try to login with your admin credentials earlier entered when executing the `syncdb` command.

    http://127.0.0.1:3000/admin/




