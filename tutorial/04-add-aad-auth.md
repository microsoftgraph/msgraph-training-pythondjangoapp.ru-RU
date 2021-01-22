<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="83b58-101">В этом упражнении вы расширим приложение из предыдущего упражнения, чтобы поддерживать проверку подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="83b58-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="83b58-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="83b58-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="83b58-103">На этом этапе вы интегрируете [библиотеку MSAL для Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) в приложение.</span><span class="sxs-lookup"><span data-stu-id="83b58-103">In this step you will integrate the [MSAL for Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) library into the application.</span></span>

1. <span data-ttu-id="83b58-104">Создайте файл в корневой папке проекта с именем `oauth_settings.yml` и добавьте следующее содержимое.</span><span class="sxs-lookup"><span data-stu-id="83b58-104">Create a new file in the root of the project named `oauth_settings.yml`, and add the following content.</span></span>

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. <span data-ttu-id="83b58-105">Замените код приложения на портале регистрации приложений `YOUR_APP_ID_HERE` и пароль, `YOUR_APP_SECRET_HERE` созданный вами.</span><span class="sxs-lookup"><span data-stu-id="83b58-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="83b58-106">Если вы используете управление исходным кодом, например git, пришло бы время исключить файл **oauth_settings.yml** из системы управления исходным кодом, чтобы избежать случайной утечки ИД приложения и пароля.</span><span class="sxs-lookup"><span data-stu-id="83b58-106">If you're using source control such as git, now would be a good time to exclude the **oauth_settings.yml** file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="83b58-107">Реализация входов</span><span class="sxs-lookup"><span data-stu-id="83b58-107">Implement sign-in</span></span>

1. <span data-ttu-id="83b58-108">Создайте файл с именем **./tutorial** и `auth_helper.py` добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="83b58-108">Create a new file in the **./tutorial** directory named `auth_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="83b58-109">Этот файл будет использовать все ваши методы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="83b58-109">This file will hold all of your authentication-related methods.</span></span> <span data-ttu-id="83b58-110">Создает URL-адрес авторизации, а метод обменивается ответом `get_sign_in_flow` `get_token_from_code` авторизации на маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="83b58-110">The `get_sign_in_flow` generates an authorization URL, and the `get_token_from_code` method exchanges the authorization response for an access token.</span></span>

1. <span data-ttu-id="83b58-111">Добавьте следующий `import` инструкции в **верхнюю часть ./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="83b58-111">Add the following `import` statement to the top of **./tutorial/views.py**.</span></span>

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code
    ```

1. <span data-ttu-id="83b58-112">Добавьте представление для входов в файл **./tutorial/views.py.**</span><span class="sxs-lookup"><span data-stu-id="83b58-112">Add a sign-in view in the **./tutorial/views.py** file.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. <span data-ttu-id="83b58-113">Добавьте представление вызова в файл **./tutorial/views.py.**</span><span class="sxs-lookup"><span data-stu-id="83b58-113">Add a callback view in the **./tutorial/views.py** file.</span></span>

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    <span data-ttu-id="83b58-114">Подумайте, что делают эти представления:</span><span class="sxs-lookup"><span data-stu-id="83b58-114">Consider what these views do:</span></span>

    - <span data-ttu-id="83b58-115">Это действие создает URL-адрес для подписи Azure AD, сохраняет поток, созданный клиентом OAuth, а затем перенаправляет браузер на страницу для регистрации `signin` Azure AD.</span><span class="sxs-lookup"><span data-stu-id="83b58-115">The `signin` action generates the Azure AD signin URL, saves the flow generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

    - <span data-ttu-id="83b58-116">Это `callback` действие перенаправляет Azure после завершения регистрации.</span><span class="sxs-lookup"><span data-stu-id="83b58-116">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="83b58-117">Это действие использует сохраненный поток и строку запроса, отправленную Azure для запроса маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="83b58-117">That action uses the saved flow and the query string sent by Azure to request an access token.</span></span> <span data-ttu-id="83b58-118">Затем он перенаправляется обратно на домашную страницу с ответом во временном значении ошибки.</span><span class="sxs-lookup"><span data-stu-id="83b58-118">It then redirects back to the home page with the response in the temporary error value.</span></span> <span data-ttu-id="83b58-119">Вы будете использовать его, чтобы убедиться, что наш вход работает, прежде чем двигаться дальше.</span><span class="sxs-lookup"><span data-stu-id="83b58-119">You'll use this to verify that our sign-in is working before moving on.</span></span>

