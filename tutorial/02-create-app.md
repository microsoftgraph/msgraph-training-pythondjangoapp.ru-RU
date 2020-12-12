<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="12dbb-101">В этом упражнении вы будете использовать [Django](https://www.djangoproject.com/) для создания веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="12dbb-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span>

1. <span data-ttu-id="12dbb-102">Если вы еще не установили Django, вы можете установить его из интерфейса командной строки (CLI) с помощью следующей команды.</span><span class="sxs-lookup"><span data-stu-id="12dbb-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    pip install --user Django==3.1.4
    ```

1. <span data-ttu-id="12dbb-103">Откройте CLI, перейдите в каталог, в котором у вас есть права на создание файлов, и запустите следующую команду, чтобы создать новое приложение Django.</span><span class="sxs-lookup"><span data-stu-id="12dbb-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. <span data-ttu-id="12dbb-104">Перейдите в **graph_tutorial** каталог и введите следующую команду, чтобы запустить локальный веб-сервер.</span><span class="sxs-lookup"><span data-stu-id="12dbb-104">Navigate to the **graph_tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="12dbb-105">Откройте браузер и перейдите по адресу `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="12dbb-105">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="12dbb-106">Если все работает, вы увидите страницу приветствия Django.</span><span class="sxs-lookup"><span data-stu-id="12dbb-106">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="12dbb-107">Если вы этого не видите, ознакомьтесь с руководством по началу [работы Django.](https://www.djangoproject.com/start/)</span><span class="sxs-lookup"><span data-stu-id="12dbb-107">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

1. <span data-ttu-id="12dbb-108">Добавьте приложение в проект.</span><span class="sxs-lookup"><span data-stu-id="12dbb-108">Add an app to the project.</span></span> <span data-ttu-id="12dbb-109">В CLI запустите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="12dbb-109">Run the following command in your CLI.</span></span>

    ```Shell
    python manage.py startapp tutorial
    ```

1. <span data-ttu-id="12dbb-110">Откройте **./graph_tutorial/settings.py** и добавьте новое `tutorial` приложение в `INSTALLED_APPS` параметр.</span><span class="sxs-lookup"><span data-stu-id="12dbb-110">Open **./graph_tutorial/settings.py** and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. <span data-ttu-id="12dbb-111">В CLI запустите следующую команду, чтобы инициализировать базу данных для проекта.</span><span class="sxs-lookup"><span data-stu-id="12dbb-111">In your CLI, run the following command to initialize the database for the project.</span></span>

    ```Shell
    python manage.py migrate
    ```

1. <span data-ttu-id="12dbb-112">Добавьте [URL-адрес для](https://docs.djangoproject.com/en/3.0/topics/http/urls/) `tutorial` приложения.</span><span class="sxs-lookup"><span data-stu-id="12dbb-112">Add a [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="12dbb-113">Создайте файл с именем **./tutorial** и `urls.py` добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="12dbb-113">Create a new file in the **./tutorial** directory named `urls.py` and add the following code.</span></span>

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

1. <span data-ttu-id="12dbb-114">Обновим URL-адрес проекта, чтобы импортировать его.</span><span class="sxs-lookup"><span data-stu-id="12dbb-114">Update the project URLconf to import this one.</span></span> <span data-ttu-id="12dbb-115">Откройте **./graph_tutorial/urls.py** и замените содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="12dbb-115">Open **./graph_tutorial/urls.py** and replace the contents with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. <span data-ttu-id="12dbb-116">Добавьте в приложение временное представление, чтобы убедиться, что `tutorials` маршрутия URL-адресов работает.</span><span class="sxs-lookup"><span data-stu-id="12dbb-116">Add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="12dbb-117">Откройте **./tutorial/views.py** и замените все его содержимое следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="12dbb-117">Open **./tutorial/views.py** and replace its entire contents with the following code.</span></span>

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

1. <span data-ttu-id="12dbb-118">Сохраните все изменения и перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="12dbb-118">Save all of your changes and restart the server.</span></span> <span data-ttu-id="12dbb-119">Перейдите по этой `http://localhost:8000` теме.</span><span class="sxs-lookup"><span data-stu-id="12dbb-119">Browse to `http://localhost:8000`.</span></span> <span data-ttu-id="12dbb-120">Вы должны увидеть `Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="12dbb-120">You should see `Welcome to the tutorial.`</span></span>

## <a name="install-libraries"></a><span data-ttu-id="12dbb-121">Установка библиотек</span><span class="sxs-lookup"><span data-stu-id="12dbb-121">Install libraries</span></span>

<span data-ttu-id="12dbb-122">Прежде чем двигаться дальше, установите дополнительные библиотеки, которые вы будете использовать позже:</span><span class="sxs-lookup"><span data-stu-id="12dbb-122">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="12dbb-123">[Библиотека проверки подлинности Майкрософт (MSAL) для Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) для обработки потоков входов и маркеров OAuth.</span><span class="sxs-lookup"><span data-stu-id="12dbb-123">[Microsoft Authentication Library (MSAL) for Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="12dbb-124">[Запросы: HTTP для людей для](https://requests.readthedocs.io/en/master/) вызовов Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="12dbb-124">[Requests: HTTP for Humans](https://requests.readthedocs.io/en/master/) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="12dbb-125">[PyYAML для](https://pyyaml.org/wiki/PyYAMLDocumentation) загрузки конфигурации из файла YAML.</span><span class="sxs-lookup"><span data-stu-id="12dbb-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="12dbb-126">[python-dateutil](https://pypi.org/project/python-dateutil/) для parsing ISO 8601 date strings returned from Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="12dbb-126">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

1. <span data-ttu-id="12dbb-127">В CLI запустите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="12dbb-127">Run the following command in your CLI.</span></span>

    ```Shell
    pip install msal==1.7.0
    pip install requests==2.25.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a><span data-ttu-id="12dbb-128">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="12dbb-128">Design the app</span></span>

1. <span data-ttu-id="12dbb-129">Создайте каталог с именем **./tutorial.** `templates`</span><span class="sxs-lookup"><span data-stu-id="12dbb-129">Create a new directory in the **./tutorial** directory named `templates`.</span></span>

1. <span data-ttu-id="12dbb-130">В **каталоге ./tutorial/templates** создайте новый каталог с именем `tutorial` .</span><span class="sxs-lookup"><span data-stu-id="12dbb-130">In the **./tutorial/templates** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="12dbb-131">В **каталоге ./tutorial/templates/tutorial** создайте файл с именем `layout.html` .</span><span class="sxs-lookup"><span data-stu-id="12dbb-131">In the **./tutorial/templates/tutorial** directory, create a new file named `layout.html`.</span></span> <span data-ttu-id="12dbb-132">Добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="12dbb-132">Add the following code in that file.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    <span data-ttu-id="12dbb-133">Этот код добавляет [Bootstrap](http://getbootstrap.com/) для простого стиля и [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) для некоторых простых значков.</span><span class="sxs-lookup"><span data-stu-id="12dbb-133">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) for some simple icons.</span></span> <span data-ttu-id="12dbb-134">Он также определяет глобальный макет с панели nav.</span><span class="sxs-lookup"><span data-stu-id="12dbb-134">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="12dbb-135">Создайте каталог с именем **./tutorial.** `static`</span><span class="sxs-lookup"><span data-stu-id="12dbb-135">Create a new directory in the **./tutorial** directory named `static`.</span></span>

1. <span data-ttu-id="12dbb-136">В **каталоге ./tutorial/static** создайте новый каталог с именем `tutorial` .</span><span class="sxs-lookup"><span data-stu-id="12dbb-136">In the **./tutorial/static** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="12dbb-137">В **каталоге ./tutorial/static/tutorial** создайте файл с именем `app.css` .</span><span class="sxs-lookup"><span data-stu-id="12dbb-137">In the **./tutorial/static/tutorial** directory, create a new file named `app.css`.</span></span> <span data-ttu-id="12dbb-138">Добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="12dbb-138">Add the following code in that file.</span></span>

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. <span data-ttu-id="12dbb-139">Создайте шаблон для домашней страницы, использующей макет.</span><span class="sxs-lookup"><span data-stu-id="12dbb-139">Create a template for the home page that uses the layout.</span></span> <span data-ttu-id="12dbb-140">Создайте файл с именем **./tutorial/templates/tutorial** и `home.html` добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="12dbb-140">Create a new file in the **./tutorial/templates/tutorial** directory named `home.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. <span data-ttu-id="12dbb-141">Откройте файл `./tutorial/views.py` и добавьте следующую новую функцию.</span><span class="sxs-lookup"><span data-stu-id="12dbb-141">Open the `./tutorial/views.py` file and add the following new function.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. <span data-ttu-id="12dbb-142">Замените `home` существующее представление на следующее.</span><span class="sxs-lookup"><span data-stu-id="12dbb-142">Replace the existing `home` view with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. <span data-ttu-id="12dbb-143">Добавьте PNG-файл с **именемno-profile-photo.png** в **каталог ./tutorial/static/tutorial.**</span><span class="sxs-lookup"><span data-stu-id="12dbb-143">Add a PNG file named **no-profile-photo.png** in the **./tutorial/static/tutorial** directory.</span></span>

1. <span data-ttu-id="12dbb-144">Сохраните все изменения и перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="12dbb-144">Save all of your changes and restart the server.</span></span> <span data-ttu-id="12dbb-145">Теперь приложение должно выглядеть совершенно иначе.</span><span class="sxs-lookup"><span data-stu-id="12dbb-145">Now, the app should look very different.</span></span>

    ![Снимок экрана с измененной домашней страницей](./images/create-app-01.png)
