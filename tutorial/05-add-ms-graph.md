<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5e9a0-101">В этом упражнении вы добавите Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="5e9a0-102">Для этого приложения вы будете использовать библиотеку [запросов — OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) , чтобы совершать вызовы в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-102">For this application, you will use the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="5e9a0-103">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="5e9a0-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="5e9a0-104">Для начала добавьте метод в файле **./туториал/graph_helper. корректировки** , чтобы получить события календаря.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-104">Start by adding a method to **./tutorial/graph_helper.py** to fetch the calendar events.</span></span> <span data-ttu-id="5e9a0-105">Добавьте указанный ниже метод.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-105">Add the following method.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    <span data-ttu-id="5e9a0-106">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-106">Consider what this code is doing.</span></span>

    - <span data-ttu-id="5e9a0-107">URL-адрес, который будет вызываться — это `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-107">The URL that will be called is `/v1.0/me/events`.</span></span>
    - <span data-ttu-id="5e9a0-108">`$select` Параметр позволяет ограничить поля, возвращаемые для каждого события, только теми, которые будут реально использоваться представлением.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-108">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="5e9a0-109">`$orderby` Параметр сортирует результаты по дате и времени создания, начиная с самого последнего элемента.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-109">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="5e9a0-110">В файле **./туториал/виевс.Пи**измените `from tutorial.graph_helper import get_user` строку на приведенную ниже строку.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-110">In **./tutorial/views.py**, change the `from tutorial.graph_helper import get_user` line to the following.</span></span>

    ```python
    from tutorial.graph_helper import get_user, get_calendar_events
    ```

1. <span data-ttu-id="5e9a0-111">Добавьте следующее представление в **./туториал/виевс.Пи**.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-111">Add the following view to **./tutorial/views.py**.</span></span>

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

1. <span data-ttu-id="5e9a0-112">Откройте **./туториал/урлс.Пи** и замените существующие `path` операторы `calendar` на приведенные ниже.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-112">Open **./tutorial/urls.py** and replace the existing `path` statements for `calendar` with the following.</span></span>

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. <span data-ttu-id="5e9a0-113">Войдите и щелкните ссылку **Календарь** на панели навигации.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-113">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="5e9a0-114">Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-114">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="5e9a0-115">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="5e9a0-115">Display the results</span></span>

<span data-ttu-id="5e9a0-116">Теперь можно добавить шаблон для отображения результатов более удобным для пользователя способом.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-116">Now you can add a template to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="5e9a0-117">Создайте новый файл в каталоге **./туториал/темплатес/туториал** `calendar.html` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-117">Create a new file in the **./tutorial/templates/tutorial** directory named `calendar.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    <span data-ttu-id="5e9a0-118">Это приведет к перебору коллекции событий и добавлению строки таблицы для каждой из них.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-118">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="5e9a0-119">Добавьте следующий `import` оператор в начало файла **./туториалс/виевс.Пи** .</span><span class="sxs-lookup"><span data-stu-id="5e9a0-119">Add the following `import` statement to the top of the **./tutorials/views.py** file.</span></span>

    ```python
    import dateutil.parser
    ```

1. <span data-ttu-id="5e9a0-120">Замените `calendar` представление в файле **./туториал/виевс.Пи** на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-120">Replace the `calendar` view in **./tutorial/views.py** with the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. <span data-ttu-id="5e9a0-121">Обновите страницу, после чего приложение должно отобразить таблицу событий.</span><span class="sxs-lookup"><span data-stu-id="5e9a0-121">Refresh the page and the app should now render a table of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