1. <span data-ttu-id="83b58-120">Откройте **./tutorial/urls.py** и замените существующие инструкции `path` на `signin` следующие.</span><span class="sxs-lookup"><span data-stu-id="83b58-120">Open **./tutorial/urls.py** and replace the existing `path` statements for `signin` with the following.</span></span>

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. <span data-ttu-id="83b58-121">Добавьте новое `path` для `callback` представления.</span><span class="sxs-lookup"><span data-stu-id="83b58-121">Add a new `path` for the `callback` view.</span></span>

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. <span data-ttu-id="83b58-122">Запустите сервер и перейдите к `https://localhost:8000` .</span><span class="sxs-lookup"><span data-stu-id="83b58-122">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="83b58-123">Нажмите кнопку "Вход" и перенаправите вас на `https://login.microsoftonline.com` .</span><span class="sxs-lookup"><span data-stu-id="83b58-123">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="83b58-124">Войдите в систему с помощью учетной записи Майкрософт и согласиться на запрашиваемую разрешения.</span><span class="sxs-lookup"><span data-stu-id="83b58-124">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="83b58-125">Браузер перенаправляет пользователя в приложение с отображением отклика, включая маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="83b58-125">The browser redirects to the app, showing the response, including the access token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="83b58-126">Получить сведения о пользователе</span><span class="sxs-lookup"><span data-stu-id="83b58-126">Get user details</span></span>

1. <span data-ttu-id="83b58-127">Создайте файл с именем **./tutorial** и `graph_helper.py` добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="83b58-127">Create a new file in the **./tutorial** directory named `graph_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="83b58-128">Метод создает запрос GET к конечной точке Microsoft Graph для получения профиля пользователя с помощью маркера `get_user` `/me` доступа, который вы приобрели ранее.</span><span class="sxs-lookup"><span data-stu-id="83b58-128">The `get_user` method makes a GET request to the Microsoft Graph `/me` endpoint to get the user's profile, using the access token you acquired previously.</span></span>

1. <span data-ttu-id="83b58-129">`callback`Обновив метод в **./tutorial/views.py,** чтобы получить профиль пользователя из Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="83b58-129">Update the `callback` method in **./tutorial/views.py** to get the user's profile from Microsoft Graph.</span></span> <span data-ttu-id="83b58-130">Добавьте следующий `import` выписку в верхнюю часть файла.</span><span class="sxs-lookup"><span data-stu-id="83b58-130">Add the following `import` statement to the top of the file.</span></span>

    ```python
    from tutorial.graph_helper import *
    ```

1. <span data-ttu-id="83b58-131">Замените `callback` метод следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="83b58-131">Replace the `callback` method with the following code.</span></span>

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)

      #Get the user's profile
      user = get_user(result['access_token'])
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': 'User: {0}\nToken: {1}'.format(user, result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    <span data-ttu-id="83b58-132">Новый код вызывает метод `get_user` для запроса профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="83b58-132">The new code calls the `get_user` method to request the user's profile.</span></span> <span data-ttu-id="83b58-133">Он добавляет объект пользователя во временные выходные данные для тестирования.</span><span class="sxs-lookup"><span data-stu-id="83b58-133">It adds the user object to the temporary output for testing.</span></span>

1. <span data-ttu-id="83b58-134">Добавьте следующие новые методы в **./tutorial/auth_helper.py.**</span><span class="sxs-lookup"><span data-stu-id="83b58-134">Add the following new methods to **./tutorial/auth_helper.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="SecondCodeSnippet":::

1. <span data-ttu-id="83b58-135">`callback`Обновите функцию **в ./tutorial/views.py,** чтобы сохранить пользователя в сеансе и перенаправить обратно на главную страницу.</span><span class="sxs-lookup"><span data-stu-id="83b58-135">Update the `callback` function in **./tutorial/views.py** to store the user in the session and redirect back to the main page.</span></span> <span data-ttu-id="83b58-136">Замените `from tutorial.auth_helper import get_sign_in_flow, get_token_from_code` строку на следующую.</span><span class="sxs-lookup"><span data-stu-id="83b58-136">Replace the `from tutorial.auth_helper import get_sign_in_flow, get_token_from_code` line with the following.</span></span>

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code, store_user, remove_user_and_token, get_token
    ```

