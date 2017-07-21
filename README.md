# Django Tutorial Video Series on youtube
[Link to django class @ pythonprogramming.net](https://pythonprogramming.net/django-web-development-with-python-intro/)

## Part 1: Install django and test 
[Link to video](https://www.youtube.com/watch?v=FNQxxpM1yOs&t=627s)

Create the project
```
$ mkdir django-tutorial; cd django-tutorial
$ django-admin startproject mysite
```
The project is created in the current dir
./mysite/mysite contains the settings and main files

Running for the first time
```
$ cd mysite
$ python3 manage.py run
(ok to ignore the unapplied migrations for now)
```
Browse to the URL http://localhost:8000 to check if django website is up


## Part 2: Create the first app 
[Link to video](https://www.youtube.com/watch?v=iZ5my3krEVM)

Start at the top level to create the new app, webapp
```
$ cd mysite
$ python3 manage.py startapp webapp
```
Add 'webapp' to ./mysite/settings.py INSTALLED_APPS
```python
INSTALLED_APPS = [
    'webapp',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Add webapp to ./mysite/urls.py and add import include
```python
from django.conf.urls import url, include
from django.contrib import admin

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^webapp/', include('webapp.urls')),
]
```

Add index function to ./mysite/webapp/views.py
```python
from django.shortcuts import render
from django.http import HttpResponse

def index(request):
    return HttpResponse("<h2>HEY!</h2>"
```

Create new file ./mysite/webapp/urls.py, add new pattern
```python
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^$', views.index, name='index')]
```

Start the app on the server
```
$ cd mysite
$ python3 manage.py runserver
```

Browse to the URL http://localhost:8000/webapp to check that app works  
*NOTE: browsing to http://localhost:8000/ for now gives error 404*

Each app is modeled as MVC model-view-controller
* ./mysite/urls.py has the "list of apps"
* ./mysite/webapp/urls.py controls what is served based on url patterns
   (regular expressions)
* ./mysite/webapp/models.py has data models, i.e. database structures and metadata
* ./mysite/webapp/views.py handles UI, i.e. what the end-user views/interacts


## Part 3: HTML templating with Jinja
[Link to video](https://www.youtube.com/watch?v=3tf8XlhsQAo)

### Pre-requisites
Start at the top level to create the new app, personal
```
    $ cd mysite
    $ python3 manage.py startapp personal
```
Install the app (add entry 'personal' to settings.py INSTALLED_APPS)
```python
INSTALLED_APPS = [
    'personal',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```
Add default URL to ./mysite/urls.py to point to personal
```python
from django.conf.urls import url, include
from django.contrib import admin

urlpatterns = [
    url(r'^$', include('personal.urls')),
]
```

Create new file ./mysite/personal/urls.py, add new pattern
```python
from django.conf.urls import url, include
from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
```

Edit ./mysite/personal/views.py, add return a html location to render
```python
from django.shortcuts import render

def index(request):
    return render(request, 'personal/home.html')
```

### Add HTML template files

Add a templates directory with a personal subdirectory  
*NOTE: subdirectory needed because django looks at all apps in the project in the templates dir*
```
$mkdir templates/personal
```

In templates/personal directory, create a header.html file  
*The content between {% %} in Jinja*
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <title>Dennis Wong</title>
    <meta charset="utf-8"/>
</head>

<body class="body" style="background-color:#f6f6f6">
    <div>
        {% block content %}
        {% endblock %}
    </div>
</body>

</html>
```

Create a home.html file that extends header.html
```
{% extends "personal/header.html" %}

{% block content %}
<p>Hello, welcome to my django website!</p>
{% endblock %}
```

Start the app on the server
```
$ cd mysite
$ python3 manage.py runserver
```

Browse to the URL http://localhost:8000/ to check that app works

Besides using extends, content can be added using include, this is home.html with an include
```
{% extends "personal/header.html" %}

{% block content %}
<p>Hello, welcome to my django website!</p>

{% include "personal/includes/htmlsnippet.html" %}
{% endblock %}
```

Create the new include file htmlsnippet.html in the templates/personal/includes directory
```
{% block content %}
<p>Look at me, I am included!!!</p>
{% endblock %}
```

Refresh web page on browser http://localhost:8000/ and see the included content

## Part 4: Use Bootstrap to add style to HTML/CSS
[Link to video](https://www.youtube.com/watch?v=p8qpu9WscFU)

### Pre-requisites

Download bootstrap from http://getbootstrap.com/getting-started/#download
*NOTE: tutorial author recommends perusing CSS, Components, and Javascript tabs of the Bootstrap website*

Copy all directories under bootstrap-3.x.y-dist into static
```
$ mkdir static; cd static
$ cp ~/Downloads/bootstrap-3.3.7-dist/* .
```

### Add bootstrap style to the website

Add the static files from bootstrap in header.html

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <title>Dennis Wong</title>
    <meta charset="utf-8"/>
    {% load staticfiles %}
    <link rel='stylesheet' href="{% static 'css/bootstrap.min.css' %}"
     type='text/css'/>
</head>

<body class="body" style="background-color:#f6f6f6">
    <div>
        {% block content %}
        {% endblock %}
    </div>
</body>

</html>
```

The tutorial lesson has a more complicated version of header.html that includes more bootstrap components that makes the web page mobile friendly.

## Part 5: Passing variables from python to HTML in Jinja
[Link to video](https://www.youtube.com/watch?v=nWRboXrcipU#t=1.932872701)

Create a contact page to illustrate passing variables from python to HTML, this is done using a basic.html file.

First, edit views.py and add a contact function
```python
from django.shortcuts import render

def index(request):
    return render(request, 'personal/home.html')

def contact(request):
    return render(request, 'personal/basic.html', {'content':['If you would like to contact me, please email me','hskinsley@gmail.com']})
```

Second, create a basic.html file in templates/personal directory  
This is where the strings in views content is passed from python to html
```
{% extends "personal/header.html" %}

{% block content %}
    {% for c in content %}
        <p>{{c}}</p>
    {% endfor %}

{% endblock %}
```

Next, edit personal/urls.py, add the contact pattern
```python
from django.conf.urls import url, include
from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
    url(r'^contact/', views.contact, name='contact'),
]
```

At this point, if we try to click contact, there is an error 404,  
The error can be fixed by removing the $ in the r'^$' pattern  
in mysite/urls.py
```python
from django.conf.urls import url, include
from django.contrib import admin

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^', include('personal.urls')),
]
```

## Part 6: Beginning the blog
[Link to video](https://www.youtube.com/watch?v=uI2KW8K_eks)

Start at the top level, create a new app blog
```
    $ cd mysite
    $ python3 manage.py startapp blog
```

Install the app (add entry 'blog' to settings.py INSTALLED_APPS)
```python
INSTALLED_APPS = [
    'personal',
    'blog',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```
Add blog URL to ./mysite/urls.py to point to blog
```python
from django.conf.urls import url, include
from django.contrib import admin

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^', include('personal.urls')),
    url(r'^blog/', include('blog.urls')),
]
```

Create the data model, edit mysite/blog/models.py
```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=140)
    body = models.TextField()
    date = models.DateTimeField()
    
    def __str__(self):
        return self.title
```

## Part 7: Add view and template to blog
[Link to video](https://www.youtube.com/watch?v=uGsmuCjZBSU)

Add blog to mysite/urls.py, use django generic views instead of models.py  
The posts are reverse sorted and limited to 25 posts
```python
from django.conf.urls import url, include
from django.views.generic import ListView, DetailView
from blog.models import Post

urlpatterns = [ url(r'^$', ListView.as_view(queryset=Post.objects.all().order_by("-date")[:25], template_name="blog/blog.html"))]
```

Create templates/blog/blog.html, display posts with date
```
{% extends "personal/header.html" %}
{% block content %}
    {% for post in object_list %}
        <h5>{{ post.date|date:"Y-m-d" }}<a href="/blog/{{post.id}}">  {{ post.title }}</a></h5>
    {% endfor %}
{% endblock %}
```

## Part 8: Database and migrations
[Link to video](https://www.youtube.com/watch?v=hU5QSQt6yQc)

Always think of migrations when using models and DBs
  
First run a migrate on mysite
```
$ python3 manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying sessions.0001_initial... OK
```

### Steps to migrate:
1. Makemigrations: look at the models that need migration and create the migrations
```
$ python3 manage.py makemigrations
Migrations for 'blog':
  blog/migrations/0001_initial.py
    - Create model Post

```

2. Create the database using sqlmigrate command  
   The result is the sql command to create the blog_post table
```
$ python3 manage.py sqlmigrate blog 0001
BEGIN;
--
-- Create model Post
--
CREATE TABLE "blog_post" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "title" varchar(140) NOT NULL, "body" text NOT NULL, "date" datetime NOT NULL);
COMMIT;
```

3. Finally, run the migration
```
$python3 manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, blog, contenttypes, sessions
Running migrations:
  Applying blog.0001_initial... OK
```

Start the app on the server
```
$ cd mysite
$ python3 manage.py runserver
```

Browse to the URL http://localhost:8000/, the blog link shows a blank page  
This is good, since it verifies it went thru all the blog app logic  
A blog page will display only after blog posts are created using admin.

## Part 9: The admin app
[Link to video](https://www.youtube.com/watch?v=xVAbW1G64bM)

Before the admin app can be used, need to create the admin, super-user.
```
$ python3 manage.py createsuperuser
```

Start the server and browse to the URL http://localhost:8000/admin,
now you can login to the admin account.

Next, we let admin know about post, add the post model to mysite/blog/admin.py
```python
from django.contrib import admin
from blog.models import Post

admin.site.register(Post)
```

Now browsing to the admin page will show Posts under BLOG,  
and we can add or edit the posts.


