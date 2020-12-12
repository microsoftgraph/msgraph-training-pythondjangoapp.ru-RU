<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="9cbe4-101">В этом разделе вы добавим возможность создания событий в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="9cbe4-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="9cbe4-102">Добавьте следующий метод в **./tutorial/graph_helper.py,** чтобы создать новое событие.</span><span class="sxs-lookup"><span data-stu-id="9cbe4-102">Add the following method to **./tutorial/graph_helper.py** to create a new event.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="CreateEventSnippet":::

## <a name="create-a-new-event-form"></a><span data-ttu-id="9cbe4-103">Создание формы события</span><span class="sxs-lookup"><span data-stu-id="9cbe4-103">Create a new event form</span></span>

1. <span data-ttu-id="9cbe4-104">Создайте файл с именем **./tutorial/templates/tutorial** и `newevent.html` добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="9cbe4-104">Create a new file in the **./tutorial/templates/tutorial** directory named `newevent.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/newevent.html" id="NewEventSnippet":::

1. <span data-ttu-id="9cbe4-105">Добавьте следующее представление в **./tutorial/views.py.**</span><span class="sxs-lookup"><span data-stu-id="9cbe4-105">Add the following view to **./tutorial/views.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="NewEventViewSnippet":::

1. <span data-ttu-id="9cbe4-106">Откройте **./tutorial/urls.py** и добавьте `path` инструкции для `newevent` представления.</span><span class="sxs-lookup"><span data-stu-id="9cbe4-106">Open **./tutorial/urls.py** and add a `path` statements for the `newevent` view.</span></span>

    ```python
    path('calendar/new', views.newevent, name='newevent'),
    ```

1. <span data-ttu-id="9cbe4-107">Сохраните изменения и обновите приложение.</span><span class="sxs-lookup"><span data-stu-id="9cbe4-107">Save your changes and refresh the app.</span></span> <span data-ttu-id="9cbe4-108">На странице **"Календарь"** выберите кнопку **"Новое** событие".</span><span class="sxs-lookup"><span data-stu-id="9cbe4-108">On the **Calendar** page, select the **New event** button.</span></span> <span data-ttu-id="9cbe4-109">Заполните форму и выберите **"Создать",** чтобы создать событие.</span><span class="sxs-lookup"><span data-stu-id="9cbe4-109">Fill in the form and select **Create** to create the event.</span></span>

    ![Снимок экрана с новой формой события](images/create-event-01.png)
