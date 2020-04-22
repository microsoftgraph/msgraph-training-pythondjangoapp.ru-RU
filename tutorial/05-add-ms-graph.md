<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы добавите Microsoft Graph в приложение. Для этого приложения вы будете использовать библиотеку [запросов — OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) , чтобы совершать вызовы в Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Получение событий календаря из Outlook

1. Для начала добавьте метод в файле **./туториал/graph_helper. корректировки** , чтобы получить события календаря. Добавьте указанный ниже метод.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    Рассмотрите, что делает этот код.

    - URL-адрес, который будет вызываться — это `/v1.0/me/events`.
    - `$select` Параметр позволяет ограничить поля, возвращаемые для каждого события, только теми, которые будут реально использоваться представлением.
    - `$orderby` Параметр сортирует результаты по дате и времени создания, начиная с самого последнего элемента.

1. В файле **./туториал/виевс.Пи**измените `from tutorial.graph_helper import get_user` строку на приведенную ниже строку.

    ```python
    from tutorial.graph_helper import get_user, get_calendar_events
    ```

1. Добавьте следующее представление в **./туториал/виевс.Пи**.

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

1. Откройте **./туториал/урлс.Пи** и замените существующие `path` операторы `calendar` на приведенные ниже.

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. Войдите и щелкните ссылку **Календарь** на панели навигации. Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.

## <a name="display-the-results"></a>Отображение результатов

Теперь можно добавить шаблон для отображения результатов более удобным для пользователя способом.

1. Создайте новый файл в каталоге **./туториал/темплатес/туториал** `calendar.html` и добавьте указанный ниже код.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    Это приведет к перебору коллекции событий и добавлению строки таблицы для каждой из них.

1. Добавьте следующий `import` оператор в начало файла **./туториалс/виевс.Пи** .

    ```python
    import dateutil.parser
    ```

1. Замените `calendar` представление в файле **./туториал/виевс.Пи** на приведенный ниже код.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. Обновите страницу, после чего приложение должно отобразить таблицу событий.

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
