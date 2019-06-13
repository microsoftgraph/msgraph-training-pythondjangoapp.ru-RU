<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="22013-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="22013-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="22013-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="22013-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="22013-103">На этом этапе в приложение будет интегрироваться библиотека [запросов – OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) .</span><span class="sxs-lookup"><span data-stu-id="22013-103">In this step you will integrate the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library into the application.</span></span>

<span data-ttu-id="22013-104">Создайте в корневом каталоге проекта новый файл с именем `oauth_settings.yml`и добавьте приведенный ниже контент.</span><span class="sxs-lookup"><span data-stu-id="22013-104">Create a new file in the root of the project named `oauth_settings.yml`, and add the following content.</span></span>

```text
app_id: YOUR_APP_ID_HERE
app_secret: YOUR_APP_PASSWORD_HERE
redirect: http://localhost:8000/tutorial/callback
scopes: openid profile offline_access user.read calendars.read
authority: https://login.microsoftonline.com/common
authorize_endpoint: /oauth2/v2.0/authorize
token_endpoint: /oauth2/v2.0/token
```

<span data-ttu-id="22013-105">Замените `YOUR_APP_ID_HERE` идентификатором приложения на портале регистрации приложений и замените `YOUR_APP_SECRET_HERE` созданным паролем.</span><span class="sxs-lookup"><span data-stu-id="22013-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="22013-106">Если вы используете систему управления версиями (например, Git), то в дальнейшем будет полезно исключить `oauth_settings.yml` файл из системы управления версиями, чтобы избежать непреднамеренного утечки идентификатора и пароля приложения.</span><span class="sxs-lookup"><span data-stu-id="22013-106">If you're using source control such as git, now would be a good time to exclude the `oauth_settings.yml` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="22013-107">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="22013-107">Implement sign-in</span></span>

<span data-ttu-id="22013-108">Создайте новый файл в `./tutorial` каталоге `auth_helper.py` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="22013-108">Create a new file in the `./tutorial` directory named `auth_helper.py` and add the following code.</span></span>

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

<span data-ttu-id="22013-109">Этот файл будет содержать все методы, связанные с проверкой подлинности.</span><span class="sxs-lookup"><span data-stu-id="22013-109">This file will hold all of your authentication-related methods.</span></span> <span data-ttu-id="22013-110">`get_sign_in_url` Создается URL-адрес авторизации, а `get_token_from_code` метод обменивается с ответом авторизации для маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="22013-110">The `get_sign_in_url` generates an authorization URL, and the `get_token_from_code` method exchanges the authorization response for an access token.</span></span>

<span data-ttu-id="22013-111">Добавьте приведенные `import` ниже операторы в верхнюю часть `./tutorial/views.py`.</span><span class="sxs-lookup"><span data-stu-id="22013-111">Add the following `import` statements to the top of `./tutorial/views.py`.</span></span>

```python
from django.urls import reverse
from tutorial.auth_helper import get_sign_in_url, get_token_from_code
```

<span data-ttu-id="22013-112">Теперь добавьте несколько новых представлений в `./tutorial/views.py` файл.</span><span class="sxs-lookup"><span data-stu-id="22013-112">Now add a couple of new views in the `./tutorial/views.py` file.</span></span>

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

<span data-ttu-id="22013-113">Определяет два новых представления: `signin` и. `callback`</span><span class="sxs-lookup"><span data-stu-id="22013-113">This defines two new views: `signin` and `callback`.</span></span>

<span data-ttu-id="22013-114">`signin` Действие создает URL-адрес входа Azure AD, сохраняет `state` значение, созданное клиентом OAuth, а затем перенаправляет браузер на страницу входа Azure AD.</span><span class="sxs-lookup"><span data-stu-id="22013-114">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

<span data-ttu-id="22013-115">`callback` Действие перенаправляет Azure после завершения входа.</span><span class="sxs-lookup"><span data-stu-id="22013-115">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="22013-116">Это действие гарантирует, что `state` значение соответствует сохраненному значению, а затем использует код проверки подлинности, отправленный Azure для запроса маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="22013-116">That action makes sure the `state` value matches the saved value, then uses the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="22013-117">Затем он перенаправляется обратно на домашнюю страницу с маркером доступа во временном значении ошибки.</span><span class="sxs-lookup"><span data-stu-id="22013-117">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="22013-118">Вы будете использовать этот параметр, чтобы проверить, работает ли наш вход перед переходом.</span><span class="sxs-lookup"><span data-stu-id="22013-118">You'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="22013-119">Прежде чем выполнять тестирование, необходимо добавить представления в `./tutorial/urls.py`.</span><span class="sxs-lookup"><span data-stu-id="22013-119">Before you test, you need to add the views to `./tutorial/urls.py`.</span></span>

