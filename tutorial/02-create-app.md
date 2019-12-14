<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете использовать [Джанго](https://www.djangoproject.com/) для создания веб-приложения. Если вы еще не установили Джанго, вы можете установить его из интерфейса командной строки (CLI) с помощью следующей команды.

```Shell
pip install Django==3.0
```

Откройте подсистему CLI, перейдите к каталогу, в котором у вас есть права на создание файлов, и выполните следующую команду, чтобы создать новое приложение Джанго.

```Shell
django-admin startproject graph_tutorial
```

Джанго создает новый каталог с именем `graph_tutorial` и формирование шаблонов для веб-приложения Джанго. Перейдите к новому каталогу и введите следующую команду для запуска локального веб-сервера.

```Shell
python manage.py runserver
```

Откройте браузер и перейдите по адресу `http://localhost:8000`. Если все работает, вы увидите страницу приветствия Джанго. Если вы не видите это, ознакомьтесь с [руководством по началу работы с Джанго](https://www.djangoproject.com/start/).

Теперь, когда вы проверили проект, добавьте приложение в проект. Выполните следующую команду в командной панели CLI.

```Shell
python manage.py startapp tutorial
```

В результате будет создано новое приложение в `./tutorial` каталоге. Откройте `./graph_tutorial/settings.py` и добавьте новое `tutorial` приложение в `INSTALLED_APPS` параметр.

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'tutorial',
]
```

В интерфейсе командной строки выполните следующую команду, чтобы инициализировать базу данных для проекта.

```Shell
python manage.py migrate
```

Добавьте [урлконф](https://docs.djangoproject.com/en/2.1/topics/http/urls/) для `tutorial` приложения. Создайте новый файл в `./tutorial` каталоге `urls.py` и добавьте указанный ниже код.

```python
from django.urls import path

from . import views

urlpatterns = [
  # /tutorial
  path('', views.home, name='home'),
]
```

Теперь обновите проект Урлконф, чтобы импортировать его. Откройте `./graph_tutorial/urls.py` файл и замените его содержимое приведенным ниже содержимым.

```python
from django.contrib import admin
from django.urls import path, include
from tutorial import views

urlpatterns = [
    path('tutorial/', include('tutorial.urls')),
    path('admin/', admin.site.urls),
]
```

Наконец, добавьте временное представление в `tutorials` приложение, чтобы убедиться в работоспособности маршрутизации URL-адресов. Откройте файл `./tutorial/views.py` и добавьте в него указанный ниже код.

```python
from django.shortcuts import render
from django.http import HttpResponse, HttpResponseRedirect

def home(request):
  # Temporary!
  return HttpResponse("Welcome to the tutorial.")
```

Сохраните все изменения и перезапустите сервер. Перейдите к `http://localhost:8000/tutorial`. Вы должны увидеть`Welcome to the tutorial.`

Прежде чем переходить, установите несколько дополнительных библиотек, которые будут использоваться позже:

- [Запросы OAuthlib: OAuth для людей](https://requests-oauthlib.readthedocs.io/en/latest/) для обработки входа и потоков маркеров OAuth, а для совершения звонков в Microsoft Graph.
- [Пиямл](https://pyyaml.org/wiki/PyYAMLDocumentation) для загрузки конфигурации из файла ямл.
- [Python-датеутил](https://pypi.org/project/python-dateutil/) для синтаксического анализа строк даты ISO 8601, возвращенных из Microsoft Graph.

Выполните следующую команду в командной панели CLI.

```Shell
pip install requests_oauthlib==1.3.0
pip install pyyaml==5.2
pip install python-dateutil==2.8.1
```

## <a name="design-the-app"></a>Проектирование приложения

Начните с создания каталога Templates и определения глобального макета для приложения. Создайте новый каталог в `./tutorial` каталоге с именем. `templates` В `templates` каталоге создайте новый каталог с именем `tutorial`. Наконец, создайте в этом каталоге новый файл с именем `layout.html`. Должен быть `./tutorial/templates/tutorial/layout.html`относительный путь из корневого каталога проекта. Добавьте в этот файл приведенный ниже код.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Python Graph Tutorial</title>

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css"
      integrity="sha384-WskhaSGFgHYWDcbwN70/dfYBj47jz9qbsMId/iRN3ewGhXQFZCSftd1LZCfmhktB" crossorigin="anonymous">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
      integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt" crossorigin="anonymous">
    {% load static %}
    <link rel="stylesheet" href="{% static "tutorial/app.css" %}">
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js"
      integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js"
      integrity="sha384-smHYKdLADwkXOn1EmN1qk/HfnUcbVRZyYmZ4qpPea6sjB/pTJ0euyQp0Mk8ck+5T" crossorigin="anonymous"></script>
  </head>

  <body>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
      <div class="container">
        <a href="{% url 'home' %}" class="navbar-brand">Python Graph Tutorial</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
          aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarCollapse">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item">
              <a href="{% url 'home' %}" class="nav-link{% if request.resolver_match.view_name == 'home' %} active{% endif %}">Home</a>
            </li>
            {% if user.is_authenticated %}
              <li class="nav-item" data-turbolinks="false">
                <a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>
              </li>
            {% endif %}
          </ul>
          <ul class="navbar-nav justify-content-end">
            <li class="nav-item">
              <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                <i class="fas fa-external-link-alt mr-1"></i>Docs
              </a>
            </li>
            {% if user.is_authenticated %}
              <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true" aria-expanded="false">
                  {% if user.avatar %}
                    <img src="{{ user.avatar }}" class="rounded-circle align-self-center mr-2" style="width: 32px;">
                  {% else %}
                    <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                  {% endif %}
                </a>
                <div class="dropdown-menu dropdown-menu-right">
                  <h5 class="dropdown-item-text mb-0">{{ user.name }}</h5>
                  <p class="dropdown-item-text text-muted mb-0">{{ user.email }}</p>
                  <div class="dropdown-divider"></div>
                  <a href="#" class="dropdown-item">Sign Out</a>
                </div>
              </li>
            {% else %}
              <li class="nav-item">
                <a href="#" class="nav-link">Sign In</a>
              </li>
            {% endif %}
          </ul>
        </div>
      </div>
    </nav>
    <main role="main" class="container">
      {% if errors %}
        {% for error in errors %}
          <div class="alert alert-danger" role="alert">
            <p class="mb-3">{{ error.message }}</p>
            {% if error.debug %}
              <pre class="alert-pre border bg-light p-2"><code>{{ error.debug }}</code></pre>
            {% endif %}
          </div>
        {% endfor %}
      {% endif %}
      {% block content %}{% endblock %}
    </main>
  </body>
</html>
```

В этом коде добавляется [Начальная](http://getbootstrap.com/) загрузка для простых стилей и [Шрифт Awesome](https://fontawesome.com/) для некоторых простых значков. Он также определяет глобальную структуру с помощью панели навигации.

Теперь создайте новый каталог в `./tutorial` каталоге с именем. `static` В `static` каталоге создайте новый каталог с именем `tutorial`. Наконец, создайте в этом каталоге новый файл с именем `app.css`. Должен быть `./tutorial/static/tutorial/app.css`относительный путь из корневого каталога проекта. Добавьте в этот файл приведенный ниже код.

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

Затем создайте шаблон для домашней страницы, использующей макет. Создайте новый файл в `./tutorial/templates/tutorial` каталоге `home.html` и добавьте указанный ниже код.

```html
{% extends "tutorial/layout.html" %}
{% block content %}
<div class="jumbotron">
  <h1>Python Graph Tutorial</h1>
  <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from Python</p>
  {% if user.is_authenticated %}
    <h4>Welcome {{ user.name }}!</h4>
    <p>Use the navigation bar at the top of the page to get started.</p>
  {% else %}
    <a href="#" class="btn btn-primary btn-large">Click here to sign in</a>
  {% endif %}
</div>
{% endblock %}
```

Обновите `home` представление, чтобы использовать этот шаблон. Откройте `./tutorial/views.py` файл и добавьте следующую новую функцию.

```python
def initialize_context(request):
  context = {}

  # Check for any errors in the session
  error = request.session.pop('flash_error', None)

  if error != None:
    context['errors'] = []
    context['errors'].append(error)

  # Check for user in the session
  context['user'] = request.session.get('user', {'is_authenticated': False})
  return context
```

Затем замените имеющееся `home` представление следующим.

```python
def home(request):
  context = initialize_context(request)

  return render(request, 'tutorial/home.html', context)
```

Сохраните все изменения и перезапустите сервер. Теперь приложение должно выглядеть по-другому.

![Снимок экрана с переработанной домашней страницей](./images/create-app-01.png)
