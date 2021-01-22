<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы расширим приложение из предыдущего упражнения, чтобы поддерживать проверку подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. На этом этапе вы интегрируете [библиотеку MSAL для Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) в приложение.

1. Создайте файл в корневой папке проекта с именем `oauth_settings.yml` и добавьте следующее содержимое.

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. Замените код приложения на портале регистрации приложений `YOUR_APP_ID_HERE` и пароль, `YOUR_APP_SECRET_HERE` созданный вами.

> [!IMPORTANT]
> Если вы используете управление исходным кодом, например git, пришло бы время исключить файл **oauth_settings.yml** из системы управления исходным кодом, чтобы избежать случайной утечки ИД приложения и пароля.

## <a name="implement-sign-in"></a>Реализация входов

1. Создайте файл с именем **./tutorial** и `auth_helper.py` добавьте следующий код.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    Этот файл будет использовать все ваши методы проверки подлинности. Создает URL-адрес авторизации, а метод обменивается ответом `get_sign_in_flow` `get_token_from_code` авторизации на маркер доступа.

1. Добавьте следующий `import` инструкции в **верхнюю часть ./tutorial/views.py**.

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code
    ```

1. Добавьте представление для входов в файл **./tutorial/views.py.**

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. Добавьте представление вызова в файл **./tutorial/views.py.**

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    Подумайте, что делают эти представления:

    - Это действие создает URL-адрес для подписи Azure AD, сохраняет поток, созданный клиентом OAuth, а затем перенаправляет браузер на страницу для регистрации `signin` Azure AD.

    - Это `callback` действие перенаправляет Azure после завершения регистрации. Это действие использует сохраненный поток и строку запроса, отправленную Azure для запроса маркера доступа. Затем он перенаправляется обратно на домашную страницу с ответом во временном значении ошибки. Вы будете использовать его, чтобы убедиться, что наш вход работает, прежде чем двигаться дальше.

1. Откройте **./tutorial/urls.py** и замените существующие инструкции `path` на `signin` следующие.

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. Добавьте новое `path` для `callback` представления.

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. Запустите сервер и перейдите к `https://localhost:8000` . Нажмите кнопку "Вход" и перенаправите вас на `https://login.microsoftonline.com` . Войдите в систему с помощью учетной записи Майкрософт и согласиться на запрашиваемую разрешения. Браузер перенаправляет пользователя в приложение с отображением отклика, включая маркер доступа.

### <a name="get-user-details"></a>Получить сведения о пользователе

1. Создайте файл с именем **./tutorial** и `graph_helper.py` добавьте следующий код.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    Метод создает запрос GET к конечной точке Microsoft Graph для получения профиля пользователя с помощью маркера `get_user` `/me` доступа, который вы приобрели ранее.

1. `callback`Обновив метод в **./tutorial/views.py,** чтобы получить профиль пользователя из Microsoft Graph. Добавьте следующий `import` выписку в верхнюю часть файла.

    ```python
    from tutorial.graph_helper import *
    ```

1. Замените `callback` метод следующим кодом.

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

    Новый код вызывает метод `get_user` для запроса профиля пользователя. Он добавляет объект пользователя во временные выходные данные для тестирования.

1. Добавьте следующие новые методы в **./tutorial/auth_helper.py.**

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="SecondCodeSnippet":::

1. `callback`Обновите функцию **в ./tutorial/views.py,** чтобы сохранить пользователя в сеансе и перенаправить обратно на главную страницу. Замените `from tutorial.auth_helper import get_sign_in_flow, get_token_from_code` строку на следующую.

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code, store_user, remove_user_and_token, get_token
    ```

1. Замените `callback` метод на следующий.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a>Реализация выходов

1. Добавьте новое представление `sign_out` в **./tutorial/views.py.**

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. Откройте **./tutorial/urls.py** и замените существующие инструкции `path` на `signout` следующие.

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. Перезапустите сервер и войдите в нее. Вы должны вернуться на home-страницу, но пользовательский интерфейс должен измениться, чтобы указать, что вы вписались.

    ![Снимок экрана с домашней страницей после входов](./images/add-aad-auth-01.png)

1. Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **"Выйти".** При **нажатии** кнопки "Выйти" сеанс сбрасывается и возвращается на домашней странице.

    ![Снимок экрана с выпадающим меню со ссылкой "Выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Обновление маркеров

На этом этапе приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API. Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.

Однако этот маркер является кратковременной. Срок действия маркера истекает через час после его выпуска. В этом случае маркер обновления становится полезным. Маркер обновления позволяет приложению запрашивать новый маркер доступа, не требуя от пользователя повторного входить.

Поскольку в этом примере используется MSAL, не нужно писать код для обновления маркера. Метод MSAL при необходимости обрабатывает обновление `acquire_token_silent` маркера.
