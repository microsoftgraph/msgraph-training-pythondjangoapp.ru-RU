<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8238d-101">В этом упражнении вы будете использовать [Джанго](https://www.djangoproject.com/) для создания веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="8238d-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span>

1. <span data-ttu-id="8238d-102">Если вы еще не установили Джанго, вы можете установить его из интерфейса командной строки (CLI) с помощью следующей команды.</span><span class="sxs-lookup"><span data-stu-id="8238d-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    pip install Django==3.0.4
    ```

1. <span data-ttu-id="8238d-103">Откройте подсистему CLI, перейдите к каталогу, в котором у вас есть права на создание файлов, и выполните следующую команду, чтобы создать новое приложение Джанго.</span><span class="sxs-lookup"><span data-stu-id="8238d-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. <span data-ttu-id="8238d-104">Перейдите в каталог **graph_tutorial** и введите следующую команду для запуска локального веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="8238d-104">Navigate to the **graph_tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="8238d-105">Откройте браузер и перейдите по адресу `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="8238d-105">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="8238d-106">Если все работает, вы увидите страницу приветствия Джанго.</span><span class="sxs-lookup"><span data-stu-id="8238d-106">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="8238d-107">Если вы не видите это, ознакомьтесь с [руководством по началу работы с Джанго](https://www.djangoproject.com/start/).</span><span class="sxs-lookup"><span data-stu-id="8238d-107">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

1. <span data-ttu-id="8238d-108">Добавление приложения в проект.</span><span class="sxs-lookup"><span data-stu-id="8238d-108">Add an app to the project.</span></span> <span data-ttu-id="8238d-109">Выполните следующую команду в командной панели CLI.</span><span class="sxs-lookup"><span data-stu-id="8238d-109">Run the following command in your CLI.</span></span>

    ```Shell
    python manage.py startapp tutorial
    ```

1. <span data-ttu-id="8238d-110">Откройте **graph_tutorial/Сеттингс.Пи** и добавьте новое `tutorial` приложение в `INSTALLED_APPS` параметр.</span><span class="sxs-lookup"><span data-stu-id="8238d-110">Open **./graph_tutorial/settings.py** and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. <span data-ttu-id="8238d-111">В интерфейсе командной строки выполните следующую команду, чтобы инициализировать базу данных для проекта.</span><span class="sxs-lookup"><span data-stu-id="8238d-111">In your CLI, run the following command to initialize the database for the project.</span></span>

    ```Shell
    python manage.py migrate
    ```

1. <span data-ttu-id="8238d-112">Добавьте [урлконф](https://docs.djangoproject.com/en/3.0/topics/http/urls/) для `tutorial` приложения.</span><span class="sxs-lookup"><span data-stu-id="8238d-112">Add a [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="8238d-113">Создайте новый файл в каталоге **./туториал** `urls.py` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="8238d-113">Create a new file in the **./tutorial** directory named `urls.py` and add the following code.</span></span>

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

1. <span data-ttu-id="8238d-114">Обновите проект Урлконф, чтобы импортировать его.</span><span class="sxs-lookup"><span data-stu-id="8238d-114">Update the project URLconf to import this one.</span></span> <span data-ttu-id="8238d-115">Откройте **/урлс.Пи./graph_tutorial** и замените содержимое приведенным ниже содержимым.</span><span class="sxs-lookup"><span data-stu-id="8238d-115">Open **./graph_tutorial/urls.py** and replace the contents with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. <span data-ttu-id="8238d-116">Добавьте временное представление в `tutorials` приложение, чтобы убедиться в работоспособности маршрутизации URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="8238d-116">Add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="8238d-117">Откройте **./туториал/виевс.Пи** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="8238d-117">Open **./tutorial/views.py** and add the following code.</span></span>

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. <span data-ttu-id="8238d-118">Сохраните все изменения и перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="8238d-118">Save all of your changes and restart the server.</span></span> <span data-ttu-id="8238d-119">Перейдите к `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="8238d-119">Browse to `http://localhost:8000`.</span></span> <span data-ttu-id="8238d-120">Вы должны увидеть`Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="8238d-120">You should see `Welcome to the tutorial.`</span></span>

## <a name="install-libraries"></a><span data-ttu-id="8238d-121">Установка библиотек</span><span class="sxs-lookup"><span data-stu-id="8238d-121">Install libraries</span></span>

<span data-ttu-id="8238d-122">Прежде чем переходить, установите несколько дополнительных библиотек, которые будут использоваться позже:</span><span class="sxs-lookup"><span data-stu-id="8238d-122">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="8238d-123">[Запросы OAuthlib: OAuth для людей](https://requests-oauthlib.readthedocs.io/en/latest/) для обработки входа и потоков маркеров OAuth, а для совершения звонков в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="8238d-123">[Requests-OAuthlib: OAuth for Humans](https://requests-oauthlib.readthedocs.io/en/latest/) for handling sign-in and OAuth token flows, and for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="8238d-124">[Пиямл](https://pyyaml.org/wiki/PyYAMLDocumentation) для загрузки конфигурации из файла ямл.</span><span class="sxs-lookup"><span data-stu-id="8238d-124">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="8238d-125">[Python-датеутил](https://pypi.org/project/python-dateutil/) для синтаксического анализа строк даты ISO 8601, возвращенных из Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="8238d-125">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

1. <span data-ttu-id="8238d-126">Выполните следующую команду в командной панели CLI.</span><span class="sxs-lookup"><span data-stu-id="8238d-126">Run the following command in your CLI.</span></span>

    ```Shell
    pip install requests_oauthlib==1.3.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a><span data-ttu-id="8238d-127">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="8238d-127">Design the app</span></span>

1. <span data-ttu-id="8238d-128">Создайте новый каталог в каталоге **./туториал** с именем `templates`.</span><span class="sxs-lookup"><span data-stu-id="8238d-128">Create a new directory in the **./tutorial** directory named `templates`.</span></span>

1. <span data-ttu-id="8238d-129">В каталоге **./туториал/темплатес** создайте новый каталог с именем `tutorial`.</span><span class="sxs-lookup"><span data-stu-id="8238d-129">In the **./tutorial/templates** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="8238d-130">В каталоге **./туториал/темплатес/туториал** создайте новый файл с именем `layout.html`.</span><span class="sxs-lookup"><span data-stu-id="8238d-130">In the **./tutorial/templates/tutorial** directory, create a new file named `layout.html`.</span></span> <span data-ttu-id="8238d-131">Добавьте в этот файл приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="8238d-131">Add the following code in that file.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    <span data-ttu-id="8238d-132">В этом коде добавляется [Начальная](http://getbootstrap.com/) загрузка для простых стилей и [Шрифт Awesome](https://fontawesome.com/) для некоторых простых значков.</span><span class="sxs-lookup"><span data-stu-id="8238d-132">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="8238d-133">Он также определяет глобальную структуру с помощью панели навигации.</span><span class="sxs-lookup"><span data-stu-id="8238d-133">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="8238d-134">Создайте новый каталог в каталоге **./туториал** с именем `static`.</span><span class="sxs-lookup"><span data-stu-id="8238d-134">Create a new directory in the **./tutorial** directory named `static`.</span></span>

1. <span data-ttu-id="8238d-135">В каталоге **./туториал/Статик** создайте новый каталог с именем `tutorial`.</span><span class="sxs-lookup"><span data-stu-id="8238d-135">In the **./tutorial/static** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="8238d-136">В каталоге **./туториал/Статик/туториал** создайте новый файл с именем `app.css`.</span><span class="sxs-lookup"><span data-stu-id="8238d-136">In the **./tutorial/static/tutorial** directory, create a new file named `app.css`.</span></span> <span data-ttu-id="8238d-137">Добавьте в этот файл приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="8238d-137">Add the following code in that file.</span></span>

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. <span data-ttu-id="8238d-138">Создайте шаблон для домашней страницы, использующей макет.</span><span class="sxs-lookup"><span data-stu-id="8238d-138">Create a template for the home page that uses the layout.</span></span> <span data-ttu-id="8238d-139">Создайте новый файл в каталоге **./туториал/темплатес/туториал** `home.html` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="8238d-139">Create a new file in the **./tutorial/templates/tutorial** directory named `home.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. <span data-ttu-id="8238d-140">Откройте `./tutorial/views.py` файл и добавьте следующую новую функцию.</span><span class="sxs-lookup"><span data-stu-id="8238d-140">Open the `./tutorial/views.py` file and add the following new function.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. <span data-ttu-id="8238d-141">Замените существующее `home` представление следующим.</span><span class="sxs-lookup"><span data-stu-id="8238d-141">Replace the existing `home` view with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. <span data-ttu-id="8238d-142">Сохраните все изменения и перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="8238d-142">Save all of your changes and restart the server.</span></span> <span data-ttu-id="8238d-143">Теперь приложение должно выглядеть по-другому.</span><span class="sxs-lookup"><span data-stu-id="8238d-143">Now, the app should look very different.</span></span>

    ![Снимок экрана с переработанной домашней страницей](./images/create-app-01.png)
