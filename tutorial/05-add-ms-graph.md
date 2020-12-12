<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a5692-101">В этом упражнении вы включаете Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="a5692-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="a5692-102">Получить события календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="a5692-102">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="a5692-103">Для начала добавьте метод **в ./tutorial/graph_helper.py,** чтобы получить представление календаря между двумя датами.</span><span class="sxs-lookup"><span data-stu-id="a5692-103">Start by adding a method to **./tutorial/graph_helper.py** to fetch a view of the calendar between two dates.</span></span> <span data-ttu-id="a5692-104">Добавьте следующий метод.</span><span class="sxs-lookup"><span data-stu-id="a5692-104">Add the following method.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    <span data-ttu-id="a5692-105">Подумайте, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="a5692-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="a5692-106">Будет вызван URL-адрес `/v1.0/me/calendarview` .</span><span class="sxs-lookup"><span data-stu-id="a5692-106">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
        - <span data-ttu-id="a5692-107">Заголок приводит к корректировке времени начала и окончания результатов в часовом `Prefer: outlook.timezone` поясе пользователя.</span><span class="sxs-lookup"><span data-stu-id="a5692-107">The `Prefer: outlook.timezone` header causes the start and end times in the results to be adjusted to the user's time zone.</span></span>
        - <span data-ttu-id="a5692-108">Параметры `startDateTime` и начало и конец `endDateTime` представления.</span><span class="sxs-lookup"><span data-stu-id="a5692-108">The `startDateTime` and `endDateTime` parameters set the start and end of the view.</span></span>
        - <span data-ttu-id="a5692-109">Параметр `$select` ограничивает поля, возвращаемые для каждого события, только теми полями, которые будут фактически использовать представление.</span><span class="sxs-lookup"><span data-stu-id="a5692-109">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
        - <span data-ttu-id="a5692-110">Параметр `$orderby` сортировать результаты по времени начала.</span><span class="sxs-lookup"><span data-stu-id="a5692-110">The `$orderby` parameter sorts the results by start time.</span></span>
        - <span data-ttu-id="a5692-111">Этот `$top` параметр ограничивает результаты до 50 событий.</span><span class="sxs-lookup"><span data-stu-id="a5692-111">The `$top` parameter limits the results to 50 events.</span></span>

1. <span data-ttu-id="a5692-112">Добавьте следующий код в **./tutorial/graph_helper.py,** чтобы найти идентификатор часовой пояс [IANA](https://www.iana.org/time-zones) на основе имени часовой пояс Windows.</span><span class="sxs-lookup"><span data-stu-id="a5692-112">Add the following code to **./tutorial/graph_helper.py** to lookup an [IANA time zone identifier](https://www.iana.org/time-zones) based on a Windows time zone name.</span></span> <span data-ttu-id="a5692-113">Это необходимо, так как Microsoft Graph может возвращать часовые пояса в качестве имен часовых поясов Windows, а библиотеке **даты** и времени Python требуются идентификаторы часовых поясов IANA.</span><span class="sxs-lookup"><span data-stu-id="a5692-113">This is necessary because Microsoft Graph can return time zones as Windows time zone names, and the Python **datetime** library requires IANA time zone identifiers.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="ZoneMappingsSnippet":::

1. <span data-ttu-id="a5692-114">Добавьте следующее представление в **./tutorial/views.py.**</span><span class="sxs-lookup"><span data-stu-id="a5692-114">Add the following view to **./tutorial/views.py**.</span></span>

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

1. <span data-ttu-id="a5692-115">Откройте **./tutorial/urls.py** и замените существующие инструкции `path` на `calendar` следующие.</span><span class="sxs-lookup"><span data-stu-id="a5692-115">Open **./tutorial/urls.py** and replace the existing `path` statements for `calendar` with the following.</span></span>

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. <span data-ttu-id="a5692-116">Войдите и щелкните **ссылку "Календарь"** на панели nav.</span><span class="sxs-lookup"><span data-stu-id="a5692-116">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="a5692-117">Если все работает, в календаре пользователя должен быть дамп событий JSON.</span><span class="sxs-lookup"><span data-stu-id="a5692-117">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="a5692-118">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="a5692-118">Display the results</span></span>

<span data-ttu-id="a5692-119">Теперь можно добавить шаблон для более удобного отображения результатов.</span><span class="sxs-lookup"><span data-stu-id="a5692-119">Now you can add a template to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="a5692-120">Создайте файл с именем **./tutorial/templates/tutorial** и `calendar.html` добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="a5692-120">Create a new file in the **./tutorial/templates/tutorial** directory named `calendar.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    <span data-ttu-id="a5692-121">При этом будет цикл через коллекцию событий и добавлена строка таблицы для каждого из них.</span><span class="sxs-lookup"><span data-stu-id="a5692-121">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="a5692-122">Замените `calendar` представление в **./tutorial/views.py** следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="a5692-122">Replace the `calendar` view in **./tutorial/views.py** with the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. <span data-ttu-id="a5692-123">Обновите страницу, и приложение должно отрисовки таблицы событий.</span><span class="sxs-lookup"><span data-stu-id="a5692-123">Refresh the page and the app should now render a table of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