```python
path('signin', views.sign_in, name='signin'),
path('callback', views.callback, name='callback'),
```

<span data-ttu-id="22013-120">Замените `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` строку на `./tutorial/templates/tutorial/home.html` приведенную ниже строку.</span><span class="sxs-lookup"><span data-stu-id="22013-120">Replace the `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` line in `./tutorial/templates/tutorial/home.html` with the following.</span></span>

```html
<a href="{% url 'signin' %}" class="btn btn-primary btn-large">Click here to sign in</a>
```

<span data-ttu-id="22013-121">Замените `<a href="#" class="nav-link">Sign In</a>` строку на `./tutorial/templates/tutorial/layout.html` приведенную ниже строку.</span><span class="sxs-lookup"><span data-stu-id="22013-121">Replace the `<a href="#" class="nav-link">Sign In</a>` line in `./tutorial/templates/tutorial/layout.html` with the following.</span></span>

```html
<a href="{% url 'signin' %}" class="nav-link">Sign In</a>
```

<span data-ttu-id="22013-122">Запустите сервер и перейдите к `https://localhost:8000/tutorial`.</span><span class="sxs-lookup"><span data-stu-id="22013-122">Start the server and browse to `https://localhost:8000/tutorial`.</span></span> <span data-ttu-id="22013-123">Нажмите кнопку входа, и вы будете перенаправлены на `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="22013-123">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="22013-124">Войдите с помощью учетной записи Майкрософт и согласия с запрошенными разрешениями.</span><span class="sxs-lookup"><span data-stu-id="22013-124">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="22013-125">Браузер перенаправляется на приложение, отображая маркер.</span><span class="sxs-lookup"><span data-stu-id="22013-125">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="22013-126">Получение сведений о пользователе</span><span class="sxs-lookup"><span data-stu-id="22013-126">Get user details</span></span>

<span data-ttu-id="22013-127">Создайте новый файл в `./tutorial` каталоге `graph_helper.py` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="22013-127">Create a new file in the `./tutorial` directory named `graph_helper.py` and add the following code.</span></span>

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

<span data-ttu-id="22013-128">`get_user` Метод создает запрос GET к конечной точке Microsoft Graph `/me` для получения профиля пользователя с помощью маркера доступа, полученного ранее.</span><span class="sxs-lookup"><span data-stu-id="22013-128">The `get_user` method makes a GET request to the Microsoft Graph `/me` endpoint to get the user's profile, using the access token you acquired previously.</span></span>

<span data-ttu-id="22013-129">Обновите `callback` метод в `./tutorial/views.py` , чтобы получить профиль пользователя из Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="22013-129">Update the `callback` method in `./tutorial/views.py` to get the user's profile from Microsoft Graph.</span></span>

<span data-ttu-id="22013-130">Сначала добавьте следующий `import` оператор в начало файла.</span><span class="sxs-lookup"><span data-stu-id="22013-130">First, add the following `import` statement to the top of the file.</span></span>

```python
from tutorial.graph_helper import get_user
```

<span data-ttu-id="22013-131">Замените `callback` метод на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="22013-131">Replace the `callback` method with the following code.</span></span>

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

<span data-ttu-id="22013-132">Новый код вызывает `get_user` метод, чтобы запросить профиль пользователя.</span><span class="sxs-lookup"><span data-stu-id="22013-132">The new code calls the `get_user` method to request the user's profile.</span></span> <span data-ttu-id="22013-133">Он добавляет объект пользователя во временный выход для тестирования.</span><span class="sxs-lookup"><span data-stu-id="22013-133">It adds the user object to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="22013-134">Сохранение маркеров</span><span class="sxs-lookup"><span data-stu-id="22013-134">Storing the tokens</span></span>

<span data-ttu-id="22013-135">Теперь, когда вы можете получить маркеры, следует реализовать способ их хранения в приложении.</span><span class="sxs-lookup"><span data-stu-id="22013-135">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="22013-136">Так как это пример приложения, для простоты вы будете хранить их в сеансе.</span><span class="sxs-lookup"><span data-stu-id="22013-136">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="22013-137">Реальное приложение использует более надежное решение для безопасного хранения, например базу данных.</span><span class="sxs-lookup"><span data-stu-id="22013-137">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="22013-138">Добавьте следующие новые методы в `./tutorial/auth_helper.py`.</span><span class="sxs-lookup"><span data-stu-id="22013-138">Add the following new methods to `./tutorial/auth_helper.py`.</span></span>

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

<span data-ttu-id="22013-139">Затем обновите `callback` функцию в `./tutorial/views.py` , чтобы сохранить маркеры в сеансе и переадресовать обратно на главную страницу.</span><span class="sxs-lookup"><span data-stu-id="22013-139">Then, update the `callback` function in `./tutorial/views.py` to store the tokens in the session and redirect back to the main page.</span></span> <span data-ttu-id="22013-140">Замените `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` строку на приведенную ниже строку.</span><span class="sxs-lookup"><span data-stu-id="22013-140">Replace the `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` line with the following.</span></span>

```python
from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
```

<span data-ttu-id="22013-141">Замените `callback` метод на приведенный ниже.</span><span class="sxs-lookup"><span data-stu-id="22013-141">Replace the `callback` method with the following.</span></span>

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

## <a name="implement-sign-out"></a><span data-ttu-id="22013-142">Реализация выхода</span><span class="sxs-lookup"><span data-stu-id="22013-142">Implement sign-out</span></span>

<span data-ttu-id="22013-143">Перед тестированием новой функции добавьте способ выхода. Добавьте новое `sign_out` представление в `./tutorial/views.py`.</span><span class="sxs-lookup"><span data-stu-id="22013-143">Before you test this new feature, add a way to sign out. Add a new `sign_out` view in `./tutorial/views.py`.</span></span>

```python
def sign_out(request):
  # Clear out the user and token
  remove_user_and_token(request)

  return HttpResponseRedirect(reverse('home'))
