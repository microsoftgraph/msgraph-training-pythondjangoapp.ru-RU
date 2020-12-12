<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете использовать [Django](https://www.djangoproject.com/) для создания веб-приложения.

1. Если вы еще не установили Django, вы можете установить его из интерфейса командной строки (CLI) с помощью следующей команды.

    ```Shell
    pip install --user Django==3.1.4
    ```

1. Откройте CLI, перейдите в каталог, в котором у вас есть права на создание файлов, и запустите следующую команду, чтобы создать новое приложение Django.

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. Перейдите в **graph_tutorial** каталог и введите следующую команду, чтобы запустить локальный веб-сервер.

    ```Shell
    python manage.py runserver
    ```

1. Откройте браузер и перейдите по адресу `http://localhost:8000`. Если все работает, вы увидите страницу приветствия Django. Если вы этого не видите, ознакомьтесь с руководством по началу [работы Django.](https://www.djangoproject.com/start/)

1. Добавьте приложение в проект. В CLI запустите следующую команду:

    ```Shell
    python manage.py startapp tutorial
    ```

1. Откройте **./graph_tutorial/settings.py** и добавьте новое `tutorial` приложение в `INSTALLED_APPS` параметр.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. В CLI запустите следующую команду, чтобы инициализировать базу данных для проекта.

    ```Shell
    python manage.py migrate
    ```

1. Добавьте [URL-адрес для](https://docs.djangoproject.com/en/3.0/topics/http/urls/) `tutorial` приложения. Создайте файл с именем **./tutorial** и `urls.py` добавьте следующий код.

    ```python
    from django.urls import path

    from . import views

    urlpatterns = [
      # /
      path('', views.home, name='home'),
      # TEMPORARY
      path('signin', views.home, name='signin'),
      path('signout', views.home, name='signout'),
      path('calendar', views.home, name='calendar'),
    ]
    ```

1. Обновим URL-адрес проекта, чтобы импортировать его. Откройте **./graph_tutorial/urls.py** и замените содержимое на следующее.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. Добавьте в приложение временное представление, чтобы убедиться, что `tutorials` маршрутия URL-адресов работает. Откройте **./tutorial/views.py** и замените все его содержимое следующим кодом.

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect
    from django.urls import reverse
    from datetime import datetime, timedelta
    from dateutil import tz, parser

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. Сохраните все изменения и перезапустите сервер. Перейдите по этой `http://localhost:8000` теме. Вы должны увидеть `Welcome to the tutorial.`

## <a name="install-libraries"></a>Установка библиотек

Прежде чем двигаться дальше, установите дополнительные библиотеки, которые вы будете использовать позже:

- [Библиотека проверки подлинности Майкрософт (MSAL) для Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) для обработки потоков входов и маркеров OAuth.
- [Запросы: HTTP для людей для](https://requests.readthedocs.io/en/master/) вызовов Microsoft Graph.
- [PyYAML для](https://pyyaml.org/wiki/PyYAMLDocumentation) загрузки конфигурации из файла YAML.
- [python-dateutil](https://pypi.org/project/python-dateutil/) для parsing ISO 8601 date strings returned from Microsoft Graph.

1. В CLI запустите следующую команду:

    ```Shell
    pip install msal==1.7.0
    pip install requests==2.25.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a>Проектирование приложения

1. Создайте каталог с именем **./tutorial.** `templates`

1. В **каталоге ./tutorial/templates** создайте новый каталог с именем `tutorial` .

1. В **каталоге ./tutorial/templates/tutorial** создайте файл с именем `layout.html` . Добавьте в этот файл следующий код.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    Этот код добавляет [Bootstrap](http://getbootstrap.com/) для простого стиля и [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) для некоторых простых значков. Он также определяет глобальный макет с панели nav.

1. Создайте каталог с именем **./tutorial.** `static`

1. В **каталоге ./tutorial/static** создайте новый каталог с именем `tutorial` .

1. В **каталоге ./tutorial/static/tutorial** создайте файл с именем `app.css` . Добавьте в этот файл следующий код.

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. Создайте шаблон для домашней страницы, использующей макет. Создайте файл с именем **./tutorial/templates/tutorial** и `home.html` добавьте следующий код.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. Откройте файл `./tutorial/views.py` и добавьте следующую новую функцию.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. Замените `home` существующее представление на следующее.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. Добавьте PNG-файл с **именемno-profile-photo.png** в **каталог ./tutorial/static/tutorial.**

1. Сохраните все изменения и перезапустите сервер. Теперь приложение должно выглядеть совершенно иначе.

    ![Снимок экрана с измененной домашней страницей](./images/create-app-01.png)
