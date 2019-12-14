<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. На этом этапе в приложение будет интегрироваться библиотека [запросов – OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) .

Создайте в корневом каталоге проекта новый файл с именем `oauth_settings.yml`и добавьте приведенный ниже контент.

```text
app_id: "YOUR_APP_ID_HERE"
app_secret: "YOUR_APP_PASSWORD_HERE"
redirect: "http://localhost:8000/tutorial/callback"
scopes: "openid profile offline_access user.read calendars.read"
authority: "https://login.microsoftonline.com/common"
authorize_endpoint: "/oauth2/v2.0/authorize"
token_endpoint: "/oauth2/v2.0/token"
```

Замените `YOUR_APP_ID_HERE` идентификатором приложения на портале регистрации приложений и замените `YOUR_APP_SECRET_HERE` созданным паролем.

> [!IMPORTANT]
> Если вы используете систему управления версиями (например, Git), то в дальнейшем будет полезно исключить `oauth_settings.yml` файл из системы управления версиями, чтобы избежать непреднамеренного утечки идентификатора и пароля приложения.

## <a name="implement-sign-in"></a>Реализация входа

Создайте новый файл в `./tutorial` каталоге `auth_helper.py` и добавьте указанный ниже код.

```python
import yaml
from requests_oauthlib import OAuth2Session
import os
import time

# This is necessary for testing with non-HTTPS localhost
# Remove this if deploying to production
os.environ['OAUTHLIB_INSECURE_TRANSPORT'] = '1'

# This is necessary because Azure does not guarantee
# to return scopes in the same case and order as requested
os.environ['OAUTHLIB_RELAX_TOKEN_SCOPE'] = '1'
os.environ['OAUTHLIB_IGNORE_SCOPE_CHANGE'] = '1'

# Load the oauth_settings.yml file
stream = open('oauth_settings.yml', 'r')
settings = yaml.load(stream, yaml.SafeLoader)
authorize_url = '{0}{1}'.format(settings['authority'], settings['authorize_endpoint'])
token_url = '{0}{1}'.format(settings['authority'], settings['token_endpoint'])

# Method to generate a sign-in url
def get_sign_in_url():
  # Initialize the OAuth client
  aad_auth = OAuth2Session(settings['app_id'],
    scope=settings['scopes'],
    redirect_uri=settings['redirect'])

  sign_in_url, state = aad_auth.authorization_url(authorize_url, prompt='login')

  return sign_in_url, state

# Method to exchange auth code for access token
def get_token_from_code(callback_url, expected_state):
  # Initialize the OAuth client
  aad_auth = OAuth2Session(settings['app_id'],
    state=expected_state,
    scope=settings['scopes'],
    redirect_uri=settings['redirect'])

  token = aad_auth.fetch_token(token_url,
    client_secret = settings['app_secret'],
    authorization_response=callback_url)

  return token
```

Этот файл будет содержать все методы, связанные с проверкой подлинности. `get_sign_in_url` Создается URL-адрес авторизации, а `get_token_from_code` метод обменивается с ответом авторизации для маркера доступа.

Добавьте приведенные `import` ниже операторы в верхнюю часть `./tutorial/views.py`.

```python
from django.urls import reverse
from tutorial.auth_helper import get_sign_in_url, get_token_from_code
```

Теперь добавьте несколько новых представлений в `./tutorial/views.py` файл.

```python
def sign_in(request):
  # Get the sign-in URL
  sign_in_url, state = get_sign_in_url()
  # Save the expected state so we can validate in the callback
  request.session['auth_state'] = state
  # Redirect to the Azure sign-in page
  return HttpResponseRedirect(sign_in_url)

def callback(request):
  # Get the state saved in session
  expected_state = request.session.pop('auth_state', '')
  # Make the token request
  token = get_token_from_code(request.get_full_path(), expected_state)
  # Temporary! Save the response in an error so it's displayed
  request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(token) }
  return HttpResponseRedirect(reverse('home'))
```

Определяет два новых представления: `signin` и. `callback`

`signin` Действие создает URL-адрес входа Azure AD, сохраняет `state` значение, созданное клиентом OAuth, а затем перенаправляет браузер на страницу входа Azure AD.

`callback` Действие перенаправляет Azure после завершения входа. Это действие гарантирует, что `state` значение соответствует сохраненному значению, а затем использует код проверки подлинности, отправленный Azure для запроса маркера доступа. Затем он перенаправляется обратно на домашнюю страницу с маркером доступа во временном значении ошибки. Вы будете использовать этот параметр, чтобы проверить, работает ли наш вход перед переходом. Прежде чем выполнять тестирование, необходимо добавить представления в `./tutorial/urls.py`.

```python
path('signin', views.sign_in, name='signin'),
path('callback', views.callback, name='callback'),
```

Замените `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` строку на `./tutorial/templates/tutorial/home.html` приведенную ниже строку.

```html
<a href="{% url 'signin' %}" class="btn btn-primary btn-large">Click here to sign in</a>
```