1. <span data-ttu-id="83b58-137">Замените `callback` метод на следующий.</span><span class="sxs-lookup"><span data-stu-id="83b58-137">Replace the `callback` method with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="83b58-138">Реализация выходов</span><span class="sxs-lookup"><span data-stu-id="83b58-138">Implement sign-out</span></span>

1. <span data-ttu-id="83b58-139">Добавьте новое представление `sign_out` в **./tutorial/views.py.**</span><span class="sxs-lookup"><span data-stu-id="83b58-139">Add a new `sign_out` view in **./tutorial/views.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. <span data-ttu-id="83b58-140">Откройте **./tutorial/urls.py** и замените существующие инструкции `path` на `signout` следующие.</span><span class="sxs-lookup"><span data-stu-id="83b58-140">Open **./tutorial/urls.py** and replace the existing `path` statements for `signout` with the following.</span></span>

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. <span data-ttu-id="83b58-141">Перезапустите сервер и войдите в нее.</span><span class="sxs-lookup"><span data-stu-id="83b58-141">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="83b58-142">Вы должны вернуться на home-страницу, но пользовательский интерфейс должен измениться, чтобы указать, что вы вписались.</span><span class="sxs-lookup"><span data-stu-id="83b58-142">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Снимок экрана с домашней страницей после входов](./images/add-aad-auth-01.png)

1. <span data-ttu-id="83b58-144">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **"Выйти".**</span><span class="sxs-lookup"><span data-stu-id="83b58-144">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="83b58-145">При **нажатии** кнопки "Выйти" сеанс сбрасывается и возвращается на домашней странице.</span><span class="sxs-lookup"><span data-stu-id="83b58-145">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Снимок экрана с выпадающим меню со ссылкой "Выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="83b58-147">Обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="83b58-147">Refreshing tokens</span></span>

<span data-ttu-id="83b58-148">На этом этапе приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="83b58-148">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="83b58-149">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="83b58-149">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="83b58-150">Однако этот маркер является кратковременной.</span><span class="sxs-lookup"><span data-stu-id="83b58-150">However, this token is short-lived.</span></span> <span data-ttu-id="83b58-151">Срок действия маркера истекает через час после его выпуска.</span><span class="sxs-lookup"><span data-stu-id="83b58-151">The token expires an hour after it is issued.</span></span> <span data-ttu-id="83b58-152">В этом случае маркер обновления становится полезным.</span><span class="sxs-lookup"><span data-stu-id="83b58-152">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="83b58-153">Маркер обновления позволяет приложению запрашивать новый маркер доступа, не требуя от пользователя повторного входить.</span><span class="sxs-lookup"><span data-stu-id="83b58-153">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="83b58-154">Поскольку в этом примере используется MSAL, не нужно писать код для обновления маркера.</span><span class="sxs-lookup"><span data-stu-id="83b58-154">Because this sample uses MSAL, you do not have to write any specific code to refresh the token.</span></span> <span data-ttu-id="83b58-155">Метод MSAL при необходимости обрабатывает обновление `acquire_token_silent` маркера.</span><span class="sxs-lookup"><span data-stu-id="83b58-155">MSAL's `acquire_token_silent` method handles refreshing the token if needed.</span></span>
