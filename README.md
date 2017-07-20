# Django Tutorial Video Series on youtube

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
$ cd mysite/webapp
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
$ cd mysite/personal
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