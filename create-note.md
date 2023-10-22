
sudo apt-get update
sudo apt-get install python3
#make dir
mkdir resume-demo



python3 -m venv virt
source virt/bin/activate
pip install django Pillow django-ckeditor

#check
pip freeze
#build
django-admin startproject resume_demo

python manage.py startapp main

## upload to github

## open settings.py
add main to INSTALLED_APPS

step 1-4

### create context_processors.py

### modify urls.py in resume_demo

### create urls.py in main

### modify models.py in main

### modify admin.py in main/  !!!important 
from .models import *

### create signals.py in main

### connect signals.py in apps.py in main


### create models forms.py in main

### create views.py in main

### create templates folder in main
create a folder called main in templates folder, this is where we put all the html files

# run pip freeze
run pip freeze to check if we have all the packages we need

### sovle [problem1](#problem1)
###  uncomment urlplatterns in main/urls.py
```
pip freeze > requirements.txt
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver

```

# Problem1 : ckeditor not showing up

G, analyzing the code and the error message, it seems like the import error is related to the usage of the `url` function from `django.conf.urls`. The error message "ImportError: cannot import name 'url' from 'django.conf.urls'" typically happens when there's an issue with the import statement or if a function has been deprecated/removed.

In Django 4.0 and above, the `url()` function is deprecated and will likely be removed in future versions. Given that you’re encountering this import error, it seems like your Django version does not support it.

### Solution:
Use `re_path()` instead of `url()` for regular expression paths. Ensure to adjust your import statement and the usage in `urlpatterns`.

#### Adjusted Code:
```python
from django.contrib import admin
from django.urls import path, include, re_path  # Modified import here
from django.conf import settings
from django.conf.urls.static import static
from django.views.static import serve

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('main.urls', namespace='main')), 
    re_path(r'^media/(?P<path>.*)$', serve,  # Modified line here
        {'document_root': settings.MEDIA_ROOT}),
    re_path(r'^static/(?P<path>.*)$', serve,  # Modified line here
        {'document_root': settings.STATIC_ROOT}),
]

if settings.DEBUG:
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### Explanation:
- Replaced `url()` with `re_path()` which is recommended for use with regular expressions in modern Django versions.
- Adjusted the import statement to bring in `re_path` instead of `url`.

### Additional Note:
Ensure that your project dependencies are managed effectively and that your codebase is updated when you upgrade packages or libraries. Deprecated functions or those removed in newer versions can lead to issues like the one you encountered.

G, let’s apply this solution and assess whether it resolves the import issue in your Django project. If further modifications or troubleshooting is required, please let me know. Crafting robust and error-free web applications is paramount


# After runserver, if you see this error:
```
cannot get template from django.template.loaders.filesystem.Loader
```
it's ok, just ignore it, it's because we don't have any templates yet, we will create them later
open http://127.0.0.1:8000/admin/ to see if the admin page is working, then login in as superuser

### what if you forgot your superuser password?
```
python manage.py shell
from django.contrib.auth.models import User
superuser = User.objects.get(username='hougarry@gmail.com')
superuser.set_password('123456')
superuser.save()
exit()
```

## take css files , images, js files from bootstrap template and put them in static folder

after this, we can see the css files in the source code of the page.
however, we still need to tell django where to find these files
```
workon resume-demo
```

### what if your work on is not working? 
The `workon` command you are trying to use appears to be related to a virtual environment management tool called `virtualenvwrapper`, which is commonly used with Python and Django development. However, the error message suggests that `workon` is not recognized as a valid command, which usually indicates that `virtualenvwrapper` is not installed or configured correctly on your system.

To resolve this issue, you can follow these steps:

1. **Install `virtualenvwrapper`**: If you haven't already installed `virtualenvwrapper`, you can do so using `pip`. Open a terminal and run:

   ```
   pip install virtualenvwrapper-win
   ```

   Note that on Windows, you typically use `virtualenvwrapper-win` instead of the standard `virtualenvwrapper` package.

2. **Set Up Virtual Environments**: I'm using windows, so I need to set up the virtual environment in the following way:

   ```bash
   $env:VIRTUALENVWRAPPER_PYTHON = "D:\Users\Administrator\AppData\Local\Programs\Python\Python311\python.exe"

   ```


4. **Create a Virtual Environment**: You can now create a virtual environment for your Django project using the `mkvirtualenv` command:

   ```
   mkvirtualenv resume_demo
   ```

   Replace `resume_demo` with the name of your virtual environment.

5. **Activate the Virtual Environment**: Once the virtual environment is created, activate it using the `workon` command:

   ```
   workon resume_demo
   python manage.py runserver
   ```

   You should now be inside your virtual environment, and you can install Django and manage your project within this environment. You can deactivate the virtual environment using the `deactivate` command. Next time you can use `workon resume_demo` to activate the virtual environment.

---

# Problem2: Where is js files, css files, images?

After the static files are collected, they are stored in the static folder in the project root directory. However, the static folder is not accessible by default. To make the static folder accessible, we need to add the following code to the urls.py file in the project root directory:

#### 1. save index.html in templates/main as base.html
#### 2. create partials folder in templates/main, then create header.html, footer.html, and nav.html
#### 3. modify base.html and add {% load static %} at the top, then add {% include 'partials/header.html' %} and {% include 'partials/footer.html' %} in the body, also we can add links to css files and js files, like this:
```html
<link rel="stylesheet" href="{% static 'css/bootstrap.min.css' %}">
```
After that , move all the navigation bar content in base.html to nav.html, and add {% load static %} at the top of nav.html, then add {% include 'partials/nav.html' %} in base.html

replace those in all files:
```
"images/ 
"{% static 'images/

.jpg"
.jpg' %}"

.svg"
.svg' %}"
```

# Problem3: urlpattern not found in urls.py /main/urls.py

```
   ...
	path('blog/', views.BlogView.as_view(), name="blogs"),
	path('blog/<slug:slug>', views.BlogDetailView.as_view(), name="blog"),
	]
```

python manage.py migrate