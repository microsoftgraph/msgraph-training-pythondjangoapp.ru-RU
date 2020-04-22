<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете использовать [Джанго](https://www.djangoproject.com/) для создания веб-приложения.

1. Если вы еще не установили Джанго, вы можете установить его из интерфейса командной строки (CLI) с помощью следующей команды.

    ```Shell
    pip install Django==3.0.4
    ```

1. Откройте подсистему CLI, перейдите к каталогу, в котором у вас есть права на создание файлов, и выполните следующую команду, чтобы создать новое приложение Джанго.

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. Перейдите в каталог **graph_tutorial** и введите следующую команду для запуска локального веб-сервера.

    ```Shell
    python manage.py runserver
    ```

1. Откройте браузер и перейдите по адресу `http://localhost:8000`. Если все работает, вы увидите страницу приветствия Джанго. Если вы не видите это, ознакомьтесь с [руководством по началу работы с Джанго](https://www.djangoproject.com/start/).

1. Добавление приложения в проект. Выполните следующую команду в командной панели CLI.

    ```Shell
    python manage.py startapp tutorial
    ```

1. Откройте **graph_tutorial/Сеттингс.Пи** и добавьте новое `tutorial` приложение в `INSTALLED_APPS` параметр.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. В интерфейсе командной строки выполните следующую команду, чтобы инициализировать базу данных для проекта.

    ```Shell
    python manage.py migrate
    ```

1. Добавьте [урлконф](https://docs.djangoproject.com/en/3.0/topics/http/urls/) для `tutorial` приложения. Создайте новый файл в каталоге **./туториал** `urls.py` и добавьте указанный ниже код.

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

1. Обновите проект Урлконф, чтобы импортировать его. Откройте **/урлс.Пи./graph_tutorial** и замените содержимое приведенным ниже содержимым.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. Добавьте временное представление в `tutorials` приложение, чтобы убедиться в работоспособности маршрутизации URL-адресов. Откройте **./туториал/виевс.Пи** и добавьте следующий код.

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. Сохраните все изменения и перезапустите сервер. Перейдите к `http://localhost:8000`. Вы должны увидеть`Welcome to the tutorial.`

## <a name="install-libraries"></a>Установка библиотек

Прежде чем переходить, установите несколько дополнительных библиотек, которые будут использоваться позже:

- [Запросы OAuthlib: OAuth для людей](https://requests-oauthlib.readthedocs.io/en/latest/) для обработки входа и потоков маркеров OAuth, а для совершения звонков в Microsoft Graph.
- [Пиямл](https://pyyaml.org/wiki/PyYAMLDocumentation) для загрузки конфигурации из файла ямл.
- [Python-датеутил](https://pypi.org/project/python-dateutil/) для синтаксического анализа строк даты ISO 8601, возвращенных из Microsoft Graph.

1. Выполните следующую команду в командной панели CLI.

    ```Shell
    pip install requests_oauthlib==1.3.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a>Проектирование приложения

1. Создайте новый каталог в каталоге **./туториал** с именем `templates`.

1. В каталоге **./туториал/темплатес** создайте новый каталог с именем `tutorial`.

1. В каталоге **./туториал/темплатес/туториал** создайте новый файл с именем `layout.html`. Добавьте в этот файл приведенный ниже код.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    В этом коде добавляется [Начальная](http://getbootstrap.com/) загрузка для простых стилей и [Шрифт Awesome](https://fontawesome.com/) для некоторых простых значков. Он также определяет глобальную структуру с помощью панели навигации.

1. Создайте новый каталог в каталоге **./туториал** с именем `static`.

1. В каталоге **./туториал/Статик** создайте новый каталог с именем `tutorial`.

1. В каталоге **./туториал/Статик/туториал** создайте новый файл с именем `app.css`. Добавьте в этот файл приведенный ниже код.

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. Создайте шаблон для домашней страницы, использующей макет. Создайте новый файл в каталоге **./туториал/темплатес/туториал** `home.html` и добавьте указанный ниже код.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. Откройте `./tutorial/views.py` файл и добавьте следующую новую функцию.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. Замените существующее `home` представление следующим.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. Сохраните все изменения и перезапустите сервер. Теперь приложение должно выглядеть по-другому.

    ![Снимок экрана с переработанной домашней страницей](./images/create-app-01.png)
