<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f810f-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="f810f-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="f810f-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="f810f-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="f810f-103">На этом этапе в приложение будет интегрироваться библиотека [запросов – OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) .</span><span class="sxs-lookup"><span data-stu-id="f810f-103">In this step you will integrate the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library into the application.</span></span>

1. <span data-ttu-id="f810f-104">Создайте в корневом каталоге проекта новый файл с именем `oauth_settings.yml`и добавьте приведенный ниже контент.</span><span class="sxs-lookup"><span data-stu-id="f810f-104">Create a new file in the root of the project named `oauth_settings.yml`, and add the following content.</span></span>

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. <span data-ttu-id="f810f-105">Замените `YOUR_APP_ID_HERE` идентификатором приложения на портале регистрации приложений и замените `YOUR_APP_SECRET_HERE` созданным паролем.</span><span class="sxs-lookup"><span data-stu-id="f810f-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f810f-106">Если вы используете систему управления версиями (например, Git), то теперь следует исключить из системы управления версиями файл **oauth_settings. yml** , чтобы избежать непреднамеренного утечки идентификатора и пароля приложения.</span><span class="sxs-lookup"><span data-stu-id="f810f-106">If you're using source control such as git, now would be a good time to exclude the **oauth_settings.yml** file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="f810f-107">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="f810f-107">Implement sign-in</span></span>

1. <span data-ttu-id="f810f-108">Создайте новый файл в каталоге **./туториал** `auth_helper.py` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="f810f-108">Create a new file in the **./tutorial** directory named `auth_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="f810f-109">Этот файл будет содержать все методы, связанные с проверкой подлинности.</span><span class="sxs-lookup"><span data-stu-id="f810f-109">This file will hold all of your authentication-related methods.</span></span> <span data-ttu-id="f810f-110">`get_sign_in_url` Создается URL-адрес авторизации, а `get_token_from_code` метод обменивается с ответом авторизации для маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="f810f-110">The `get_sign_in_url` generates an authorization URL, and the `get_token_from_code` method exchanges the authorization response for an access token.</span></span>

1. <span data-ttu-id="f810f-111">Добавьте следующие `import` операторы в верхнюю часть файла **./туториал/виевс.Пи**.</span><span class="sxs-lookup"><span data-stu-id="f810f-111">Add the following `import` statements to the top of **./tutorial/views.py**.</span></span>

    ```python
    from django.urls import reverse
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code
    ```

1. <span data-ttu-id="f810f-112">Добавьте представление входа в файл **./туториал/виевс.Пи** .</span><span class="sxs-lookup"><span data-stu-id="f810f-112">Add a sign-in view in the **./tutorial/views.py** file.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. <span data-ttu-id="f810f-113">Добавьте представление обратного вызова в файл **./туториал/виевс.Пи** .</span><span class="sxs-lookup"><span data-stu-id="f810f-113">Add a callback view in the **./tutorial/views.py** file.</span></span>

    ```python
    def callback(request):
      # Get the state saved in session
      expected_state = request.session.pop('auth_state', '')
      # Make the token request
      token = get_token_from_code(request.get_full_path(), expected_state)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(token) }
      return HttpResponseRedirect(reverse('home'))
    ```

    <span data-ttu-id="f810f-114">Рассмотрите то, что делает эти представления:</span><span class="sxs-lookup"><span data-stu-id="f810f-114">Consider what these views do:</span></span>

    - <span data-ttu-id="f810f-115">`signin` Действие создает URL-адрес входа Azure AD, сохраняет `state` значение, созданное клиентом OAuth, а затем перенаправляет браузер на страницу входа Azure AD.</span><span class="sxs-lookup"><span data-stu-id="f810f-115">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

    - <span data-ttu-id="f810f-116">`callback` Действие перенаправляет Azure после завершения входа.</span><span class="sxs-lookup"><span data-stu-id="f810f-116">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="f810f-117">Это действие гарантирует, что `state` значение соответствует сохраненному значению, а затем использует код проверки подлинности, отправленный Azure для запроса маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="f810f-117">That action makes sure the `state` value matches the saved value, then uses the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="f810f-118">Затем он перенаправляется обратно на домашнюю страницу с маркером доступа во временном значении ошибки.</span><span class="sxs-lookup"><span data-stu-id="f810f-118">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="f810f-119">Вы будете использовать этот параметр, чтобы проверить, работает ли наш вход перед переходом.</span><span class="sxs-lookup"><span data-stu-id="f810f-119">You'll use this to verify that our sign-in is working before moving on.</span></span>

1. <span data-ttu-id="f810f-120">Откройте **./туториал/урлс.Пи** и замените существующие `path` операторы `signin` на приведенные ниже.</span><span class="sxs-lookup"><span data-stu-id="f810f-120">Open **./tutorial/urls.py** and replace the existing `path` statements for `signin` with the following.</span></span>

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. <span data-ttu-id="f810f-121">Добавление нового `path` `callback` представления.</span><span class="sxs-lookup"><span data-stu-id="f810f-121">Add a new `path` for the `callback` view.</span></span>

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. <span data-ttu-id="f810f-122">Запустите сервер и перейдите к `https://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="f810f-122">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="f810f-123">Нажмите кнопку входа, и вы будете перенаправлены на `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="f810f-123">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="f810f-124">Войдите с помощью учетной записи Майкрософт и согласия с запрошенными разрешениями.</span><span class="sxs-lookup"><span data-stu-id="f810f-124">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="f810f-125">Браузер перенаправляется на приложение, отображая маркер.</span><span class="sxs-lookup"><span data-stu-id="f810f-125">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="f810f-126">Получение сведений о пользователе</span><span class="sxs-lookup"><span data-stu-id="f810f-126">Get user details</span></span>

