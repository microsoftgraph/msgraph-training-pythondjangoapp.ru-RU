<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="14887-101">В этом упражнении вы будете использовать [Django](https://www.djangoproject.com/) для создания веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="14887-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span>

1. <span data-ttu-id="14887-102">Если у вас еще нет установки Django, вы можете установить его из интерфейса командной строки (CLI) со следующей командной командной командой.</span><span class="sxs-lookup"><span data-stu-id="14887-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    pip install Django==3.2
    ```

1. <span data-ttu-id="14887-103">Откройте CLI, перейдите в каталог, в котором у вас есть права на создание файлов, и запустите следующую команду для создания нового приложения Django.</span><span class="sxs-lookup"><span data-stu-id="14887-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. <span data-ttu-id="14887-104">Перейдите к **каталогу graph_tutorial** и введите следующую команду, чтобы запустить локальный веб-сервер.</span><span class="sxs-lookup"><span data-stu-id="14887-104">Navigate to the **graph_tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="14887-105">Откройте браузер и перейдите по адресу `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="14887-105">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="14887-106">Если все работает, вы увидите страницу приветствия Django.</span><span class="sxs-lookup"><span data-stu-id="14887-106">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="14887-107">Если вы этого не видите, проверьте руководство по началу [работы с Django.](https://www.djangoproject.com/start/)</span><span class="sxs-lookup"><span data-stu-id="14887-107">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

1. <span data-ttu-id="14887-108">Добавьте приложение в проект.</span><span class="sxs-lookup"><span data-stu-id="14887-108">Add an app to the project.</span></span> <span data-ttu-id="14887-109">Запустите следующую команду в CLI.</span><span class="sxs-lookup"><span data-stu-id="14887-109">Run the following command in your CLI.</span></span>

    ```Shell
    python manage.py startapp tutorial
    ```

1. <span data-ttu-id="14887-110">Откройте **./graph_tutorial/settings.py** и добавьте новое `tutorial` приложение в `INSTALLED_APPS` параметр.</span><span class="sxs-lookup"><span data-stu-id="14887-110">Open **./graph_tutorial/settings.py** and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. <span data-ttu-id="14887-111">В CLI запустите следующую команду, чтобы инициализировать базу данных для проекта.</span><span class="sxs-lookup"><span data-stu-id="14887-111">In your CLI, run the following command to initialize the database for the project.</span></span>

    ```Shell
    python manage.py migrate
    ```

1. <span data-ttu-id="14887-112">Добавьте [URL-адрес для](https://docs.djangoproject.com/en/3.0/topics/http/urls/) `tutorial` приложения.</span><span class="sxs-lookup"><span data-stu-id="14887-112">Add a [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="14887-113">Создайте новый файл в **каталоге ./tutorial** с именем `urls.py` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="14887-113">Create a new file in the **./tutorial** directory named `urls.py` and add the following code.</span></span>

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

1. <span data-ttu-id="14887-114">Обнови проект URLconf, чтобы импортировать этот.</span><span class="sxs-lookup"><span data-stu-id="14887-114">Update the project URLconf to import this one.</span></span> <span data-ttu-id="14887-115">Откройте **./graph_tutorial/urls.py** и замените содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="14887-115">Open **./graph_tutorial/urls.py** and replace the contents with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. <span data-ttu-id="14887-116">Добавьте временное представление в `tutorials` приложение, чтобы убедиться, что маршрутия URL-адресов работает.</span><span class="sxs-lookup"><span data-stu-id="14887-116">Add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="14887-117">Откройте **./tutorial/views.py** и замените все содержимое следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="14887-117">Open **./tutorial/views.py** and replace its entire contents with the following code.</span></span>

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect
    from django.urls import reverse
    from datetime import datetime, timedelta

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. <span data-ttu-id="14887-118">Сохраните все изменения и перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="14887-118">Save all of your changes and restart the server.</span></span> <span data-ttu-id="14887-119">Просмотрите `http://localhost:8000` .</span><span class="sxs-lookup"><span data-stu-id="14887-119">Browse to `http://localhost:8000`.</span></span> <span data-ttu-id="14887-120">Вы должны видеть `Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="14887-120">You should see `Welcome to the tutorial.`</span></span>

## <a name="install-libraries"></a><span data-ttu-id="14887-121">Установка библиотек</span><span class="sxs-lookup"><span data-stu-id="14887-121">Install libraries</span></span>

<span data-ttu-id="14887-122">Прежде чем двигаться дальше, установите дополнительные библиотеки, которые вы будете использовать позже:</span><span class="sxs-lookup"><span data-stu-id="14887-122">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="14887-123">[Библиотека проверки подлинности Microsoft (MSAL) для Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) для обработки потоков маркеров входных и OAuth.</span><span class="sxs-lookup"><span data-stu-id="14887-123">[Microsoft Authentication Library (MSAL) for Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="14887-124">[PyYAML для](https://pyyaml.org/wiki/PyYAMLDocumentation) загрузки конфигурации из файла YAML.</span><span class="sxs-lookup"><span data-stu-id="14887-124">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="14887-125">[Python-dateutil](https://pypi.org/project/python-dateutil/) для размыкания строк даты ISO 8601, возвращенных из Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="14887-125">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

1. <span data-ttu-id="14887-126">Запустите следующую команду в CLI.</span><span class="sxs-lookup"><span data-stu-id="14887-126">Run the following command in your CLI.</span></span>

    ```Shell
    pip install msal==1.10.0
    pip install pyyaml==5.4.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a><span data-ttu-id="14887-127">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="14887-127">Design the app</span></span>

1. <span data-ttu-id="14887-128">Создание нового каталога в **каталоге ./tutorial** с именем `templates` .</span><span class="sxs-lookup"><span data-stu-id="14887-128">Create a new directory in the **./tutorial** directory named `templates`.</span></span>

1. <span data-ttu-id="14887-129">В **каталоге ./tutorial/templates** создайте новый каталог с именем `tutorial` .</span><span class="sxs-lookup"><span data-stu-id="14887-129">In the **./tutorial/templates** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="14887-130">В **каталоге ./tutorial/templates/tutorial** создайте новый файл с именем `layout.html` .</span><span class="sxs-lookup"><span data-stu-id="14887-130">In the **./tutorial/templates/tutorial** directory, create a new file named `layout.html`.</span></span> <span data-ttu-id="14887-131">Добавьте следующий код в этот файл.</span><span class="sxs-lookup"><span data-stu-id="14887-131">Add the following code in that file.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    <span data-ttu-id="14887-132">Этот код добавляет [Bootstrap](http://getbootstrap.com/) для простого стиля и [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) для некоторых простых значков.</span><span class="sxs-lookup"><span data-stu-id="14887-132">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) for some simple icons.</span></span> <span data-ttu-id="14887-133">Он также определяет глобальную макет с панели nav.</span><span class="sxs-lookup"><span data-stu-id="14887-133">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="14887-134">Создание нового каталога в **каталоге ./tutorial** с именем `static` .</span><span class="sxs-lookup"><span data-stu-id="14887-134">Create a new directory in the **./tutorial** directory named `static`.</span></span>

1. <span data-ttu-id="14887-135">В **каталоге ./tutorial/static** создайте новый каталог с именем `tutorial` .</span><span class="sxs-lookup"><span data-stu-id="14887-135">In the **./tutorial/static** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="14887-136">В **каталоге ./tutorial/static/tutorial** создайте новый файл с именем `app.css` .</span><span class="sxs-lookup"><span data-stu-id="14887-136">In the **./tutorial/static/tutorial** directory, create a new file named `app.css`.</span></span> <span data-ttu-id="14887-137">Добавьте следующий код в этот файл.</span><span class="sxs-lookup"><span data-stu-id="14887-137">Add the following code in that file.</span></span>

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. <span data-ttu-id="14887-138">Создайте шаблон для домашней страницы, использующей макет.</span><span class="sxs-lookup"><span data-stu-id="14887-138">Create a template for the home page that uses the layout.</span></span> <span data-ttu-id="14887-139">Создайте новый файл в **каталоге ./tutorial/templates/tutorial** и `home.html` добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="14887-139">Create a new file in the **./tutorial/templates/tutorial** directory named `home.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. <span data-ttu-id="14887-140">Откройте файл `./tutorial/views.py` и добавьте следующую новую функцию.</span><span class="sxs-lookup"><span data-stu-id="14887-140">Open the `./tutorial/views.py` file and add the following new function.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. <span data-ttu-id="14887-141">Замените `home` существующее представление следующим.</span><span class="sxs-lookup"><span data-stu-id="14887-141">Replace the existing `home` view with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. <span data-ttu-id="14887-142">Добавьте PNG-файл **с именемno-profile-photo.png** в **каталоге ./tutorial/static/tutorial.**</span><span class="sxs-lookup"><span data-stu-id="14887-142">Add a PNG file named **no-profile-photo.png** in the **./tutorial/static/tutorial** directory.</span></span>

1. <span data-ttu-id="14887-143">Сохраните все изменения и перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="14887-143">Save all of your changes and restart the server.</span></span> <span data-ttu-id="14887-144">Теперь приложение должно выглядеть совсем по-другому.</span><span class="sxs-lookup"><span data-stu-id="14887-144">Now, the app should look very different.</span></span>

    ![Снимок экрана: обновленная домашняя страница](./images/create-app-01.png)