Замените `<a href="#" class="nav-link">Sign In</a>` строку на `./tutorial/templates/tutorial/layout.html` приведенную ниже строку.

```html
<a href="{% url 'signin' %}" class="nav-link">Sign In</a>
```

Запустите сервер и перейдите к `https://localhost:8000/tutorial`. Нажмите кнопку входа, и вы будете перенаправлены на `https://login.microsoftonline.com`. Войдите с помощью учетной записи Майкрософт и согласия с запрошенными разрешениями. Браузер перенаправляется на приложение, отображая маркер.

### <a name="get-user-details"></a>Получение сведений о пользователе

Создайте новый файл в `./tutorial` каталоге `graph_helper.py` и добавьте указанный ниже код.

```python
from requests_oauthlib import OAuth2Session

graph_url = 'https://graph.microsoft.com/v1.0'

def get_user(token):
  graph_client = OAuth2Session(token=token)
  # Send GET to /me
  user = graph_client.get('{0}/me'.format(graph_url))
  # Return the JSON result
  return user.json()
```

`get_user` Метод создает запрос GET к конечной точке Microsoft Graph `/me` для получения профиля пользователя с помощью маркера доступа, полученного ранее.

Обновите `callback` метод в `./tutorial/views.py` , чтобы получить профиль пользователя из Microsoft Graph.

Сначала добавьте следующий `import` оператор в начало файла.

```python
from tutorial.graph_helper import get_user
```

Замените `callback` метод на приведенный ниже код.

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

Новый код вызывает `get_user` метод, чтобы запросить профиль пользователя. Он добавляет объект пользователя во временный выход для тестирования.

## <a name="storing-the-tokens"></a>Сохранение маркеров

Теперь, когда вы можете получить маркеры, следует реализовать способ их хранения в приложении. Так как это пример приложения, для простоты вы будете хранить их в сеансе. Реальное приложение использует более надежное решение для безопасного хранения, например базу данных.

Добавьте следующие новые методы в `./tutorial/auth_helper.py`.

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

Затем обновите `callback` функцию в `./tutorial/views.py` , чтобы сохранить маркеры в сеансе и переадресовать обратно на главную страницу. Замените `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` строку на приведенную ниже строку.

```python
from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
```

Замените `callback` метод на приведенный ниже.

```python
def callback(request):
  # Get the state saved in session
  expected_state = request.session.pop('auth_state', '')
  # Make the token request
  token = get_token_from_code(request.get_full_path(), expected_state)

  # Get the user's profile
  user = get_user(token)

  # Save token and user
  store_token(request, token)
  store_user(request, user)

  return HttpResponseRedirect(reverse('home'))
```

## <a name="implement-sign-out"></a>Реализация выхода

Перед тестированием новой функции добавьте способ выхода. Добавьте новое `sign_out` представление в `./tutorial/views.py`.

```python
def sign_out(request):
  # Clear out the user and token
  remove_user_and_token(request)

  return HttpResponseRedirect(reverse('home'))
```

Теперь добавьте это представление в `./tutorial/urls.py`.

```python
path('signout', views.sign_out, name='signout'),
```

Обновите ссылку **выхода** в `./tutorial/templates/tutorial/layout.html` , чтобы использовать это новое представление. Замените `<a href="#" class="dropdown-item">Sign Out</a>` строку на приведенную ниже строку.

```html
<a href="{% url 'signout' %}" class="dropdown-item">Sign Out</a>
```

Перезапустите сервер и пройдите процесс входа. Необходимо вернуться на домашнюю страницу, но пользовательский интерфейс должен измениться, чтобы показать, что вы вошли в систему.

![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** . При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.

![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Обновление маркеров

На этом шаге приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API. Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.

Однако этот маркер кратковременно используется. Срок действия маркера истечет через час после его выдачи. В этом случае маркер обновления становится полезен. Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа. Обновите код управления маркером, чтобы реализовать обновление маркеров.

Замените существующий `get_token` метод на `./tutorial/auth_helper.py` приведенный ниже.

```python
def get_token(request):
  token = request.session['oauth_token']
  if token != None:
    # Check expiration
    now = time.time()
    # Subtract 5 minutes from expiration to account for clock skew
    expire_time = token['expires_at'] - 300
    if now >= expire_time:
      # Refresh the token
      aad_auth = OAuth2Session(settings['app_id'],
        token = token,
        scope=settings['scopes'],
        redirect_uri=settings['redirect'])

      refresh_params = {
        'client_id': settings['app_id'],
        'client_secret': settings['app_secret'],
      }
      new_token = aad_auth.refresh_token(token_url, **refresh_params)

      # Save new token
      store_token(request, new_token)

      # Return new access token
      return new_token

    else:
      # Token still valid, just return it
      return token
```

Этот метод сначала проверяет, истек ли срок действия маркера доступа, или закройте его до истечения срока действия. Если это так, то он использует маркер обновления для получения новых токенов, затем обновляет кэш и возвращает новый маркер доступа.
