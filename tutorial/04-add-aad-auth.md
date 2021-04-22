<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы расширит приложение от предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. На этом этапе вы интегрируете [библиотеку MSAL для Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) в приложение.

1. Создайте новый файл в корне проекта с именем `oauth_settings.yml` и добавьте следующий контент.

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. Замените код приложения с портала регистрации приложений `YOUR_APP_ID_HERE` и `YOUR_APP_SECRET_HERE` замените созданный пароль.

> [!IMPORTANT]
> Если вы используете источник управления, например git, сейчас самое время исключить файл **oauth_settings.yml** из источника управления, чтобы избежать случайной утечки вашего ID и пароля приложения.

## <a name="implement-sign-in"></a>Реализация входа в систему

1. Создайте новый файл в **каталоге ./tutorial** с именем `auth_helper.py` и добавьте следующий код.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    Этот файл будет держать все методы проверки подлинности. Создает URL-адрес авторизации, а метод обменивается ответом на авторизацию `get_sign_in_flow` `get_token_from_code` маркера доступа.

1. Добавьте следующие `import` утверждения в **верхнюю часть ./tutorial/views.py**.

    ```python
    from dateutil import tz, parser
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code
    ```

1. Добавьте представление входной точки в **файле ./tutorial/views.py.**

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. Добавьте представление вызова в **файле ./tutorial/views.py.**

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    Рассмотрим, что делают эти представления:

    - Действие создает URL-адрес подписи Azure AD, сохраняет поток, созданный клиентом OAuth, а затем перенаправляет браузер на страницу `signin` подписи Azure AD.

    - Действие `callback` — это перенаправление Azure после завершения signin. В этом действии используется сохраненный поток и строка запросов, отправленная Azure для запроса маркера доступа. Затем он перенаправляет обратно на домашную страницу с ответом в значении временной ошибки. Вы будете использовать это, чтобы убедиться, что наша входная группа работает перед тем, как двигаться дальше.

1. Откройте **./tutorial/urls.py** и замените существующие инструкции `path` на `signin` следующие.

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. Добавьте новое `path` для `callback` представления.

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. Запустите сервер и просмотрите `https://localhost:8000` . Нажмите кнопку вход, и вы должны быть перенаправлены `https://login.microsoftonline.com` на . Вход с учетной записью Майкрософт и согласие на запрашиваемую разрешения. Браузер перенаправляет в приложение, демонстрируя ответ, в том числе маркер доступа.

### <a name="get-user-details"></a>Получение сведений о пользователе

1. Создайте новый файл в **каталоге ./tutorial** с именем `graph_helper.py` и добавьте следующий код.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    Метод делает запрос GET в конечную точку Microsoft Graph, чтобы получить профиль пользователя с помощью приобретенного ранее маркера `get_user` `/me` доступа.

1. `callback`Обнови метод **в ./tutorial/views.py,** чтобы получить профиль пользователя из Microsoft Graph. Добавьте указанный ниже оператор `import` в начало файла.

    ```python
    from tutorial.graph_helper import *
    ```

1. Замените метод `callback` следующим кодом.

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

    Новый код вызывает метод `get_user` для запроса профиля пользователя. Он добавляет объект пользователя к временному выходу для тестирования.

1. Добавьте следующие новые методы **в ./tutorial/auth_helper.py**.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="SecondCodeSnippet":::

1. `callback`Обнови функцию **в ./tutorial/views.py,** чтобы сохранить пользователя на сеансе и перенаправить обратно на главную страницу. Замените `from tutorial.auth_helper import get_sign_in_flow, get_token_from_code` строку следующей.

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code, store_user, remove_user_and_token, get_token
    ```

1. Замените `callback` метод следующим.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a>Реализация выходов

1. Добавьте новое `sign_out` представление **в ./tutorial/views.py**.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. Откройте **./tutorial/urls.py** и замените существующие инструкции `path` на `signout` следующие.

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. Перезапустите сервер и пройдите процедуру регистрации. Вы должны вернуться на домашняя страница, но пользовательский интерфейс должен измениться, чтобы указать, что вы подписаны.

    ![Снимок экрана с домашней страницей после входа в систему](./images/add-aad-auth-01.png)

1. Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к **ссылке Sign Out.** **Щелкнув кнопку "Выйти",** вы сбросит сеанс и возвращает вас на домашнюю страницу.

    ![Снимок экрана с раскрывающимся меню с пунктом "Выход"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Обновление маркеров

На этом этапе у приложения есть маркер доступа, который отправляется в `Authorization` заголовке вызовов API. Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.

Однако этот маркер недолговечен. Срок действия маркера истекает через час после его выпуска. Вот здесь и пригодится маркер обновления. Маркер обновления позволяет приложению запрашивать новый маркер доступа, не требуя от пользователя повторного входа в систему.

Так как в этом примере используется MSAL, для обновления маркера не нужно писать какой-либо определенный код. Метод MSAL обрабатывает `acquire_token_silent` обновление маркера при необходимости.
