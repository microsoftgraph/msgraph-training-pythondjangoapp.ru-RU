<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы добавите Microsoft Graph в приложение. Для этого приложения вы будете использовать библиотеку [запросов — OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) , чтобы совершать вызовы в Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Получение событий календаря из Outlook

Для начала добавьте метод `./tutorial/graph_helper.py` для извлечения событий календаря. Добавьте указанный ниже метод.

```python
def get_calendar_events(token):
  graph_client = OAuth2Session(token=token)

  # Configure query parameters to
  # modify the results
  query_params = {
    '$select': 'subject,organizer,start,end',
    '$orderby': 'createdDateTime DESC'
  }

  # Send GET to /me/events
  events = graph_client.get('{0}/me/events'.format(graph_url), params=query_params)
  # Return the JSON result
  return events.json()
```

РасСмотрите, что делает этот код.

- URL-адрес, который будет вызываться — это `/v1.0/me/events`.
- `$select` Параметр позволяет ограничить поля, возвращаемые для каждого события, только теми, которые будут реально использоваться представлением.
- `$orderby` Параметр сортирует результаты по дате и времени создания, начиная с самого последнего элемента.

Теперь создайте представление календаря. Сначала измените `from tutorial.graph_helper import get_user` строку на следующий.

```python
from tutorial.graph_helper import get_user, get_calendar_events
```

Затем добавьте следующее представление в `./tutorial/views.py`.

```python
def calendar(request):
  context = initialize_context(request)

  token = get_token(request)

  events = get_calendar_events(token)

  context['errors'] = [
    { 'message': 'Events', 'debug': format(events)}
  ]

  return render(request, 'tutorial/home.html', context)
```

Обновление `./tutorial/urls.py` для добавления нового представления.

```python
path('calendar', views.calendar, name='calendar'),
```

Наконец, обновите **** ссылку на календарь `./tutorial/templates/tutorial/layout.html` , чтобы создать ссылку на это представление. Замените `<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>` строку на приведенную ниже строку.

```html
<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="{% url 'calendar' %}">Calendar</a>
```

Теперь вы можете протестировать это. Войдите и щелкните ссылку **Календарь** на панели навигации. Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.

## <a name="display-the-results"></a>Отображение результатов

Теперь можно добавить шаблон для отображения результатов более удобным для пользователя способом. Создайте новый файл в `./tutorial/templates/tutorial` каталоге `calendar.html` и добавьте указанный ниже код.

```html
{% extends "tutorial/layout.html" %}
{% block content %}
<h1>Calendar</h1>
<table class="table">
  <thead>
    <tr>
      <th scope="col">Organizer</th>
      <th scope="col">Subject</th>
      <th scope="col">Start</th>
      <th scope="col">End</th>
    </tr>
  </thead>
  <tbody>
    {% if events %}
      {% for event in events %}
        <tr>
          <td>{{ event.organizer.emailAddress.name }}</td>
          <td>{{ event.subject }}</td>
          <td>{{ event.start.dateTime|date:'SHORT_DATETIME_FORMAT' }}</td>
          <td>{{ event.end.dateTime|date:'SHORT_DATETIME_FORMAT' }}</td>
        </tr>
      {% endfor %}
    {% endif %}
  </tbody>
</table>
{% endblock %}
```

Это приведет к перебору коллекции событий и добавлению строки таблицы для каждой из них. Добавьте следующий `import` оператор в начало `./tutorials/views.py` файла.

```python
import dateutil.parser
```

Замените `calendar` представление на `./tutorial/views.py` приведенный ниже код.

```python
def calendar(request):
  context = initialize_context(request)

  token = get_token(request)

  events = get_calendar_events(token)

  if events:
    # Convert the ISO 8601 date times to a datetime object
    # This allows the Django template to format the value nicely
    for event in events['value']:
      event['start']['dateTime'] = dateutil.parser.parse(event['start']['dateTime'])
      event['end']['dateTime'] = dateutil.parser.parse(event['end']['dateTime'])

    context['events'] = events['value']

  return render(request, 'tutorial/calendar.html', context)
```

Обновите страницу, после чего приложение должно отобразить таблицу событий.

![Снимок экрана С таблицей событий](./images/add-msgraph-01.png)