1. <span data-ttu-id="f810f-127">Создайте новый файл в каталоге **./туториал** `graph_helper.py` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="f810f-127">Create a new file in the **./tutorial** directory named `graph_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="f810f-128">`get_user` Метод создает запрос GET к конечной точке Microsoft Graph `/me` для получения профиля пользователя с помощью маркера доступа, полученного ранее.</span><span class="sxs-lookup"><span data-stu-id="f810f-128">The `get_user` method makes a GET request to the Microsoft Graph `/me` endpoint to get the user's profile, using the access token you acquired previously.</span></span>

1. <span data-ttu-id="f810f-129">Обновите `callback` метод в файле **./туториал/виевс.Пи** , чтобы получить профиль пользователя из Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="f810f-129">Update the `callback` method in **./tutorial/views.py** to get the user's profile from Microsoft Graph.</span></span> <span data-ttu-id="f810f-130">Добавьте следующий `import` оператор в начало файла.</span><span class="sxs-lookup"><span data-stu-id="f810f-130">Add the following `import` statement to the top of the file.</span></span>

    ```python
    from tutorial.graph_helper import get_user
    ```

1. <span data-ttu-id="f810f-131">Замените `callback` метод на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="f810f-131">Replace the `callback` method with the following code.</span></span>

    ```python
    def callback(request):
      # Get the state saved in session
      expected_state = request.session.pop('auth_state', '')
      # Make the token request
      token = get_token_from_code(request.get_full_path(), expected_state)

      # Get the user's profile
      user = get_user(token)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved',
        'debug': 'User: {0}\nToken: {1}'.format(user, token) }
      return HttpResponseRedirect(reverse('home'))
    ```

<span data-ttu-id="f810f-132">Новый код вызывает `get_user` метод, чтобы запросить профиль пользователя.</span><span class="sxs-lookup"><span data-stu-id="f810f-132">The new code calls the `get_user` method to request the user's profile.</span></span> <span data-ttu-id="f810f-133">Он добавляет объект пользователя во временный выход для тестирования.</span><span class="sxs-lookup"><span data-stu-id="f810f-133">It adds the user object to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="f810f-134">Сохранение маркеров</span><span class="sxs-lookup"><span data-stu-id="f810f-134">Storing the tokens</span></span>

<span data-ttu-id="f810f-135">Теперь, когда вы можете получить маркеры, следует реализовать способ их хранения в приложении.</span><span class="sxs-lookup"><span data-stu-id="f810f-135">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="f810f-136">Так как это пример приложения, для простоты вы будете хранить их в сеансе.</span><span class="sxs-lookup"><span data-stu-id="f810f-136">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="f810f-137">Реальное приложение использует более надежное решение для безопасного хранения, например базу данных.</span><span class="sxs-lookup"><span data-stu-id="f810f-137">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

1. <span data-ttu-id="f810f-138">Добавьте следующие новые методы в файле **./туториал/auth_helper. корректировки**.</span><span class="sxs-lookup"><span data-stu-id="f810f-138">Add the following new methods to **./tutorial/auth_helper.py**.</span></span>

    ```python
    def store_token(request, token):
      request.session['oauth_token'] = token

    def store_user(request, user):
      request.session['user'] = {
        'is_authenticated': True,
        'name': user['displayName'],
        'email': user['mail'] if (user['mail'] != None) else user['userPrincipalName']
      }

    def get_token(request):
      token = request.session['oauth_token']
      return token

    def remove_user_and_token(request):
      if 'oauth_token' in request.session:
        del request.session['oauth_token']

      if 'user' in request.session:
        del request.session['user']
    ```

1. <span data-ttu-id="f810f-139">Обновите `callback` функцию в файле **./туториал/виевс.Пи** , чтобы сохранить маркеры в сеансе и переадресовать обратно на главную страницу.</span><span class="sxs-lookup"><span data-stu-id="f810f-139">Update the `callback` function in **./tutorial/views.py** to store the tokens in the session and redirect back to the main page.</span></span> <span data-ttu-id="f810f-140">Замените `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` строку на приведенную ниже строку.</span><span class="sxs-lookup"><span data-stu-id="f810f-140">Replace the `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` line with the following.</span></span>

    ```python
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
    ```

1. <span data-ttu-id="f810f-141">Замените `callback` метод на приведенный ниже.</span><span class="sxs-lookup"><span data-stu-id="f810f-141">Replace the `callback` method with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="f810f-142">Реализация выхода</span><span class="sxs-lookup"><span data-stu-id="f810f-142">Implement sign-out</span></span>

1. <span data-ttu-id="f810f-143">Добавление нового `sign_out` представления в файле **./туториал/виевс.Пи**.</span><span class="sxs-lookup"><span data-stu-id="f810f-143">Add a new `sign_out` view in **./tutorial/views.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. <span data-ttu-id="f810f-144">Откройте **./туториал/урлс.Пи** и замените существующие `path` операторы `signout` на приведенные ниже.</span><span class="sxs-lookup"><span data-stu-id="f810f-144">Open **./tutorial/urls.py** and replace the existing `path` statements for `signout` with the following.</span></span>

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. <span data-ttu-id="f810f-145">Перезапустите сервер и пройдите процесс входа.</span><span class="sxs-lookup"><span data-stu-id="f810f-145">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="f810f-146">Необходимо вернуться на домашнюю страницу, но пользовательский интерфейс должен измениться, чтобы показать, что вы вошли в систему.</span><span class="sxs-lookup"><span data-stu-id="f810f-146">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

1. <span data-ttu-id="f810f-148">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** .</span><span class="sxs-lookup"><span data-stu-id="f810f-148">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="f810f-149">При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.</span><span class="sxs-lookup"><span data-stu-id="f810f-149">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="f810f-151">Обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="f810f-151">Refreshing tokens</span></span>

<span data-ttu-id="f810f-152">На этом шаге приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="f810f-152">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="f810f-153">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="f810f-153">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="f810f-154">Однако этот маркер кратковременно используется.</span><span class="sxs-lookup"><span data-stu-id="f810f-154">However, this token is short-lived.</span></span> <span data-ttu-id="f810f-155">Срок действия маркера истечет через час после его выдачи.</span><span class="sxs-lookup"><span data-stu-id="f810f-155">The token expires an hour after it is issued.</span></span> <span data-ttu-id="f810f-156">В этом случае маркер обновления становится полезен.</span><span class="sxs-lookup"><span data-stu-id="f810f-156">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="f810f-157">Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа.</span><span class="sxs-lookup"><span data-stu-id="f810f-157">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="f810f-158">Обновите код управления маркером, чтобы реализовать обновление маркеров.</span><span class="sxs-lookup"><span data-stu-id="f810f-158">Update the token management code to implement token refresh.</span></span>

1. <span data-ttu-id="f810f-159">Замените существующий `get_token` метод в файле **./туториал/auth_helper. корректировки** на следующий.</span><span class="sxs-lookup"><span data-stu-id="f810f-159">Replace the existing `get_token` method in **./tutorial/auth_helper.py** with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="GetTokenSnippet":::

    <span data-ttu-id="f810f-160">Этот метод сначала проверяет, истек ли срок действия маркера доступа, или закройте его до истечения срока действия.</span><span class="sxs-lookup"><span data-stu-id="f810f-160">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="f810f-161">Если это так, то он использует маркер обновления для получения новых токенов, затем обновляет кэш и возвращает новый маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="f810f-161">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