```

<span data-ttu-id="22013-144">Теперь добавьте это представление в `./tutorial/urls.py`.</span><span class="sxs-lookup"><span data-stu-id="22013-144">Now add this view to `./tutorial/urls.py`.</span></span>

```python
path('signout', views.sign_out, name='signout'),
```

<span data-ttu-id="22013-145">Обновите ссылку **выхода** в `./tutorial/templates/tutorial/layout.html` , чтобы использовать это новое представление.</span><span class="sxs-lookup"><span data-stu-id="22013-145">Update the **Sign Out** link in `./tutorial/templates/tutorial/layout.html` to use this new view.</span></span> <span data-ttu-id="22013-146">Замените `<a href="#" class="dropdown-item">Sign Out</a>` строку на приведенную ниже строку.</span><span class="sxs-lookup"><span data-stu-id="22013-146">Replace the `<a href="#" class="dropdown-item">Sign Out</a>` line with the following.</span></span>

```html
<a href="{% url 'signout' %}" class="dropdown-item">Sign Out</a>
```

<span data-ttu-id="22013-147">Перезапустите сервер и пройдите процесс входа.</span><span class="sxs-lookup"><span data-stu-id="22013-147">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="22013-148">Необходимо вернуться на домашнюю страницу, но пользовательский интерфейс должен измениться, чтобы показать, что вы вошли в систему.</span><span class="sxs-lookup"><span data-stu-id="22013-148">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

<span data-ttu-id="22013-150">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** .</span><span class="sxs-lookup"><span data-stu-id="22013-150">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="22013-151">При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.</span><span class="sxs-lookup"><span data-stu-id="22013-151">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="22013-153">Обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="22013-153">Refreshing tokens</span></span>

<span data-ttu-id="22013-154">На этом шаге приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="22013-154">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="22013-155">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="22013-155">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="22013-156">Однако этот маркер кратковременно используется.</span><span class="sxs-lookup"><span data-stu-id="22013-156">However, this token is short-lived.</span></span> <span data-ttu-id="22013-157">Срок действия маркера истечет через час после его выдачи.</span><span class="sxs-lookup"><span data-stu-id="22013-157">The token expires an hour after it is issued.</span></span> <span data-ttu-id="22013-158">В этом случае маркер обновления становится полезен.</span><span class="sxs-lookup"><span data-stu-id="22013-158">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="22013-159">Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа.</span><span class="sxs-lookup"><span data-stu-id="22013-159">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="22013-160">Обновите код управления маркером, чтобы реализовать обновление маркеров.</span><span class="sxs-lookup"><span data-stu-id="22013-160">Update the token management code to implement token refresh.</span></span>

<span data-ttu-id="22013-161">Замените существующий `get_token` метод на `./tutorial/auth_helper.py` приведенный ниже.</span><span class="sxs-lookup"><span data-stu-id="22013-161">Replace the existing `get_token` method in `./tutorial/auth_helper.py` with the following.</span></span>

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

<span data-ttu-id="22013-162">Этот метод сначала проверяет, истек ли срок действия маркера доступа, или закройте его до истечения срока действия.</span><span class="sxs-lookup"><span data-stu-id="22013-162">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="22013-163">Если это так, то он использует маркер обновления для получения новых токенов, затем обновляет кэш и возвращает новый маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="22013-163">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
