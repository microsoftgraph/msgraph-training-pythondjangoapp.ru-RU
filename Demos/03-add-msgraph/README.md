# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="fac87-101">Выполнение завершенного проекта</span><span class="sxs-lookup"><span data-stu-id="fac87-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fac87-102">Необходимые компоненты</span><span class="sxs-lookup"><span data-stu-id="fac87-102">Prerequisites</span></span>

<span data-ttu-id="fac87-103">Чтобы запустить завершенный проект в этой папке, вам потребуются следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="fac87-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="fac87-104">[Python](https://www.python.org/) (с [PIP](https://pypi.org/project/pip/)), установленной на компьютере для разработки.</span><span class="sxs-lookup"><span data-stu-id="fac87-104">[Python](https://www.python.org/) (with [pip](https://pypi.org/project/pip/)) installed on your development machine.</span></span> <span data-ttu-id="fac87-105">Если у вас нет Python, посетите предыдущую ссылку для получения вариантов загрузки.</span><span class="sxs-lookup"><span data-stu-id="fac87-105">If you do not have Python, visit the previous link for download options.</span></span> <span data-ttu-id="fac87-106">(**Примечание:** это руководство было написано с помощью Python версии 3.7.0 и джанго версии 2,1.</span><span class="sxs-lookup"><span data-stu-id="fac87-106">(**Note:** This tutorial was written with Python version 3.7.0 and Django version 2.1.</span></span> <span data-ttu-id="fac87-107">Действия, описанные в этом руководстве, могут работать с другими версиями, но не тестировались.</span><span class="sxs-lookup"><span data-stu-id="fac87-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="fac87-108">Личная учетная запись Майкрософт с почтовым ящиком на Outlook.com или рабочей или учебной учетной записью Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="fac87-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="fac87-109">Если у вас нет учетной записи Майкрософт, у вас есть несколько вариантов для получения бесплатной учетной записи:</span><span class="sxs-lookup"><span data-stu-id="fac87-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="fac87-110">Вы можете [зарегистрироваться для создания новой личной учетной записи Майкрософт](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="fac87-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="fac87-111">Вы можете [зарегистрироваться в программе для разработчиков office 365](https://developer.microsoft.com/office/dev-program) , чтобы получить бесплатную подписку на Office 365.</span><span class="sxs-lookup"><span data-stu-id="fac87-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-application-registration-portal"></a><span data-ttu-id="fac87-112">Регистрация веб-приложения с помощью портала регистрации приложений</span><span class="sxs-lookup"><span data-stu-id="fac87-112">Register a web application with the Application Registration Portal</span></span>

1. <span data-ttu-id="fac87-113">Откройте браузер и перейдите на [портал регистрации приложений](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="fac87-113">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="fac87-114">Вход с использованием **личной учетной записи** (с учетной записью Майкрософт) или **рабочей или учебНой учетной записи**.</span><span class="sxs-lookup"><span data-stu-id="fac87-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="fac87-115">В верхней части страницы выберите **Добавить приложение** .</span><span class="sxs-lookup"><span data-stu-id="fac87-115">Select **Add an app** at the top of the page.</span></span>

    > <span data-ttu-id="fac87-116">**Примечание:** Если на странице отображается несколько кнопок **Добавить приложение** , выберите ту, которая соответствует списку **приложений** для конвергенции.</span><span class="sxs-lookup"><span data-stu-id="fac87-116">**Note:** If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="fac87-117">На странице **Регистрация приложения** задайте для параметра **имя приложения** значение **учебник Microsoft Python Graph** и нажмите кнопку **создать**.</span><span class="sxs-lookup"><span data-stu-id="fac87-117">On the **Register your application** page, set the **Application Name** to **Python Graph Tutorial** and select **Create**.</span></span>

    ![Снимок экрана: создание нового приложения на веб-сайте портала регистрации приложений](/Images/arp-create-app-01.png)

1. <span data-ttu-id="fac87-119">На странице **Регистрация учебника Python Graph** в разделе **свойства** скопируйте **идентификатор приложения** так, как он понадобится позже.</span><span class="sxs-lookup"><span data-stu-id="fac87-119">On the **Python Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Снимок экрана с ИДЕНТИФИКАТОРом только что созданного приложения](/Images/arp-create-app-02.png)

1. <span data-ttu-id="fac87-121">ПроКрутите список вниз до раздела **секреты приложения** .</span><span class="sxs-lookup"><span data-stu-id="fac87-121">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="fac87-122">Выберите **создать новый пароль**.</span><span class="sxs-lookup"><span data-stu-id="fac87-122">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="fac87-123">В диалоговом окне **новый пароль** в созданном пароле скопируйте содержимое поля так, как оно потребуется позже.</span><span class="sxs-lookup"><span data-stu-id="fac87-123">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="fac87-124">**Важно!** Этот пароль никогда не отображается еще раз, поэтому убедитесь, что вы хотите скопировать его сейчас.</span><span class="sxs-lookup"><span data-stu-id="fac87-124">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![Снимок экрана с новым паролем приложения](/Images/arp-create-app-03.png)

1. <span data-ttu-id="fac87-126">ПроКрутите окно вниз до раздела **платформы** .</span><span class="sxs-lookup"><span data-stu-id="fac87-126">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="fac87-127">Нажмите кнопку **Добавить платформу**.</span><span class="sxs-lookup"><span data-stu-id="fac87-127">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="fac87-128">В диалоговом окне **Добавление платформы** выберите **веб**.</span><span class="sxs-lookup"><span data-stu-id="fac87-128">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Снимок экрана: создание платформы для приложения](/Images/arp-create-app-04.png)

    1. <span data-ttu-id="fac87-130">В поле **веб-** платформа введите URL-адрес `http://localhost:8000/tutorial/callback` для **URL-адресов перенаправления**.</span><span class="sxs-lookup"><span data-stu-id="fac87-130">In the **Web** platform box, enter the URL `http://localhost:8000/tutorial/callback` for the **Redirect URLs**.</span></span>

        ![Снимок экрана: недавно добавленная веб-платформа для приложения](/Images/arp-create-app-05.png)

