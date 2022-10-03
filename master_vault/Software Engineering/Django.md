# Django Overview
![[Pasted image 20220705152728.png]]
## Overview

Django is a back-end framework written in Python. Django is a **high-level framework** meaning that it provides a great deal of functionality for you, but you have to connect the pieces together. You have to learn things the 'django way'. This also means that isn't necessarily any deeper intuition behind things, the only answer may be "that's just how Django does things".

For comparison, [Flask](http://flask.pocoo.org/) is also a Python-based back-end framework, but whereas Django is high-level, Flask is low-level, meaning you're only given the most barebones functionality and have to do everything else yourself. Again, it's a balance of convenience and control.

The core of Django is the [request-response cycle](django_diagram.png). A request is received by the server, it follows a **route**, actives a **view**, which then uses **models** and a **template** to generate a **response**, which is then rendered in the user's browser. The following docs will cover each of these topics in turn, but bear in mind that they're all interdependent.

- Route: a mapping between a URL and a view
- View: a Python function which receives a request (url) and creates a response (html+css+js)
- Template: an HTML file with special syntax for filling in data
- Model: a Python class that parallels a database table

Django applications are contained in a **project** which can have multiple **apps**. Each app has its own routes, views, templates, and models. How you divide up the functionality of the application is up to your discretion, what's important is that it makes sense to you.
## Management Commands

Management commands are executed in a terminal to perform operations on a django project. You can view a full list of the management commands [here](https://docs.djangoproject.com/en/3.2/ref/django-admin/)

| Command | Description |
| ---     | ---         |
| `django-admin startproject myproject` | create a Django project |
| `python manage.py startapp myapp` | create an app |
| `python manage.py runserver` | run the server |
| `python manage.py makemigrations` | stage changes to the database |
| `python manage.py migrate` | apply changes to the database |
| `python manage.py createsuperuser` | create an admin (which has access to the admin panel) |
| `python manage.py collectstatic` | collects static files from each app and puts them into one folder, used for deployment |
| `python manage.py shell` | open an interactive session, often used to do database operations |

### Custom Management Commands

If you need to execute some Python code to perform administrative operations (load data into a database from a file or API, erase saved files, etc), you can write a custom management command. These are executed just like other management commands (`runserver`, `startapp`, `migrate`, etc). To create a custom management command, first create a `management` folder inside your app. Inside of that, create a `commands` folder. Inside of that, create a `mycommand.py`. \

- myproject
  - myproject
  - myapp
    - management
      - commands
        - mycommand.py


Inside your `mycommand.py`, write the following.

```python
from django.core.management.base import BaseCommand

class Command(BaseCommand):

    def handle(self, *args, **options):
        # write the code here
        print('hello!')
```
Now you can execute this function using `python manage.py <command name>`. Any parameters you write after the `<command name>` will be passed to the `handle` function.
## Resources

### General
- [MDN](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django)
- [Wikipedia](https://en.wikipedia.org/wiki/Django_(web_framework))
- [Official Docs](https://docs.djangoproject.com/en/3.2/)
  - [Table of Contents](https://docs.djangoproject.com/en/3.2/contents/)

### Tutorials
- [Official Tutorial](https://docs.djangoproject.com/en/3.2/intro/tutorial01/)
- [Django Girls Tutorial](https://tutorial.djangogirls.org/en/)
- [Real Python](https://realpython.com/tutorials/django/)
- [Simple is Better Than Complex](https://simpleisbetterthancomplex.com/archive/)

### Videos
- [Corey Schafer's Video Series](https://www.youtube.com/watch?v=UmljXZIypDc&list=PL-osiE80TeTtoQCKZ03TU5fNfx2UY6U4p)
- [The Net Ninja's Video Series](https://www.youtube.com/watch?v=n-FTlQ7Djqc&list=PL4cUxeGkcC9ib4HsrXEYpQnTOTZE1x0uc)
- [The Django Book](https://djangobook.com/beginning-django-tutorial-contents/)
- [Traversey Media](https://www.youtube.com/watch?v=e1IyzVyrLSU)
- [Dennis Ivy](https://www.youtube.com/watch?v=4RWFvXDUmjo)

### Forums
- [Official Forum](https://forum.djangoproject.com/)

### Libraries
- [Django Packages](https://djangopackages.org/)
- [A List](https://vsupalov.com/favorite-django-packages-2019/)

### Tools
- [SQLite Browser](http://sqlitebrowser.org/)

  
# Routes
## Overview

Routes connect the **path** part of a URL to a **view**. The routes are stored in a `urls.py` file, which can be found in both the project folder and in each of the apps' folders. Routes are evaluated **in order**: whichever route matches first is the one used. You can visualize Django's routing system as a series of pipes, first the incoming request hits the project's `urls.py`, which the directs it to one of the app's `urls.py`, which then directs it to a view. You can read more about routes [here](https://docs.djangoproject.com/en/3.2/topics/http/urls/) and [here](https://docs.djangoproject.com/en/3.2/ref/urls/).


## Connecting the Project's `urls.py` to the App's `urls.py`

The `include` function allows you to combine the routes of multiple `urls.py` files into one. This is used to connect the project's 'main' `urls.py` to the `urls.py` in each of the apps.

**myproject/urls.py**
```python
from django.urls import path, include
from django.contrib import admin

urlpatterns = [
    # route to admin panel
    path('admin/', admin.site.urls),
    # all routes in 'myapp/urls.py' will be under localhost:8000/mypath/...
    path('mypath/', include('myapp.urls'))
]
```

## Connecting an App's `urls.py` to a View

**myapp/urls.py**
```python
from django.urls import path

from . import views

app_name = 'myapp'
urlpatterns = [
    # localhost:8000/mypath/
    path('', views.index, name='index'),
    # localhost:8000/mypath/about/
    path('about/', views.about, name='about'),
]
```

**myapp/views.py**
```python
from django.http import HttpResponse

def index(request):
    return HttpResponse('you are at the index')

def about(request)
    return HttpResponse('you are at the )
```

## Reverse URL Lookup

The `app_name` in the `urlspy` and the `name=` in each of the path are used to perform a reverse url lookup: [04 Templates - Reverse URL Lookup](04%20-%20Templates.md#reverse-url-lookup).

**myapp/urls.py**
```python
from django.urls import path

app_name = 'myapp' # <-------------
urlpatterns = [
    path('', views.index, name='index'), # <----------
]
```

## Parameters in the Path

You can specify a parameter in your path using `<type:var_name>`, where `type` is the data type of the parameter (e.g. `str`, `int`, etc). See the [views.md](03%20-%20Views.md#path-parameters) file.
# Views
## Overview
**Views** are python functions that do the bulk of the work, they receive the incoming request and return a response. The view can then respond with HTML, JSON, text, etc. An app's views are contained in its `views.py` file. You can read more about views [here](https://docs.djangoproject.com/en/3.2/topics/http/views/) and [here](https://docs.djangoproject.com/en/3.2/ref/request-response/).

```python
from django.http import HttpResponse
def index(request):
    return HttpResponse('hello world!')
```

## Requests

### The Request Object

The request object received by the view contains lots of important information.

- `request.method` tells you which of the HTTP methods was used (GET, POST, etc)
- `request.body` the raw request body, you can also use `request.read()`
- `request.path` path of the requested page, e.g. `"/music/bands/beatles/"`
- `request.GET` dictionary of query parameters
- `request.POST` dictionary of form parameters
- `request.COOKIES` a dictionary of cookies


### Path Parameters

You can specify parameters in the path using a datatype (`int`, `str`) and a name. Those values will then be automatically taken out of the path and passed as parameters to the view function. A common use for these is for a detail view for a record, with the primary key of the record specified in the path.

**urls.py**
```python
from django.urls import path
from . import views
app_name = 'todoapp'
urlpatterns = [
    # e.g. /detail/5/, /detail/23/
    path('detail/<int:todo_item_id>/', views.detail, name='detail')
]
```

**views.py**
```python
from django.shortcuts import get_object_or_404
from .models import TodoItem
def detail(request, todo_item_id):
    # look up the TodoItem with the given id
    todo_item = get_object_or_404(TodoItem, pk=todo_item_id)
    # pass that todo item to the template to be rendered
    return render(request, 'todoapp/detail.html', {'todo_item': todo_item})
```


### Receiving Query Parameters

Query parameters are passed as part of the url and are turned into dictionary-like objects. For example, if the path entered is `/mypath/?myvar=mytext`, we can retrieve the query parameters by name.

```python
def view(request):
    print(request.GET['myvar']) # 'mytext'
```

### Receiving a Form Submission

When a form is submitted to a view, the data in the form is arranged into a dictionary. The `name` attributes of the input elements become the `keys` and the values the user enters into the input elements become the `values`. The view can then use the key to get the values out of the dictionary. For more about forms, check out [Templates - Forms](04%20-%20Templates.md#forms).


**myapp/templates/myapp/mytemplate.html**
```html
<form action="{% url 'myapp:mypathname' %}" method="post">
  <input name="myname"/>
  <button type="submit">submit</button>
</form>
```

**myapp/urls.py**
```python
from django.urls import path

app_name = 'myapp'
urlpatterns = [
    path('mypath/', views.myview, name='mypathname')
]
```

**myapp/views.py**
```python
from django.http import HttpResponse
def myview(request):
    print(request.POST['myname'])
    return HttpResponse('ok')
```


### Receiving JSON

To read JSON data sent via AJAX, you can use the built-in `json` module to read the request's body. This turns the JSON into a Python dictionary.


```javascript
axios({
    method: 'post',
    url: '{% url 'myapp:mypathname' %}',
    data: {foo: 'bar', hello: 'world'}
}).then(function(response) {
    console.log(response.data)
})
```

```python
import json
def postdata(request):
    data = json.loads(request.body)
    print(data)
    return HttpResponse('ok')
```


## Responses

### Responding with a String / Raw HTML

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse('Hello World!')

def fruits(request):
    fruits = ['apples', 'bananas', 'plums']
    html = '<ul>'
    for fruit in fruits:
        html += '<li>' + fruit + '</li>'
    html += '</ul>'
    return HttpResponse(html)
```

### Responding with a Template

To render a template, use the `render` function. The first parameter is the original request, the second is the location of the template, and the third is a dictionary containing the variables to be rendered.

```python
from django.shortcuts import render
from .models import TodoItem
def index(request):
    todo_items = TodoItem.objects.all()
    context = {'todo_items': todo_items}
    return render(request, 'todos/index.html', context)
```

### Responding with JSON

To respond with JSON, you can just pass a dictionary to a `JsonResponse`.

**myapp/urls.py**
```python
from django.http import JsonResponse
def myview(request):
    data = {'foo': 'bar', 'hello': 'world'}
    return JsonResponse(data)
```

You can then send an HTTP request to this view via ajax.

**myapp/templates/myapp/index.html**
```javascript
axios{
    method: 'get',
    path: "{% url 'myapp:myview' %}"
}).then(function(response) {
    console.log(response.data)
})
```


### Redirecting

To redirect, you can use the HttpResponseRedirect class. You can redirect to a full url `http://mysite.com/` or if you put a path `/mypath/`, django will add it to the current domain `http://localhost:8000/mypath/`. This can cause issues (`reverse('google.com')` will redirect to `localhost:8000/google.com`.

```python
from django.http import HttpResponseRedirect
def myview(request):
    ...
    return HttpResponseRedirect('/mypath/')
```
```python
def myview(request):
    ...
    return HttpResponseRedirect('http://mysite.com/')
```

It's also best to use the [reverse](https://docs.djangoproject.com/en/3.2/ref/urlresolvers/#reverse) function to look up the url using the name rather than hard-coding it. This does the same reverse url redirect as the template: [04 Template - Reverse URL Lookup](04%20-%20Templates.md#reverse-url-lookup)

```python
from django.http import HttpResponseRedirect
from django.urls import reverse
def add(request):
    return HttpResponseRedirect(reverse('myapp:myview'))
```

You can also use the [redirect](https://docs.djangoproject.com/en/3.2/topics/http/shortcuts/#redirect) function. The difference is explained [here](https://stackoverflow.com/questions/13304149/what-the-difference-between-using-django-redirect-and-httpresponseredirect).

```python
from django.shortcuts import redirect
def redirect(request):
    return redirect('http://www.mozilla.org/')
```

# Templates
## Overview

Templates are like blueprints for your HTML pages. They contain plain HTML/CSS/JavaScript, but also additional syntax for generating HTML/CSS/JavaScript using variables from your Python view. You can read more about Templates [here](https://docs.djangoproject.com/en/3.2/topics/templates/) and [here](https://docs.djangoproject.com/en/3.2/ref/templates/builtins/)


## Passing a Value to the Template

The variable names referred to inside the template must be defined in the data context (a dictionary) passed to the `render` function inside the view.




## Template Rendering Syntax

### Rendering a Value

You can render a value in a template using `{{}}`.


**views.py**
```python
from django.shortcuts import render
def index(self)
    return render(request, 'myapp/index.html', {'name': 'Jane'})
```
**index.html**
```html
<span>Hello, {{name}}!</span>
```

### Conditionals

What you put inside an `if` block will only be rendered if the condition is true.

**views.py**
```python
from django.shortcuts import render
def index(self)
    return render(request, 'myapp/index.html', {'temperature': random.randint(50, 100)})
```
**index.html**
```html
{% if temperature < 60 %}
<span>cold</span>
{% elif temperature < 80 %}
<span>warm</span>
{% else %}
<span>hot</span>
{% endif %}
```

### Looping

Whatever you put inside the `for` block will be repeated for each iteration of the loop. For example, we can build a list of items.

**views.py**
```python
from django.shortcuts import render
def index(self)
    return render(request, 'myapp/index.html', {'fruits': ['apples', 'bananas', 'pears']})
```
**index.html**
```html
<ul>
    {% for fruit in fruits %}
    <li>{{ fruit }}</li>
    {% endfor %}
</ul>
```


## Reverse URL Lookup

In order for Django to find the proper path when rendering the template, the app's `urls.py` must contain the variable `app_name`, e.g. `app_name = 'todos'`. The `name` given in `urls.py` and the actual `path` can be different. To keep things simple, use consistent names.


**urls.py**
```python
from django.urls import path
from . import views
app_name = 'todos'
urlpatterns = [
    path('', views.index, name='index'),
    path('add/', views.add, name='add')
]
```

**index.html**
```html
<form action="{% url 'todos:add' %}" method="post">
    {% csrf_token %}
    <input type="text" name="todo_text"/>
    <button type="submit">+</button>
</form>
```


## Static Files

To load static files into a page, create a folder in your app called `static`. Inside that folder, create a folder with the same name as your app (just as you did with templates). In your template, you then must add `{% load static %}` before you load your static file.

- [Managing Static Files](https://docs.djangoproject.com/en/3.2/howto/static-files/)
- [Polls Tutorial: Part 6](https://docs.djangoproject.com/en/3.2/intro/tutorial06/)

```html
{% load static %}
<img src="{% static 'myapp/example.jpg' %}" alt="My image"/>
```

## Template Inheritance: `block` and `extend`

You can have one template 'inherit' from another, meaning the child template's content will be included inside the parent. You can accomplish this by putting a `{% block content %} / {% endblock %}` in the parent and an `{% extends '<app name>/<parent>.html' %}` in the child. This is useful if your header/footer/menus are consistent across multiple pages and you don't want to repeat the HTML. You can read more about template inheritance [here](https://tutorial.djangogirls.org/en/template_extending/).

In the example below, `base.html` contains the header and footer. Two pages, `index.html` and `detail.html` inherit from `base.html`. The contents of each `{% block %}` in the child templates are used to fill the corresponding block in the parent when the template is rendered.


**base.html**
```html
<html>
    <head>
        <title>Document</title>
    </head>
    <body>
        <header>
            <h1>{% block title %}{% endblock %}</h1>
        </header>
        <main>
            {% block content %}
            {% endblock %}
        </main>
        <footer>
            <span>&copy; me 2020</span>
        </footer>
    </body>
</html>
```

**index.html**
```html
{% extends 'myapp/base.html' %}
{% block title %}Home{% endblock %}
{% block content %}
<p>this is the page content for the index page</p>
{% endblock %}
```

**detail.html**
````html
{% extends 'myapp/base.html' %}
{% block title %}Details{% endblock %}
{% block content %}
<p>this is content for the detail page</p>
{% endblock %}
````

## Filters

Filters allow you to change how values are rendered in the template.
# Forms
## Overview
A `form` is an HTML element that can transmit data from the front-end (client) to the back-end (server). Read more about forms [here](../../2%20Flask%20+%20HTML%20+%20CSS/docs/11%20HTML%20Forms.md). There are 5 important parts to a form:

1. The `action` is the path or url to which the form's data will be submitted.
2. The `method` is the HTTP method to send the request in (POST, GET).
3. The `input` elements inside a form need name attributes, which will be used to retreive the data on the back-end.
4. The `<button type="submit">` or `<input type="submit">` will submit the form when clicked.
5. The `{% csrf_token %}` will insert a token that protects against [Cross-site request forgeries](https://en.wikipedia.org/wiki/Cross-site_request_forgery).

```html
<form action="{% url 'contacts:save_contact' %}" method="post">
    {% csrf_token %}
    <input type="text" name="first_name"/>
    <input type="text" name="last_name"/>
    <button type="submit">save</button>
</form>
```

Django will take the key-value pairs from the form data in the request and put them into a dictionary-like object `request.POST`. You can then access those values from the view using the value of the `name` attribute as a key.


```python
def save_contact(request): # a view for receiving a form submission
    print(request.POST) # verify we received the form data
    first_name = request.POST['first_name'] # get the value the user entered into the 'first name' field
    last_name = request.POST['last_name'] # get the value the user entered into the 'last name' field
    contact = Contact(first_name=first_name, last_name=last_name) # create an instance of our model
    contact.save() # save a new row to the database
    ...
```

## Django Forms

Django has a special Form class to make the creation of forms easier. These also do input validation on the front-end and the back-end for you. You can read in the official docs: [Working with Forms](https://docs.djangoproject.com/en/3.2/topics/forms/), [Forms API](https://docs.djangoproject.com/en/3.2/ref/forms/api/#django.forms.Form). You can put your forms in a `forms.py` inside your app.
**forms.py**
```python
from django import forms
class ContactForm(forms.Form):
    contact_name = forms.CharField(label='Contact Name', max_length=100)
    contact_age = forms.IntegerField(label='Contact Age')
```

**views.py**
```python
from django.shortcuts import render
from django.http import HttpResponseRedirect
from .forms import ContactForm
def index(request):
    if request.method == 'POST': # receiving a form submission
        # create an instance of our form from the form data
        form = ContactForm(request.POST)
        if form.is_valid():
            # get the data out of the form
            contact_name = form.cleaned_data['contact_name']
            contact_age = form.cleaned_data['contact_age']
            # create an instance of our model from the data
            contact = Contact(name=contact_name, age=contact_age)
            # save a new record to the database
            contact.save()
            # create a new blank form for the template
            form = ContactForm()
        # if the form is invalid, we just send it back to the template
    else: # receiving a GET request
        form = ContactForm() # create a new blank form
    return render(request, 'contacts/index.html', {'form': form}) # pass the form to the template
```

**index.html**
```html
<form action="{% url 'contacts:index' %}" method="post">
    {% csrf_token %}
    {{ form }}
    <input type="submit" value="Submit" />
</form>
```

### The ModelForm Class
ModelForms allow us to generate a form directly from a model. You can read more about ModelForms in the [official docs](https://docs.djangoproject.com/en/3.2/topics/forms/modelforms/).
**models.py**
```python
from django.db import models
class Contact(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()
```

**forms.py**
```python
from django.forms import ModelForm
from .models import Contact
class TodoForm(ModelForm):
    class Meta:
        # the model to associate with the form
        model = Contact
        # a list of all the models' fields you want in the form
        # fields = ['text']
        # or just use all of them
        fields = '__all__'
```

**views.py**
```python
from django.shortcuts import render
from django.http import HttpResponseRedirect
from .forms import ContactForm
def index(request):
    if request.method == 'POST': # receiving a form submission
        form = ContactForm(request.POST)
        if form.is_valid():
            form.save() # save the todo item associated with the form
            form = ContactForm() # create a new blank form
        # if the form is invalid, we just send it back to the template
    else: # receiving a GET request
        form = ContactForm() # create a new blank form
    return render(request, 'contacts/index.html', {'form': form}) # pass the form to the template
```

**index.html**
```html
<form action="{% url 'contacts:index' %}" method="post">
    {% csrf_token %}
    {{ form }}
    <input type="submit" value="Submit" />
</form>
```

### Using Forms with CSS Frameworks

CSS Frameworks like Boostrap and Materialize have specific ways their forms are structures, and don't work with the default forms very well. [Crispy forms](https://django-crispy-forms.readthedocs.io/en/latest/) allow you to better control how forms are rendered.

- [tutorial on using bootstrap w/ django forms](https://simpleisbetterthancomplex.com/tutorial/2018/08/13/how-to-use-bootstrap-4-forms-with-django.html)
- [library for using materialize w/ django forms](https://pypi.org/project/crispy-forms-materialize/)

# Models
## Overview

Models are Python classes that parallel tables in the database. The ORM (object-relational mapping) manages this dual representation, translating statements in Python to queries on the database. You can read more about models [here](https://docs.djangoproject.com/en/3.2/topics/db/models/), and more about the ORM [here](https://docs.djangoproject.com/en/3.2/ref/models/querysets/). For ORM practice, check out the [Polls Tutorial - Part 2](https://docs.djangoproject.com/en/3.2/intro/tutorial02/).

Database tables are like spreadsheets: they have headers and rows. Tables can also be thought of as Python classes, where the headers are class attributes, and the rows are class instances. All models are automatically given an `id` field as a primary key, which is used to uniquely identifies a row.

| id | email_address | first_name | last_name |
| --- | --- | --- | --- |
| 1 | wendy@gmail.com | Wendy | Carson |
| 2 | alyssa@gmail.com | Alyssa	 | Lyons |
| 3 | brian@gmail.com | Brian | Barber |

```python
from django.db import models

# our contact model
class Contact(models.Model):
    email_address = models.CharField(max_length=200)
    first_name = models.CharField(max_length=200)
    last_name = models.CharField(max_length=200)

# get the record with id=1 from the database, the first row
contact = Contact.objects.get(id=1)
# access the column value as a class attribute
print(contact.first_name) # Wendy

# create a new instance of our model, creating the third row
contact_new = Contact(first_name='Brian', last_name='Barber', email='brian@gmail.com')
contact_new.save() # save it to the database
```

## Field Types

The fields of a model create represent both the attribute of a class and the column of a table. You can read more about the field types [here](https://docs.djangoproject.com/en/3.2/ref/models/fields/). Below are some of the common fields used with a model.

- `BooleanField` represents a boolean (true/false) value
- `IntegerField` represents an integer
- `FloatField` represents a floating-point number
- `CharField` represents a string, requires `max_length` parameter indicating the number of characters
- `TextField` like `CharField` but has unlimited length
- `DateTimeField` represents a datetime (more [here](https://docs.djangoproject.com/en/3.2/topics/i18n/timezones/))
- `OneToOneField` represents a [one-to-one relationship](https://docs.djangoproject.com/en/3.2/topics/db/examples/one_to_one/)
- `ForeignKey` represents a [many-to-one relationship](https://docs.djangoproject.com/en/3.2/topics/db/examples/many_to_one/)
- `ManyToManyField` represents a [many-to-many relationship](https://docs.djangoproject.com/en/3.2/topics/db/examples/many_to_many/)


### Blankable Fields

Fields that are marked `blank=True` allow the user to insert a blank value in the admin panel, which will result in a blank string for a `CharField` or `TextField`, or `null` for other field types.

```python
from django.db import models
class Contact(models.Model):
    ...
    favorite_color = models.CharField(blank=True) # the user can save a blank string in the admin panel
```


### Nullable Fields

Fields that are marked `null=True` are 'nullable', meaning they can have a null value. In Python, the attributes of the model will have a value of `None`. To save records with `null` value from the admin panel, you must also add `blank=True`. [more info] (https://www.geeksforgeeks.org/nulltrue-django-built-in-field-validation/)

```Python
from django.db import models

class TodoItem(models.Model)
    ...
    date_completed = models.DateTimeField(null=True, blank=True)
```

### Default Values
You can specify a default value for a field by adding `default=value`. That way, you can leave the value out when creating and saving an instance.

```python
from django.db import models

class BlogPost(models.Model):
    text = models.CharField()
    upvotes = models.IntegerField(default=0)
```

```python
blog_post = BlogPost(text="Lorem Ipsum") # no need to specify age, it will default to 0
blog_post.save()

blog_post2 = BlogPost(text="Delorum Est", upvotes=3) # we can specify if needed
blog_post2.save()
```
## Database Relationships

The three types of database relationships: one-to-one, many-to-one, and many-to-many. The `id` field of a table is called the **primary key** because it uniquely identifies a row. When another table contains a reference to that `id` field, it's called a **foreign key**.

### Many-to-One

A many-to-one relationship means that for every row in table A, there may be multiple rows in table B connected to it. An example might be between a [mother and her children](https://upload.wikimedia.org/wikipedia/commons/thumb/2/26/CPT-Databases-OnetoMany.svg/460px-CPT-Databases-OnetoMany.svg.png). A mother may have multiple children, but any child only has one mother. You can read more about many-to-one relationships [here](https://docs.djangoproject.com/en/2.1/topics/db/examples/many_to_one/).

In the following example, `city_id` on `Contact` is a **foreign key**, `id` on `Contact` and `id` on `City` are **Primary Keys**. This is an example of a **many-to-one relationship**.

**Contacts**
| id | first_name | last_name | email_address | city_id |
| --- | --- | --- | --- | --- |
| 1 | Wendy | Carson | wendy@gmail.com | 1 |
| 2  | Alyssa | Lyons | alyssa@gmail.com | 1 |
| 3  | Brian | Barber | brian@gmail.com | 2 |

**Cities**
| id | name |
| --- | --- |
| 1 | Portland |
| 2 | Eugene |

```python
from django.db import models

class City(models.Model):
    name = models.CharField(max_length=200)

    def __str__(self):
        return self.name

class Contact(models.Model):
    first_name = models.CharField(max_length=200)
    last_name = models.CharField(max_length=200)
    email_address = models.CharField(max_length=200)
    city = models.ForeignKey(City, on_delete=models.CASCADE, related_name='contacts')

    def __str__(self):
        return self.first_name + ' ' + self.last_name
```

Notice the `related_name` on the `ForeignKey` field. This controls how we refer to the list of all the `Contact`s for a given `City`. By default Django will take model name, make it lowercase, and add an `_set`, so if we did not specify `related_name` here it would be `contact_set`, but because we did, it will be `contacts`.

```python
contact = Contact.objects.get(first_name='Alyssa')
# only one city per contact
print(contact.city.name) # Portland

city = City.objects.get(name='Portland')
# multiple contacts for a given city
contacts = city.contacts.all()
print(contacts) # Wendy, Alyssa
```
### One-to-One
A one-to-one relationship means that for every row in table A, there will be a single corresponding row in table B. An example might be between [counties and capital cities](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f7/CPT-Databases-OnetoOne.svg/460px-CPT-Databases-OnetoOne.svg.png). A country only has one capital. A capital only pertains to one country. You can read more about one-to-one relationships [here](https://docs.djangoproject.com/en/3.2/topics/db/examples/one_to_one/). Normally a one-to-one relationship is unnecessary, because one could just take the fields from both models and put them onto one model. But you may have to associate new fields with an old model without changing the old model, or need to restrict access to certain data [more info](https://stackoverflow.com/questions/25206447/when-to-use-one-to-one-relationships-in-django-models).

**Capital**
| id | name |
| --- | --- |
| 1 | Washington DC |
| 2 | Mexico City |
| 3 | Ottawa |

**Country**
| id | name |
| --- | --- |
| 1 | The United States |
| 2 | Mexico |
| 3 | Canada |
```python
from django.db import models

class Capital(models.Model):
    name = models.CharField(max_length=200)

    def __str__(self):
        return self.name

class Country(models.Model):
    name = models.CharField(max_length=200)
    capital = models.OneToOneField(Capital, on_delete=models.CASCADE, related_name='country')

    def __str__(self):
        return self.name
```

```python

capital = Capital.objects.get(name='Washington DC')
print(capital.country.name) # The United States

country = Country.objects.get(name='The United States')
print(country.capital.name) # Washington DC

# create a new capital
capital_city = Capital(name='Canberra')
capital_city.save()

# create a new city
country = Country(name='Australia', capital=capital_city)
country.save()
```

### Many-to-Many
An example of many-to-many relationships might be between [authors and books](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c4/CPT-Databases-ManytoMany.svg/460px-CPT-Databases-ManytoMany.svg.png). One book may have multiple authors. One author may have multiple books. A many-to-many relationship can be created in Django using a [ManyToManyField](https://docs.djangoproject.com/en/3.2/topics/db/examples/many_to_many/). To maintain such a relationship in SQL, Django creates a [junction table](https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/Databases-ManyToManyWJunction.jpg/800px-Databases-ManyToManyWJunction.jpg) with two many-to-one relationships.

**Books**
| id | title |
| --- | --- |
| 1 | Good Omens |
| 2 | The Odyssey |
| 3 | The Illiad |

**Authors**
| id | name |
| --- | --- |
| 1 | Homer |
| 2 | Terry Pratchett |
| 3 | Neil Gaiman |

**Book-Authors**
| id | book_id | author_id |
| --- | --- | --- |
| 1 | 2 | 1 |
| 2 | 1 | 2 |
| 3 | 1 | 3 |

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=50)

    def __str__(self):
        return self.name

class Author(models.Model):
    name = models.CharField(max_length=200)
    books = models.ManyToManyField(Book, related_name='authors')

    def __str__(self):
        return self.title
```

```python
book = Book.objects.get(title='Good Omens')
authors = book.authors.all() # all the authors for a book
print(authors) # Terry Pratchett, Neil Gaiman

author = Author.objects.get(name='Homer')
books = author.objects.all() # all the books for an author
print(books) # The Odyssey, The Illiad
```


### The On-Delete Parameter: `on_delete`

The `on_delete` parameter lets you control what to do with other rows when a connected row is deleted. You can read more about `on_delete` [here](https://docs.djangoproject.com/en/3.2/ref/models/fields/#arguments). The important options are:

- `CASCADE` deleted this row when the other is deleted
- `PROTECT` throws an exception when the other is deleted, this forces the developer re-assign the relationship when they want to delete a row
- `SET_NULL` sets the field containing the relationship to null (the field must also be nullable)
- `SET_DEFAULT` sets the field containing the relationship to its default value (a default must be specified)

For example, consider the following models and data:

**Contacts**
| id | first_name | last_name | email_address | city_id |
| --- | --- | --- | --- | --- |
| 1 | Wendy | Carson | wendy@gmail.com | 1 |
| 2  | Alyssa | Lyons | alyssa@gmail.com | 1 |
| 3  | Brian | Barber | brian@gmail.com | 2 |

**Cities**
| id | name |
| --- | --- |
| 1 | Portland |
| 2 | Eugene |

```python
from django.db import models

class City(models.Model):
    name = models.CharField(max_length=200)

    def __str__(self):
        return self.name

class Contact(models.Model):
    first_name = models.CharField(max_length=200)
    last_name = models.CharField(max_length=200)
    email_address = models.CharField(max_length=200)
    city = models.ForeignKey(City, on_delete=models.CASCADE, related_name='contacts')

    def __str__(self):
        return self.first_name + ' ' + self.last_name
```

Note the `on_delete=models.CASCADE`, this means if we delete a `City`, all the `Contact`s associated with that `City` will automatically be deleted as well.

```python
city = City.objects.get(name='Portland')
city.delete() # also deletes the Contacts Wendy and Alyssa
```

###  The Related-Name Parameter: `related_name`

The `related_name` parameter controls what the name of the related class's attribute is. For example, consider the following models and data:

**Contact**
| id | first_name | last_name | email_address | city_id |
| --- | --- | --- | --- | --- |
| 1 | Wendy | Carson | wendy@gmail.com | 1 |
| 2  | Alyssa | Lyons | alyssa@gmail.com | 1 |
| 3  | Brian | Barber | brian@gmail.com | 2 |

**Cities**
| id | name |
| --- | --- |
| 1 | Portland |
| 2 | Eugene |

```python
from django.db import models

class City(models.Model):
    name = models.CharField(max_length=200)

    def __str__(self):
        return self.name

class Contact(models.Model):
    first_name = models.CharField(max_length=200)
    last_name = models.CharField(max_length=200)
    email_address = models.CharField(max_length=200)
    city = models.ForeignKey(City, on_delete=models.CASCADE, related_name='contacts')

    def __str__(self):
        return self.first_name + ' ' + self.last_name
```

Notice the `related_name` on the `ForeignKey` field. This controls how we refer to the list of all the `Contact`s for a given `City`. By default Django will take model name, make it lowercase, and add an `_set`, so if we did not specify `related_name` here it would be `contact_set`, but because we did, it will be `contacts`.

```python
contact = Contact.objects.get(first_name='Alyssa')
# only one city per contact
print(contact.city.name) # Portland

city = City.objects.get(name='Portland')
# multiple contacts for a given city
contacts = city.contacts.all()
print(contacts) # Wendy, Alyssa
```


## ORM Operations

The ORM 'object relational mapping' provides functions in Python that perform operations on the database. To read more about ORM operations, look [here](https://docs.djangoproject.com/en/3.2/topics/db/queries/). Note that `__init__`, `get`,  and `filter` take `**kwargs` (which turns named parameters into a dictionary), whereas `order_by` takes `*args` (which turns arguments into a list).

### Example Models and Data

**Contact**
| id | first_name | last_name | email_address | city_id |
| --- | --- | --- | --- | --- |
| 1 | Wendy | Carson | wendy@gmail.com | 1 |
| 2  | Alyssa | Lyons | alyssa@gmail.com | 1 |
| 3  | Brian | Barber | brian@gmail.com | 2 |
| 2  | Wendy | Clark | alyssa@gmail.com | 1 |

**Cities**
| id | name |
| --- | --- |
| 1 | Portland |
| 2 | Eugene |

```python
from django.db import models

class Contact(models.Model):
    first_name = models.CharField(max_length=200)
    last_name = models.CharField(max_length=200)

    def __str__(self):
        return self.first_name + ' ' + self.last_name
```


### Creating a Record

We create an instance of our model by invoking the class's initializer, which takes `kwargs`.

```python
contact = Contact(first_name='Alena', last_name='Deacon')
contact.save()
```

### Getting a Record

We can get a particular row using `get()` and passing the values of one or more attributes.

```python
# get a record using a single attribute
contact = Contact.objects.get(id=1)
print(contact) # Wendy Carson

# get a record using two attributes
contact = Contact.objects.get(first_name='Wendy', last_name='Carson')
print(contect) # Wendy Carson
```

An exception will occur if a record is not found. A safer way is to use `filter` (which gives us a list of matches) and `first` (which gives us the first result if there are any, and `None` if there isn't).

```python
contact = Contact.objects.filter(first_name='Alena').first()
print(contact) # None
```


### Updating a Record

We can update a record by assigning values to its attributes and saving.

```python
contact = Contact.objects.get(id=1)
contact.first_name = 'Cindy'
contact.save()
```


### Get All Rows

To get all the rows in a table use `all()`.

```python
contacts = Contact.objects.all()
print(contacts) # Wendy Carson, Alyssa Lyons, Brian Barber
```

### Check if a Record Exists

We can use `filter` (which gives us a list of matches) and `exists` (which return `True` if there are records, and `False` if there aren't).


```python
if Contact.objects.filter(first_name='Cindy').exists():
    ...
```

### Filter Rows

We can use `filter()` to get a list of records.

```python
contacts = Contact.objects.filter(first_name='Wendy')
print(contacts) # Wendy Carson, Wendy Clark
```

### Specify an Order

To specify an order, use `order_by`, which takes any number of strings containing the names of the fields to sort by. By default sort is ascending, use a negative symbol `-` to sort in the descending order.

```python
# sort by last name in ascending order
# then first name in descending order
contacts = Contact.objects.order_by('last_name', '-first_name')
```


### Specify the Number of Records to Return

To limit the number of items returned, use slicing.

```python
# only get the first 5 results
contacts = Contact.objects.all()[:5]
```

### Get the Number of Records


```python
contacts = Contact.objects.all().count()
```

### Advanced ORM

To filter variables by whether or not a field is null, use `<field_name>__isnull`
```python
completed_items = TodoItem.objects.filter(date_completed__isnull=False)
```
# User Management
## Overview
Many web applications have the ability for a user to 1) create an account, 2) log into and out of that account, and 3) view pages that are only accessible to logged-in users. For more info, read [here](https://docs.djangoproject.com/en/3.2/topics/auth/) and [here](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Authentication).


## Users, Groups, and Permissions

Start by looking at the users section of the admin interface. Here you can create users, groups, and assign permissions. A group is a collection of users which you can add and remove permisions from, so you don't have to go to each user to change their permissions. Django has many built-in permissions, but you can also define your own. For more information about these, look [here](https://docs.djangoproject.com/en/3.2/ref/contrib/auth/).

## Creating & Editing Users

You can create users programmatically using the 'create_user' function, which automatically creates a user and saves it. It's important to note that Django does not save passwords in 'plain text', only a hash of the password. This means you cannot retrieve a user's password, only check if the password you have is correct by putting it through the same hashing algorithm. You can read more about how Django manages passwords [here](https://docs.djangoproject.com/en/3.2/topics/auth/passwords/).

```python
from django.contrib.auth.models import User
user = User.objects.create_user('jane', 'jane@gmail.com', 'janespassword')
```

You can also create users from within the admin panel, by clicking 'add' next to 'Users' under 'AUTHENTICATION AND AUTHORIZATION'.


## Changing Passwords

You can change a user's password using `set_password` in Python or `changepassword` in the terminal. You can also change a user's password in the admin panel.

```python
from django.contrib.auth.models import User
user = User.objects.get(username='jane')
user.set_password('newpassword')
user.save()
```

```
python manage.py changepassword jane
```

## Authentication, Login, & Logout

To log a user in, we can use a form to post the username and password to a view. The `authenticate` function verifies their username and password are correct. If they are, it returns the user. If they aren't, it returns `None`. After verifying that the username and password match, we can log a user in using `login`.

```python
from django.contrib.auth import authenticate, login

def mylogin(request):
    # retrieve the variables from the form submission
    username = request.POST['username']
    password = request.POST['password']
    user = authenticate(request, username=username, password=password)
    if user is not None:
        login(request, user)
        # redirect to a success page
    else:
        # return an 'invalid login' error message
```

Logging out a user is even simpler.

```python
from django.contrib.auth import logout

def logout_view(request):
    logout(request)
    # redirect to a success page.
```

## Authorization

### is_authenticated

In other views, we can check if a user is logged in by checking the `is_authenticated` field.

```python
def otherview(request):
    if request.user.is_authenticated:
        # do something for authenticated users.
    else:
        # do something else for anonymous users.
```
### has_perm

If you want to restrict access to users with particular permissions, use `has_perm`.

```python
def otherview(request):
    if request.user.has_perm('blog.add_comment'):
        # do something for users with this permision
    else:
        # do something for everyone else
```

### @login_required

Django comes with a built-in decorator which can check if a user is logged in. If the user is logged in, the execution of the view coninues unabated. If not, the user will be redirected to [settings.LOGIN_URL](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-LOGIN_URL). You can read more [here](https://docs.djangoproject.com/en/3.2/topics/auth/default/#the-login-required-decorator).

```python
from django.contrib.auth.decorators import login_required

@login_required
def my_view(request):
    ...
```

### @permission_required

Like `@login_required`, if this fails, the user will be redirected to [settings.LOGIN_URL](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-LOGIN_URL).
You can read more [here](https://docs.djangoproject.com/en/3.2/topics/auth/default/#the-permission-required-decorator).

```python
from django.contrib.auth.decorators import permission_required

@permission_required('polls.can_vote')
def my_view(request):
    ...
```

### @user_passes_test(f)

The `@users_passes_test` decorator takes a function which is given a user. That function can then return `True` or `False` whether that user should be allowed in. Like the others, if this fails, the user will be redirected to [settings.LOGIN_URL](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-LOGIN_URL). You can read more [here](https://docs.djangoproject.com/en/3.2/topics/auth/default/#limiting-access-to-logged-in-users-that-pass-a-test).


```python
from django.contrib.auth.decorators import user_passes_test

def email_check(user):
    return user.email.endswith('@example.com')

@user_passes_test(email_check)
def my_view(request):
    ...
```


## Extending the User Model

The built-in user model only has a few fields (username, email, first name, last name). If you'd like to associate more information with the user (phone number, location, profile image), there are two main strategies.

### Inherit from AbstractUser

We can create a custom user model by inheriting from `AbstractUser`. You should create one **when you start a project**. It's much more difficult to change once you already have users in your database. You can read more [here](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#auth-custom-user).


**models.py**
```python
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    phone_number = models.CharField(max_length=20)
    ...
```

**settings.py**
```python
AUTH_USER_MODEL = 'myapp.User'
```

**admin.py**
```python
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import User

admin.site.register(User, UserAdmin)
```



### Separate UserProfile Model

Another option is to have a separate model with a one-to-one field connected to the built-in user model.

```python
from django.contrib.auth.models import User

class UserProfile(models.Model):
    user = models.OneToOneField(User, on_delete=models.PROTECT, related_name='user_profile')
    phone_number = models.CharField(max_length=20)
    ...
```

The caveat of this approach is that accessing that information via the ORM takes an extra step, which is a little messy.

```python

def index(request):
    print(request.user.user_profile.phone_number)
```


## Managing Groups and Permissions
The `User` model has two many-to-many fields: groups and permissions. You can access these on the User object using the ORM. Note that `user_permissions` only include permissions assigned to that individual user, and not permissions that user has as part of a group. However, `has_perm` will check if the given permission is amony the group.

- `user.groups.set([group_list])` set the groups
- `user.groups.add(group, group, ...)` add to a group
- `user.groups.remove(group, group, ...)` remove from group
- `user.groups.clear()` remove from all groups
- `user.user_permissions.set([permission_list])` set permissions
- `user.user_permissions.add(permission, permission, ...)` add permissions
- `user.user_permissions.remove(permission, permission, ...)` remove permissions
- `user.user_permissions.clear()` clear all user permissions
- `user.has_perm(permission_code)` check if a user has a permission, either in user_permissions or in one of their groups

```python
from django.contrib.auth.models import User, Group, Permission

# add a user to a group
group = Group.objects.get(name='commenters')
user.groups.add(group)
user.save()

# add a permission to a group
permission = Permission.objects.get(codename='change_comment')
group.permissions.add(permission)
group.save()

# check if a user has a permission
if user.has_perm('blog.add_comment'):
    # ...


# check if a user is in a group
if user.groups.filter(name='commenters').exists():
    # ...
```
# Media Files
## Overview

Web applications often allow users to upload files. This document covers how to allow users to upload files and save them alongside our application on a server. If you're using cloud hosting, you may want to look at alternative ways of storing files which separate the files from the application. Look at the official docs for more info: [File Uploads](https://docs.djangoproject.com/en/3.2/topics/http/file-uploads/), [ImageField](https://docs.djangoproject.com/en/3.2/ref/models/fields/#django.db.models.ImageField), [FileField](https://docs.djangoproject.com/en/3.2/ref/models/fields/#django.db.models.FileField). You may also look at the different [mime types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types).


## 1: Specify the Save Location

In your project's `settings.py`, set the following variables.

```python
MEDIA_URL = '/uploaded_files/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'uploaded_files')
```

## 2: Set up the Model

Given the settings shown here, files will be saved to `<project name>/uploaded_files/images`.

```python
from django.db import models
class MyModel(models.Model):
    my_image = models.ImageField(upload_to='images/')
```

## 3: Add a Route to Access the Files

In your project's `urls.py`, add the following line at the bottom. This will give the user the ability to access the file statically. Note that there's built-in access restriction, so anyone with a valid link will be able to view and download the associated file.

```python
from django.urls import path
from django.conf import settings
from django.conf.urls.static import static
urlpatterns = [
   ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

## 4: Test

At this point, it's best to register your model with the admin panel, go to your admin panel, upload a file, make sure that the file appears in the directory you expected and that the link to the file works.

## 5: Render an Image

Once you have a few instances of your model saved, you can use the `url` property on the `ImageField` to render the image inside the template or create a link to it.

```html
<!-- display the image-->
<img src="{{image.url}}"/>

<!-- create a link -->
<a href="{{image.url}}">{{image.name}}</a>
```

## 6: Put a Form on your Page

If we want to let users upload images, we can create a form with `input` `type="file"`. Notice the `enctype` on the `form`.

```html
<form action="<myurl>" method="POST" enctype="multipart/form-data">
    <!-- ... -->
    <input type="file" name="my_image" accept="image/*" required>
    <button type="submit">submit</button>
</form>
```

## 7: Add a View to Receive the Form and Save the Model

```python
from .models import MyModel
def upload_image(request):
    my_image = request.FILES['my_image']
    model = MyModel(..., my_image=my_image)
    model.save()
```
# Class-Based Views
## Overview

The caveat with class-based views is that while they provide much for you, you must know how to customize them, they reflect the balance of convenience and control you find elsewhere in programming.

- [Reference Documentation](https://docs.djangoproject.com/en/3.2/ref/class-based-views/)
- [Introduction to Class-Based Views](https://docs.djangoproject.com/en/3.2/topics/class-based-views/intro/)
- [Polls Tutorial: Part 4](https://docs.djangoproject.com/en/3.2/intro/tutorial04/#use-generic-views-less-code-is-better)
- [Built-in class-based generic views](https://docs.djangoproject.com/en/3.2/topics/class-based-views/generic-display/)
- [Form handling with class-based views](https://docs.djangoproject.com/en/3.2/topics/class-based-views/generic-editing/)
- [Using mixins with class-based views](https://docs.djangoproject.com/en/3.2/topics/class-based-views/mixins/)
- [simpleisbetterthancomplex.com's overview](https://simpleisbetterthancomplex.com/article/2017/03/21/class-based-views-vs-function-based-views.html)


## Using Class-Based Views with Routing
There are two ways to use the built-in class-based views, either directly in the `urls.py` or by subclassing them. To create a route to a class-based view, you have to call `as_view()` and pass the result to `path`. The `DetailView` requires a `pk` in the path.

```python
from django.urls import path
from . import views
app_name = 'myapp'
urlpatterns = [
    
    # function-based view
    path('', views.index, name='index'),
    path('<int:model_id>/', views.detail, name='detail'),
    
    # class-based view
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail')
]
```

## Base Views

- [Base Views: View, TemplateView, RedirectView](https://docs.djangoproject.com/en/3.2/ref/class-based-views/base/)

### View

This the the base class of all other class-based views. It allows you to write a method to handle each HTTP method.

```python
# function-based view
def index(request):
    if request.method == 'GET':
        #...
    elif request.method == 'POST':
        #...

# class-based view
from django.views import View
class MyView(View):
    def get(self, request):
        #...
    def post(self, request):
        #...
```

### TemplateView

The `TemplateView` allows you to define a view just by specifying a template name and the data used to render it.

```python
from django.views.generic.base import TemplateView
from .models import MyModel
class MyView(TemplateView):
    template_name = "mytemplate.html"
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['my_model'] = MyModel.objects.all()
        return context
```

You can actually use a TemplateView without writing your own implementation.

```python
from django.views.generic.base import TemplateView
urlpatterns = [
    path('', TemplateView.as_view(template_name='index.html'), name="home"),
]
```


## Display Views
- [Display Views: DetailView,List View](https://docs.djangoproject.com/en/3.2/ref/class-based-views/generic-display/)

### ListView

A `ListView` 

The list of objects will default to the name `mymodel_list` in the template. You can customize this by setting `context_object_name` inside your view. You can also filter the data by overriding the `get_queryset` method.

The template used will default to `mymodel_list.html`. You can customize this by specifying a `template_name` inside your view.
```python
from django.views import generic
from .models import MyModel
class MyListView(generic.ListView):
    model = MyModel
    
    #below are default values you can change
    #context_object_name = 'mymodel_list'
    #template_name = 'myapp/mymodel_list.html'
    #def get_queryset(self):
    #    return MyModel.objects.all()
```

### DetailView
DetailView expects a 'pk' in the route: `path('<int:pk>/', views.DetailView.as_view(), name='detail')`.

The object's name will default to the name `mymodel` in the template. You can customize this by setting `context_object_name` inside your view.

The template used will default to `mymodel_detail.html`. You can customize this by specifying a `template_name` inside your view.
```python
from django.views import generic
from .models import MyModel
class MyDetailView(generic.DetailView):
    model = MyModel
    
    #below are default values you can change
    #context_object_name = 'mymodel'
    #template_name = 'myapp/mymodel_detail.html'
    #def get_object(self):
    #   object = super().get_object()
    #   return object
```
## Editing Views
- [Editing Views: FormView, CreateView, UpdateView, DeleteView](https://docs.djangoproject.com/en/3.2/ref/class-based-views/generic-editing/)

# Django Rest Framework and Vue

Once you have an API made in Django Rest Framework, it's easy to retrieve data using Vue.

## Two ways to structure your project

In most professional web applications, the front end and the back end are completely separate code bases. Often they are even in different repositories! This gives you the advantage of modular development. It's easy for one person or team to work on the front end while others work on the back end, with a common API interface between them. This also means you can swap out front-ends or the back-end serving your API as technologies come and go. It also makes it easy to have multiple front-ends, for example iOS/Android apps, a desktop app, a web app, etc.

There is a downside though. Because the back-end and front-end are separate, authentication is a lot more work. In vanilla Django, cookies and user sessions are used to track when a user is logged in as they visit templates. By hosting our front-end separately, Django can't manage users for us. Instead, we need to use something called token authentication. When a user wants to log in to our app, they need to send an API request with their authentication details. If they are correct, DRF will return an authentication token to be submitted with all other API requests.

For our purposes, we're going to bypass this requirement by serving up a hybrid approach to DRF and Vue. By embedding our Vue app in a Django template, we can take advantage of Django's user session system an we will not have to write any special authentication code beyond what we did in vanilla Django. This approach creates code that can sometimes get messy and inelegant whith a larger Vue application, but it saves us a lot of time and hassle.

## Import Vue

To use Vue in a Django template, simply make sure the end of your `body` has a `script` tag to load Vue. I recommend writing your Vue app in a `script` tag at the bottom of your template instead of an external JavaScript file. This means all your templates are in the same file.

```django
{% extends 'base.html' %}

{% block content %}

<div id="app">
  <p>{{ message }}</p> # Uh oh, this is the same template syntax as Django! We'll need to fix that...
</div>
<script src="https://unpkg.com/vue"></script>
<script>
  let app = new Vue({
    el: '#app',
    data: {
      message: 'Hello world!',
    },
    methods: {
      logMessage: function () {
        console.log(this);
      }
    },
    mounted: function () {
      logMessage();
    }
  });
</script>

{% endblock %}
```

## Changing delimiters

By default, both Django and Vue templates use `{{ }}` to insert expressions. We need to change the delimiters that Vue uses to square brackets instead. This can be done by adding a `delimiters` value to our root Vue configuration object.

```django
{% extends 'base.html' %}

{% block content %}

<div id="app">
  <p>[[ message ]]</p> # Much better!
</div>
<script src="https://unpkg.com/vue"></script>
<script>
  let app = new Vue({
    el: '#app',
    delimiters: ['[[', ']]'], // This is new
    data: {
      message: 'Hello world!',
    },
    methods: {
      logMessage: function () {
        console.log(this);
      }
    },
    mounted: function () {
      logMessage();
    }
  });
</script>

{% endblock %}
```

## Call your API

If you have an API, you can  use an `axios` ajax call in your `mounted` method to load the initial data when the page loads.

This requires you to have an API to get the data from. You have two choices: create manual API endpoints by creating views that return a `JSONResponse` (see *02 - Views.md*), or use Django Rest Framework to quickly create a full API, and then call the endpoint that corresponds the data you would like to load.

Once you have an API endpoint, your code is the same as any other API call. Make sure you either include the CSRF token in the template or set your API views to be `csrf_exempt`.

```django
...
  data: {
    grocery_items: [],
    new_grocery_item: {
      name: "",
    },
...
  mounted: {
    let crsf_token = document.querySelector("input[name=csrfmiddlewaretoken]").value;
    axios({
      url: '/api/grocery_item/',
      method: 'get',
      headers: {
        'X-CSRFToken': csrf_token
      }
    }).then(res => this.grocery_items = res.data)
  }
...
```

## Sending information back to Django

You'll need to send a data object back to your API. Axios makes this easy. Again, this can be a custom API endpoint you created (see *02 - Views.md*) or it can be a Django Rest Framework endpoint.

```django
...
  data: {
    grocery_items: [ ... ],
    new_grocery_item: {
      name: " ... ",
    },
...
  methods:
    save: {
      let crsf_token = document.querySelector("input[name=csrfmiddlewaretoken]").value;
      axios({
        url: 'api/grocery_item/',
        method: 'post',
        headers: {
          X-CSRFToken: csrf_token
        },
        data: this.new_grocery_item
      }).then(res => console.log(res))
    },
...
```

This is a *create* example, hence the `method: 'post'`. To *edit*, use `method: 'put'` or `method: 'patch'`. To *delete*, use `method: 'delete'`. For more information, take a look at the DRF's browsable API to see what your endpoints and avaliable methods are. If you submit an invalid request, DRF is very good and letting you know why. You can either handle the error in your Axios promise, or simply open the browser development tools and open the `Network` tab to view the error message that DRF returned.

If you're using Django REST Framework, that's it! If it didn't work, read your own API documentation and the DRF documentation to make sure you are submitting properly.

If you're using a custom API endpoint that you wrote, make sure it connects to a view. You can read the JSON body of your incoming request using `request.body` and parsing back from JSON to a Python dictionary.

#### views.py
```python
def save(request):
  if request.method == 'POST':
    json_data = json.loads(request.body)
    try:
      # access data here. you probably want to get an object, edit it, and save it.
    except KeyError:
      HttpResponseServerError("Malformed data!")
    HttpResponse("Thumbs up, you did it!")
```

## Templates vs Vue

Vue and Django templates fulfill the same role: presentation of data. With this in mind, decide which you are going to use to render your page. For instance, trying to use `{% for %}` and `v-for` in the same template will quickly make things complicated and buggy. For best results, I recommend using Vue to create pages and avoiding Django, even though technically you're editing a Django template.

# Deployment
- [Hosting Services](#hosting-services)
  - [Examples](#examples)
  - [SaaS vs PaaS vs IaaS](#saas-vs-paas-vs-iaas)
  - [Domain Names](#domain-names)
  - [DNS](#dns)
  - [HTTPS](#https)
- [Deploying Django](#deploying-django)
  - [Deploying with PythonAnywhere](#deploying-with-pythonanywhere)
  - [Deploying with Heroku](#deploying-with-heroku)
  - [Deploying with AWS](#deploying-with-aws)
  - [Deploying with DigitalOcean](#deploying-with-digitalocean)

## Hosting Services

### Examples

In order to have your web application accessible on the web, you'll have to find a hosting service. Below are some popular hosting services.

- [PythonAnywhere](https://www.pythonanywhere.com/)
- [Heroku](https://devcenter.heroku.com/articles/deploying-python)
- [WebFaction](https://www.webfaction.com/)
- [NearlyFreeSpeech.net](https://www.nearlyfreespeech.net/)
- [Digital Ocean](https://www.digitalocean.com/)
- [Amazon Web Services (AWS)](https://aws.amazon.com/)
- [Microsoft Azure](https://azure.microsoft.com/en-us/)

### SaaS vs PaaS vs IaaS

SaaS, PaaS, and IaaS are three different types of hosting services. [more info](https://www.bmc.com/blogs/saas-vs-paas-vs-iaas-whats-the-difference-and-how-to-choose/)

|    | description | examples |
|--- |---          |---       |
| SaaS | Software as a Service: they provide nearly everything through an interface of their web application | Wordpress, Squarespace |
| PaaS | Platform as a Service:  they provide the software and hardware, you copy files over and configure | PythonAnywhere, NearlyFreeSpeech, Windows Azure, AWS |
| IaaS | Infrastructure as a Service: they provide the hardware, you install software | Digital Ocean, AWS |
| Self-hosted | You manage all the software and hardware |    |



### Domain Names

Most hosting services will give your website a default domain name, but if you want to use a custom domain name, you'll have to rent it from a domain registrar. You can then add a DNS record to associate it with your server's IP address. You can read more about domains on the [MDN](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_is_a_domain_name).

- [Google Domains](https://domains.google/)
- [Hover](https://www.hover.com/)
- [GoDaddy](https://www.godaddy.com/)

### DNS

DNS stands for "[Domain Name System](https://en.wikipedia.org/wiki/Domain_Name_System)" and represents a series of servers which associate domain names with IP addresses. When you type a domain name into your browser's address bar and hit 'enter', the browser first queries the DNS to find out which IP address belongs to that domain name. Then it sends the request to that IP address and displays the response on the page. DNS servers can store different types of records.

|Type|Description|Function|
|--- |--- |--- |
|A|Address record|Returns a 32-bit IPv4 address, most commonly used to map hostnames to an IP address of the host.|
|AAAA|IPv6 address record|Returns a 128-bit IPv6 address, most commonly used to map hostnames to an IP address of the host.|
|CNAME|Canonical name record|Alias of one name to another: the DNS lookup will continue by retrying the lookup with the new name.|
|TXT|Text record|Originally for arbitrary human-readable text in a DNS record, it's now often used to verify domain ownership for SSL.|



### HTTPS

To allow your site to use HTTP, you must install an SSL Certificate on your server. SSL certificates can be purchased from a Commercial Certificate Authority, created with Let's Encrypt, or self-signed.


## Deploying Django

First check out the [Django deployment checklist](https://docs.djangoproject.com/en/3.2/howto/deployment/checklist/) and working with [static files](https://docs.djangoproject.com/en/3.2/howto/static-files/deployment/), then consider these additional steps.

- Set up a [virtual environment](../../1%20Python/docs/Virtual%20Environments.md) with a `requirements.txt` to keep track of your libraries.
- Create a `development_settings.py` and a `production_settings.py` to be able to switch configurations and hide your production environment's [SECRET_KEY](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-SECRET_KEY).

If you want to generate a new secret key you can use the code below:
```python
from django.core.management.utils import get_random_secret_key
print(get_random_secret_key())
```

If your project uses Django Channels then deployment is [more complicated](https://channels.readthedocs.io/en/latest/deploying.html).

### Deploying with PythonAnywhere

- [Tutorial](https://help.pythonanywhere.com/pages/DeployExistingDjangoProject) ([video](https://www.youtube.com/watch?v=Y4c4ickks2A))
- [Static Files](https://help.pythonanywhere.com/pages/DjangoStaticFiles)
- [Custom Domains](https://help.pythonanywhere.com/pages/CustomDomains/)
- [HTTPS](https://help.pythonanywhere.com/pages/HTTPSSetup/)
- [Help Pages](https://help.pythonanywhere.com/pages/)

It can be difficult to debug on PythonAnywhere because you do not have access to the process running django and thus cannot see the output of `print()`. However, you can print to the error log with the code below, a link to the error log can be found on the web app's page.

```python
import sys
print('check the error log', file=sys.stderr)
```

### Deploying with Heroku

Check out [this tutorial](https://devcenter.heroku.com/articles/getting-started-with-python?singlepage=true)

### Deploying with AWS

Check out [this tutorial](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-django.html)

### Deploying with DigitalOcean

Check out [this tutorial](https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-18-04)
 
# How to start a Django project
## Table of Contents

1. [Setup the project](#setup)
    - [Reference: "Am I inside or outside the virtual environment?"](#where-am-i)
1. [Create a Django App](#create-app)
1. [Create a View](#create-view)
1. [Create a Route to the View](#create-route)
1. [Running the web server locally](#runserver)
1. [Create Models](#create-models)
1. [Add the Model to the Admin Panel](#add-model)
1. [Create a Template](#create-template)
1. [Render a Template](#render-template)
1. [Set up template directories](#template-dirs)
1. [Set up static directories](#static-dirs)

- ## [Quickstart (Abbreviated version)](#quickstart)

---
## Setup the project <a name="setup"></a>

1. Create a directory. Open a terminal and navigate to a new (blank) folder, and  create your project folder. Make sure to replace **$PROJECT_NAME** with your own project name. Your project name should be named in snake_case and contain no capital letters.
    * `mkdir $PROJECT_NAME`
2. Change into that directory 
    * `cd $PROJECT_NAME`
3. Install django (requires `pipenv` to be installed via `pip install pipenv` . In case you ever need to remove the virtual environment, use `pipenv --rm`)
    * `pipenv install django`
4. Get into the environment
    * `pipenv shell`
    * `pip list` (verify Django is installed)
5. Create your project in the currect directory
    * `django-admin startproject $PROJECT_NAME .`
    * **The period at the end of this command is important!** It says ignore creating a new folder and put the contents of our new project in the current directory. 


### How to tell, "Where am I?" "Inside the virtual environment or not?" <a name="where-am-i"></a>

There are several ways to check whethe you are inside the virtual environment or not:

* `pipenv shell` (You can't shell into the environment again if you're already in there. But do NOT created a doubly-nested virtual environment!)
* `pip list` (Should show a full list of installed python packages on your system, or a more limited list inside the virtual environment.)
* `which python` (Does not work on all OS/python/terminal configurations.) This should give two possibilities. `C/system/python` would mean you are outside in your main terminal, or something including `C/users/name/.virtualenvs/$PROJECT-NAME-{UNIQUE-PIPENV-CODE}/Scripts/python` Both the `.virtualenvs` and the `unique-project-name-with-code` indicate you are inside the virtual environment.



---   
## Create a Django app inside the project <a name="create-app"></a>

Creating an app to add to your project is done by calling `python manage.py startapp $APPNAME_HERE`

Django won't recognize your app until you append it to the `INSTALLED_APPS` list in `settings.py`.

eg:
```python
INSTALLED_APPS = [
   ... other apps
   '$APPNAME_HERE',  
]
```

---
## Create a View <a name="create-view"></a>

- In your app's `views.py`:
```python
from django.http import HttpResponse
def <viewname>(request):
    return HttpResponse('ok')
```
A common `<viewname>` is `index`.

---
## Create a Route to the View <a name="create-route"></a>

- Create a `urls.py` inside your app
- Add a route in your app's `urls.py` which points to the the view
- Add an `app_name` to be able to look up paths when you render a template

```python
from django.urls import path
from . import views

app_name = '<app name>' # for namespacing
urlpatterns = [
    path('<path>', views.<viewname>, name='<viewname>')
]
```

- Add a route in your project's `urls.py` which points to the app's `urls.py` using `include`

```python
from django.urls import path, include
from django.contrib import admin

urlpatterns = [
    path('admin/', admin.site.urls),
    path('<path>/', include('<appname>.urls')) # Note: all your app urls will start with this path
]
```
---
## Running the web server locally <a name="runserver"></a>

At this point, you should run the server (`python manage.py runserver`) and go to `localhost:8000/app_path/view_path` and verify that you can access the view.
The local Django web server will generally remain running and automatically restart if it detects changes in any .py files, but this is the first place you should check if you can't access it locally. Close the server like any python file with `Cmd+C / Control+C`. Remember to restart it after performing any direct commands to the manage.py file. 

---
## Create Models <a name="create-models"></a>

- Define your models (Python classes) in the app's `models.py`
- Stage your migrations: `python manage.py makemigrations <appname>`
- (optional) View the SQL commands that will occur during migrations: `python manage.py sqlmigrate <appname> <migration number>`. You can find the migration number and the code that'll be executed during the migration in `<appname>/migrations/<migration number>_initial.py`
- Perform migrations (synchronize your models with your database): `python manage.py migrate`

---
## Add the Model to the Admin Panel <a name="add-model"></a>

- Add a `def __str__(self):` to your model so the admin interface knows how to display it.
- Make your app visible in the admin panel by registering your models with our app's `admin.py`
    ```py
    admin.site.register(Model)
    ```

```python
from django.contrib import admin
from .models import <model name 1> <model name 2>
admin.site.register(<model name 1>)
admin.site.register(<model name 2>)
```

- Go to `localhost:8000/admin` in your browser, and add some data.

---
## Create a Template <a name="create-template"></a>

- Create a folder inside your app called `templates`, inside of that create another folder with the name of your app, and inside of *that* create a `<filename>.html`. You can view examples of the template syntax [here](03%20-%20Templates.md).

---
## Render a Template <a name="render-template"></a>

- Inside your view, you can use the `render` shortcut to render a template. The first parameter is the request, the second parameter is the name of the template, and the third is a dictionary containing the values you'd like to render in the template.

```python
from django.shortcuts import render
def <view name>(request):
    context = {<name-value pairs>}
    return render(request, '<app name>/<template name>.html', context)
```

---
### Set up template directories <a name="template-dirs"></a>
In `settings.py`:
```py
TEMPLATES = [
    {
		...
		'DIRS': [os.path.join(BASE_DIR, 'templates')],
		...
	}
]
```

---
### Set up static directories <a name="static-dirs"></a>
In `settings.py`
```py
STATIC_URL = '/static/'
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
    os.path.join(
        os.path.dirname(__file__),
        'static',
    ),    
)
```

## Quickstart (Abbreviated version) <a name="quickstart"></a>
### Create the Project

- Create a site/project: `django-admin startproject $PROJECT_NAME`
- Move into the site's directory: `cd $PROJECT_NAME`
- Run migrations to create the database and user system `python manage.py migrate`
- Create an admin account with `python manage.py createsuperuser`, and enter a username, email address, and password

### Create the App

- Create an app: `python manage.py startapp $PROJECT_NAME`
- Add your app (`PROJECT_NAME.apps.PROJECT_NAMEConfig`) to the `INSTALLED_APPS` list [] in `settings.py`
	- Note: PROJECT_NAMEConfig is found in your `appname/apps.py` file


---
---

### *Note: the following "virtualenv / venv" commands are deprecated, but the old instructions may be useful in rare situations:*

### Set up Virtual Environment
If you haven't install `virtualenv`, do so now:
```
> [sudo] pip install virtualenv
```

Create a new project directory where you want to store your project and `cd` into it:
```
> mkdir project_dir_name
> cd project_dir_name
```

Create a new virtual environment:
```
> virtualenv ENV
```

To enter your virtual environment: 
In Linux/OSX:
```
> source ENV/bin/activate
```

In Windows:
```
> ENV/Scripts/activate
```

You are now in your virtual environment! This is a clean state with only Python and Pip installed. Install all the dependencies for your project inside this environment. 

For this course, install Django 2.0.0 
```
> pip install django==2.0.0
```

(Optional) To export your packages:
```
> pip freeze > requirements.txt
```

To exit your virtual environment:
```
> deactivate
```


# Django and Vue
So you want to use Django as your backend, but you want more interactivity on your pages than you can provide with Django templates? Good news -- Django and Vue play very nicely together, and are easy to integrate. There's just a few things to keep in mind.

## Import Vue
To use Vue in a Django template, simply make sure the end of your `body` has a `script` tag to load Vue. I recommend writing your Vue app in a `script` tag at the bottom of your template instead of an external JavaScript file. This can make it easy to have your Django app pass data into your Vue app, like the CSRF token or even a context object. Even if you're using Django Rest Framework, it means all your templates are in the same file.

```django
{% extends 'base.html' %}

{% block content %}

<div id="app">
  <p>{{ message }}</p> # Uh oh, this is the same template syntax as Django! We'll need to fix that...
</div>
<script src="https://unpkg.com/vue"></script>
<script>
  let app = new Vue({
    el: '#app',
    data: {
      message: 'Hello world!',
    },
    methods: {
      logMessage: function () {
        console.log(this);
      }
    },
    mounted: function () {
      logMessage();
    }
  });
</script>

{% endblock %}
```

## Changing delimiters
By default, both Django and Vue templates use `{{ }}` to insert expressions. We need to change the delimiters that Vue uses to square brackets instead. This can be done by adding a `delimiters` value to our root Vue configuration object.

```django
{% extends 'base.html' %}

{% block content %}

<div id="app">
  <p>[[ message ]]</p> # Much better!
</div>
<script src="https://unpkg.com/vue"></script>
<script>
  let app = new Vue({
    el: '#app',
    delimiters: ['[[', ']]'], // This is new
    data: {
      message: 'Hello world!',
    },
    methods: {
      logMessage: function () {
        console.log(this);
      }
    },
    mounted: function () {
      logMessage();
    }
  });
</script>

{% endblock %}
```

## Passing data context from Django to Vue

We now have a working Vue app in a Django template! How to we transfer over the data from our backend? We have two options:

  - Pass data from our Django view to our context and then load our template context into our Vue instance (this is clunky and we're not going to do it)
  - If we have an API (this could be manually programmed Django views that return JSON or a full-fledged Django REST Framework app) we can use the `mounted` method on out root Vue component to fetch the data needed to initialize our page. To do this you need to build a REST API, but can then build a more modern and robust application.
  
Let's look at how to do both!

### Clunky -- Passing context to Javascript

This requires modifying our Django view a little bit. Our context needs to include JSON data that our JavaScript code can read. Remember, a Django QuerySet is a Python object, JSON is a text string that JavaScript can parse into a data object.

We need to use something called a *serializer* to go through our QuerySet object and turn it into JSON. Luckly, Python has one built in. We then need to pass this JSON data into our context so that we can access it in our template. In the following example I'm using a functional view so I've added it to my context object. If you're using a class-based view, you can use `get_context_data` to add extra content to your context.

#### views.py
```python
...
incomplete_items = serializers.serialize('json', GroceryItem.objects.filter(is_completed=False).order_by('-date_created'))
complete_items = serializers.serialize('json', GroceryItem.objects.filter(is_completed=True).order_by('-date_completed'))
...
context = {
  'incomplete_items': incomplete_items,
  'complete_items': complete_items,
  ...
  }
...
```

Now we need to load our JSON into JavaScript. Django 2.2 adds a very handy new template tag called `json_script` that takes an object in context (it won't work with a standard QuerySet!) and creates a `<script>` tag containing the JSON code. We can then use `JSON.parse()` to load it as a normal JavaScript variable.

The syntax for `json_script` is `{{ <value containing JSON>|json_script:"<id of script tag to create>" }}`

At this point we have our data in JavaScript, and can initialize our Vue instance using our new JavaScript variables.

#### template.html
```django
...
<script src="https://unpkg.com/vue"></script>
{{ complete_items|json_script:"init-complete" }}
{{ incomplete_items|json_script:"init-incomplete" }}
<script>
  let json_incomplete_items = JSON.parse(JSON.parse(document.getElementById('init-incomplete').textContent));
  let json_complete_items = JSON.parse(JSON.parse(document.getElementById('init-complete').textContent));
  let app = new Vue({
    el: '#app',
    delimiters: ['[[', ']]'], // This is new
    data: {
        incomplete_items: json_incomplete_items,
        complete_items: json_complete_items,
        message: "Hello World!"
    },
    methods: {
      logMessage: function () {
        console.log(this);
      }
    },
    mounted: function () {
      logMessage();
    }
  });
</script>
...
```

### Better -- `mounted` API call

If you have an API, you can avoid all the awkward conversions we just did. You can just use Django to load your Vue app, and then use an `axios` Ajax call in your `mounted` method to load the initial data when the page loads.

This requires you to have an API to get the data from. You have two choices: create manual API endpoints by creating views that return a `JSONResponse` (see *02 - Views.md*), or use Django Rest Framework to quickly create a full API, and then call the endpoint that corresponds the data you would like to load.

Once you have an API endpoint, your code is the same as any other API call. Make sure you either include the CSRF token or set your API views to be `csrf_exempt`.

```django
...
  mounted: {
    let crsf_token = document.querySelector("input[name=csrfmiddlewaretoken]").value;
    axios({
      url: '/api/grocery_item/',
      method: 'get',
      headers: {
        'X-CSRFToken': csrf_token
      }
    }).then(res => this.grocery_items = res.data)
  }
...
```

## Sending information back to Django

You'll need to send a data object back to your API. Axios makes this easy. Again, this can be a custom API endpoint you created (see *02 - Views.md*) or it can be a Django Rest Framework endpoint.

#### template.html
```django
...
  methods:
    save: {
      let crsf_token = document.querySelector("input[name=csrfmiddlewaretoken]").value;
      axios({
        url: 'api/grocery_item/',
        method: 'post',
        headers: {
          X-CSRFToken: csrf_token
        },
        data: this.new_grocery_item
      }).then(res => console.log(res))
    },
...
```

If you're using Django REST Framework, that's it! If it didn't work, read your own API documentation and the DRF documentation to make sure you are submitting properly.

If you're using a custom API endpoint that you wrote, make sure it connects to a view. You can read the JSON body of your incoming request using `request.body` and parsing back from JSON to a Python dictionary.

#### views.py
```python
def save(request):
  if request.method == 'POST':
    json_data = json.loads(request.body)
    try:
      # access data here. you probably want to get an object, edit it, and save it.
    except KeyError:
      HttpResponseServerError("Malformed data!")
    HttpResponse("Thumbs up, you did it!")
```

## Templates vs Vue

Vue and Django templates fulfill the same role: presentation of data. With this in mind, decide which you are going to use to render your page. For instance, trying to use `{% for %}` and `v-for` in the same template will quickly make things complicated and buggy.
# Email
Email is simple to set up in Django. You can read more about setting up email in the [official docs](https://docs.djangoproject.com/en/3.2/topics/email/). You may also consider using [anymail](https://github.com/anymail/django-anymail).

Add the following fields to your `settings.py`.

```python
EMAIL_HOST = 'smtp.email-host-provider-domain.com'
EMAIL_HOST_USER = 'yourusername@youremail.com'
EMAIL_HOST_PASSWORD = 'your password'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
DEFAULT_FROM_EMAIL = 'Your Name <you@email.com>'
```

Then in a view you can just use the `send_mail` function.

```python
from django.core.mail import send_mail

send_mail(
    'Subject here',
    'Here is the message.',
    'from@example.com',
    ['to@example.com'],
    fail_silently=False,
)
```


## SMTP Servers / Email Services

- [gmail](https://mail.google.com/mail/u/0/#settings/general)
  - [digital ocean guide](https://www.digitalocean.com/community/tutorials/how-to-use-google-s-smtp-server)
- [https://sendgrid.com/](https://sendgrid.com/)
  - [sendgrid official doc](https://sendgrid.com/docs/Integrate/Frameworks/django.html)
  - [sibtc guide](https://simpleisbetterthancomplex.com/tutorial/2016/06/13/how-to-send-email.html)
- [https://www.mailgun.com/](https://www.mailgun.com/)
  - [sibtc guide](https://simpleisbetterthancomplex.com/tutorial/2017/05/27/how-to-configure-mailgun-to-send-emails-in-a-django-app.html)

# Django for Professionals
## Chapter 1: Docker
### Creating Docker Project
1. Create virtual environment for project 
	- Inside project root directory.
```bash
pipenv install django
pipenv shell
```
2. Create django project with startproject command 
	- *note: adding `.` at the end of the command is optional, but recommended because without it Django adds an additional directory to the project; with it it does not.*
	- server will be launched at http://127.0.0.1:8000/ in web browser.
```bash
django-admin startproject <prj_name> .
python manage.py migrate
python manage.py runserver
```

### Creating Pages for the App
1. ***Create new app with `startapp`*
```bash
python manage.py startapp pages
```
- Django automatically install a new pages directory and several files.
- even though app is created project will not recognize it until it is added to the INSTALLED_APPS config withing `prj_name/settings.py`
- Apps are loaded top to bottom so good practice to add new apps below built in apps they may rely on *admin, auth etc.*
- *best practice to type the full `pages.apps.PagesConfig` which will open up more possibilities when configuring apps*
```python
# prj_name/settings.py

INSTALLED_APPS = [

'django.contrib.admin',

'django.contrib.auth',

'django.contrib.contenttypes',

'django.contrib.sessions',

'django.contrib.messages',

'django.contrib.staticfiles',

'pages.apps.PagesConfig', # new

]
```
2. **Set URL routes for new app**
```python
# prj_name/urls.py

from django.contrib import admin

from django.urls import path, include # new

urlpatterns = [

	path('admin/', admin.site.urls),

	path('', include('pages.urls')), # new

]
```
3. **Create application view template in view.py**
```python
# pages/views.py

from django.http import HttpResponse

def home_page_view(request):

	return HttpResponse('Hello, World!')
```
4. **Final step is to create a urls.py**
- create urls.py file `touch pages/urls.py`
- *note: best practice to provide optional name, home, for this route*
```python
# pages/urls.py

from django.urls import path

from .views import home_page_view

urlpatterns = [

	path('', home_page_view, name='home')
]
```
- `python manage.py runserver` link: http://127.0.0.1:8000/
### Image, Containers, and the Docker Host
- Stop local server with *ctrl+c* and stop venv by typing `exit` in CLI
1. **Create a Dockerfile
	- A Dockerfile is literally a list of instructions that must be built. 
	- A Docker Container is a running instance of an image.
	- to create a Dockerfile: `touch Dockerfile` 
```python
# Pull base image

FROM python:3.10.4-slim-bullseye

# Set environment variables

ENV PIP_DISABLE_PIP_VERSION_CHECK 1

ENV PYTHONDONTWRITEBYTECODE 1

ENV PYTHONUNBUFFERED 1

# Set work directory

WORKDIR /code

# Install dependencies

COPY ./requirements.txt .

RUN pip install -r requirements.txt

# Copy project

COPY . .
```
 - `PYTHONUNBUFFERED` ensures our console output looks familiar and is not buffered by Docker, which we don’t want.
 - `PYTHONDONTWRITEBYTECODE` means Python will not try to write .pyc files which we also do not desire
 - [Most recent version of Docker Compose](https://docs.docker.com/compose/compose-file/compose-versioning/)
2. **Build image with docker build
	- `docker build .`
3. **Create a `docker-compose.yml` to control how we run the container
	- it is possible to have multiple containers running in our docker host
```yml
version: "3.9"

services:

	web:

		build: .
		command: python /code/manage.py runserver 0.0.0.0:8000	
		volumes:	
			- .:/code	
		ports:
			- 8000:8000	
		depends_on:	
			- db	
	db:	
		image: postgres:13
		volumes:
			- postgres_data:/var/lib/postgresql/data/
		environment:
			- "POSTGRES_HOST_AUTH_METHOD=trust"

volumes:
	postgres_data:
```
4. **Final step is run Docker container using the command `docker-compose up`
	- Containers are resource heavy and should be stopped when not in use, *ctrl+c* and type command `docker-compose down`



### Chapter 1 Summary
- Create virtual environment locally and install Django
- Create a new project
- exit the virtual environment
- write a Dockerfile and the build the initial image.
- write a docker-compose.yml file and run the container with the `docker-compose up`



## Chapter 2: PostgreSQL
### PostgreSQL
- [PostgreSQL](https://www.postgresql.org/)Is an open source relational database that is the most popular choice among Django developers.