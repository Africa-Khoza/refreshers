# Starting a basic django app

This is a quick refresher on building a web application using django, based on the version 4.0 Writing your first django app tutorial.

## Steps

1. Start a project - `django-admin startproject <proj_name>`
2. Create app - `python manage.py startapp <app_name>`

### Creating web pages

1. Create a view - these are functions/classes that take a request and return an HttpResponse, usually a web page.
   1. Defined in the app/views.py file
   2. The functions/classes are called by a url, defined in app/urls.py
2. Create URLconf for the app (urls.py) - this lists all urls that call views in the app.
   1. Defined in the app/urls.py file.
   2. The next step is to point the root URLconf at the `app.urls` module. In `project/urls.py`, add an import for `django.urls.include` and insert an `include()` in the urlpatterns list.
   3. `path() argument: name` - Naming your URL lets you refer to it unambiguously from elsewhere in Django, especially from within templates. This powerful feature allows you to make global changes to the URL patterns of your project while only touching a single file.
3. Test with `python manage.py runserver`

### Setting up database (PostgreSQL)

Settings are located in the `project/settings.py` file, `DATABASES`. Change the following to switch to PostgreSQL.

1. Install postgres adapter for python: `pip install psycopg2`
2. Update settings
   `DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': '<database_name>',
            'USER': '<database_username>',
            'PASSWORD': '<password>',
            'HOST': '<database_hostname_or_ip>',
            'PORT': '<database_port>',
        }
    }`
3. Change timezone to Africa/Johannesburg
4. Run `python manage.py migrate` - this looks at all the installed apps and checks if there are any tables that need to be created, updated from each app.

### Creating Models

1. The name of the variable will be the column name on the database, but we can be extra and define a human readable column name as a parameter of the field.
2. A foreign key is added in this way: `question = models.ForeignKey(Question, on_delete=models.CASCADE)` - this tells Django each Choice is related to a single Question.

### Modifying Models

1. Make modifications.
2. Run `python manage.py makemigrations` to create migrations for those changes
3. Run `python manage.py migrate` to apply those changes to the database.

### Plugin the app

This is only a matter of adding `app_name.apps.App_NameConfig` to the `INSTALLED_APPS` in the settings. Then run `python manage.py makemigrations polls` to add any models defined or modified.

### Adding all-auth

1. Install `pip install django-allauth`
2. Add required settings (add link)
3. Continue adding all-auth then move on to part 3 in django tutorial.