1. <span data-ttu-id="fac87-132">ПроКрутите страницу вниз и выберите команду **сохранить**.</span><span class="sxs-lookup"><span data-stu-id="fac87-132">Scroll to the bottom of the page and select **Save**.</span></span>

## <a name="configure-the-sample"></a><span data-ttu-id="fac87-133">Настройка примера</span><span class="sxs-lookup"><span data-stu-id="fac87-133">Configure the sample</span></span>

1. <span data-ttu-id="fac87-134">Переименуйте `oauth_settings.yml.example` файл в `oauth_settings.yml`.</span><span class="sxs-lookup"><span data-stu-id="fac87-134">Rename the `oauth_settings.yml.example` file to `oauth_settings.yml`.</span></span>
1. <span data-ttu-id="fac87-135">Измените `oauth_settings.yml` файл и внесите следующие изменения.</span><span class="sxs-lookup"><span data-stu-id="fac87-135">Edit the `oauth_settings.yml` file and make the following changes.</span></span>
    1. <span data-ttu-id="fac87-136">Замените `YOUR_APP_ID_HERE` идентификатором **приложения** , полученНым на портале регистрации приложений.</span><span class="sxs-lookup"><span data-stu-id="fac87-136">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="fac87-137">Замените `YOUR_APP_PASSWORD_HERE` паролем, полученНым на портале регистрации приложений.</span><span class="sxs-lookup"><span data-stu-id="fac87-137">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="fac87-138">В интерфейсе командной строки (CLI) перейдите к этому каталогу и выполните следующую команду, чтобы установить требования.</span><span class="sxs-lookup"><span data-stu-id="fac87-138">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    pip install -r requirements.txt
    ```

1. <span data-ttu-id="fac87-139">В интерфейсе командной строки выполните следующую команду, чтобы инициализировать базу данных приложения.</span><span class="sxs-lookup"><span data-stu-id="fac87-139">In your CLI, run the following command to initialize the app's database.</span></span>

    ```Shell
    python manage.py migrate
    ```

## <a name="run-the-sample"></a><span data-ttu-id="fac87-140">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="fac87-140">Run the sample</span></span>

1. <span data-ttu-id="fac87-141">Выполните следующую команду в командной панели CLI, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="fac87-141">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="fac87-142">Откройте браузер и перейдите по адресу `http://localhost:8000/tutorial`.</span><span class="sxs-lookup"><span data-stu-id="fac87-142">Open a browser and browse to `http://localhost:8000/tutorial`.</span></span>