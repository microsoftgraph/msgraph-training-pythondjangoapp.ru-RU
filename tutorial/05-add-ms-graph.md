<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы включаете Microsoft Graph в приложение.

## <a name="get-calendar-events-from-outlook"></a>Получить события календаря из Outlook

1. Для начала добавьте метод **в ./tutorial/graph_helper.py,** чтобы получить представление календаря между двумя датами. Добавьте следующий метод.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    Подумайте, что делает этот код.

    - Будет вызван URL-адрес `/v1.0/me/calendarview` .
        - Заголок приводит к корректировке времени начала и окончания результатов в часовом `Prefer: outlook.timezone` поясе пользователя.
        - Параметры `startDateTime` и начало и конец `endDateTime` представления.
        - Параметр `$select` ограничивает поля, возвращаемые для каждого события, только теми полями, которые будут фактически использовать представление.
        - Параметр `$orderby` сортировать результаты по времени начала.
        - Этот `$top` параметр ограничивает результаты до 50 событий.

1. Добавьте следующий код в **./tutorial/graph_helper.py,** чтобы найти идентификатор часовой пояс [IANA](https://www.iana.org/time-zones) на основе имени часовой пояс Windows. Это необходимо, так как Microsoft Graph может возвращать часовые пояса в качестве имен часовых поясов Windows, а библиотеке **даты** и времени Python требуются идентификаторы часовых поясов IANA.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="ZoneMappingsSnippet":::

1. Добавьте следующее представление в **./tutorial/views.py.**

    ```python
    def calendar(request):
      context = initialize_context(request)
      user = context['user']

      # Load the user's time zone
      # Microsoft Graph can return the user's time zone as either
      # a Windows time zone name or an IANA time zone identifier
      # Python datetime requires IANA, so convert Windows to IANA
      time_zone = get_iana_from_windows(user['timeZone'])
      tz_info = tz.gettz(time_zone)

      # Get midnight today in user's time zone
      today = datetime.now(tz_info).replace(
        hour=0,
        minute=0,
        second=0,
        microsecond=0)

      # Based on today, get the start of the week (Sunday)
      if (today.weekday() != 6):
        start = today - timedelta(days=today.isoweekday())
      else:
        start = today

      end = start + timedelta(days=7)

      token = get_token(request)

      events = get_calendar_events(
        token,
        start.isoformat(timespec='seconds'),
        end.isoformat(timespec='seconds'),
        user['timeZone'])

      context['errors'] = [
        { 'message': 'Events', 'debug': format(events)}
      ]

      return render(request, 'tutorial/home.html', context)
    ```

1. Откройте **./tutorial/urls.py** и замените существующие инструкции `path` на `calendar` следующие.

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. Войдите и щелкните **ссылку "Календарь"** на панели nav. Если все работает, в календаре пользователя должен быть дамп событий JSON.

## <a name="display-the-results"></a>Отображение результатов

Теперь можно добавить шаблон для более удобного отображения результатов.

1. Создайте файл с именем **./tutorial/templates/tutorial** и `calendar.html` добавьте следующий код.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    При этом будет цикл через коллекцию событий и добавлена строка таблицы для каждого из них.

1. Замените `calendar` представление в **./tutorial/views.py** следующим кодом.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. Обновите страницу, и приложение должно отрисовки таблицы событий.

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
