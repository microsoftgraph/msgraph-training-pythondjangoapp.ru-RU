<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете использовать [Django](https://www.djangoproject.com/) для создания веб-приложения.

1. Если у вас еще нет установки Django, вы можете установить его из интерфейса командной строки (CLI) со следующей командной командной командой.

    ```Shell
    pip install Django==3.2
    ```

1. Откройте CLI, перейдите в каталог, в котором у вас есть права на создание файлов, и запустите следующую команду для создания нового приложения Django.

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. Перейдите к **каталогу graph_tutorial** и введите следующую команду, чтобы запустить локальный веб-сервер.

    ```Shell
    python manage.py runserver
    ```

1. Откройте браузер и перейдите по адресу `http://localhost:8000`. Если все работает, вы увидите страницу приветствия Django. Если вы этого не видите, проверьте руководство по началу [работы с Django.](https://www.djangoproject.com/start/)

1. Добавьте приложение в проект. Запустите следующую команду в CLI.

    ```Shell
    python manage.py startapp tutorial
    ```

1. Откройте **./graph_tutorial/settings.py** и добавьте новое `tutorial` приложение в `INSTALLED_APPS` параметр.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. В CLI запустите следующую команду, чтобы инициализировать базу данных для проекта.

    ```Shell
    python manage.py migrate
    ```

1. Добавьте [URL-адрес для](https://docs.djangoproject.com/en/3.0/topics/http/urls/) `tutorial` приложения. Создайте новый файл в **каталоге ./tutorial** с именем `urls.py` и добавьте следующий код.

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

1. Обнови проект URLconf, чтобы импортировать этот. Откройте **./graph_tutorial/urls.py** и замените содержимое следующим.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. Добавьте временное представление в `tutorials` приложение, чтобы убедиться, что маршрутия URL-адресов работает. Откройте **./tutorial/views.py** и замените все содержимое следующим кодом.

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect
    from django.urls import reverse
    from datetime import datetime, timedelta

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. Сохраните все изменения и перезапустите сервер. Просмотрите `http://localhost:8000` . Вы должны видеть `Welcome to the tutorial.`

## <a name="install-libraries"></a>Установка библиотек

Прежде чем двигаться дальше, установите дополнительные библиотеки, которые вы будете использовать позже:

- [Библиотека проверки подлинности Microsoft (MSAL) для Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) для обработки потоков маркеров входных и OAuth.
- [PyYAML для](https://pyyaml.org/wiki/PyYAMLDocumentation) загрузки конфигурации из файла YAML.
- [Python-dateutil](https://pypi.org/project/python-dateutil/) для размыкания строк даты ISO 8601, возвращенных из Microsoft Graph.

1. Запустите следующую команду в CLI.

    ```Shell
    pip install msal==1.10.0
    pip install pyyaml==5.4.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a>Проектирование приложения

1. Создание нового каталога в **каталоге ./tutorial** с именем `templates` .

1. В **каталоге ./tutorial/templates** создайте новый каталог с именем `tutorial` .

1. В **каталоге ./tutorial/templates/tutorial** создайте новый файл с именем `layout.html` . Добавьте следующий код в этот файл.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    Этот код добавляет [Bootstrap](http://getbootstrap.com/) для простого стиля и [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) для некоторых простых значков. Он также определяет глобальную макет с панели nav.

1. Создание нового каталога в **каталоге ./tutorial** с именем `static` .

1. В **каталоге ./tutorial/static** создайте новый каталог с именем `tutorial` .

1. В **каталоге ./tutorial/static/tutorial** создайте новый файл с именем `app.css` . Добавьте следующий код в этот файл.

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. Создайте шаблон для домашней страницы, использующей макет. Создайте новый файл в **каталоге ./tutorial/templates/tutorial** и `home.html` добавьте следующий код.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. Откройте файл `./tutorial/views.py` и добавьте следующую новую функцию.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. Замените `home` существующее представление следующим.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. Добавьте PNG-файл **с именемno-profile-photo.png** в **каталоге ./tutorial/static/tutorial.**

1. Сохраните все изменения и перезапустите сервер. Теперь приложение должно выглядеть совсем по-другому.

    ![Снимок экрана: обновленная домашняя страница](./images/create-app-01.png)
