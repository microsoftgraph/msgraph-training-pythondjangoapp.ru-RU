<!-- markdownlint-disable MD002 MD041 -->

В этом разделе вы добавим возможность создания событий в календаре пользователя.

1. Добавьте следующий метод в **./tutorial/graph_helper.py,** чтобы создать новое событие.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="CreateEventSnippet":::

## <a name="create-a-new-event-form"></a>Создание формы события

1. Создайте файл с именем **./tutorial/templates/tutorial** и `newevent.html` добавьте следующий код.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/newevent.html" id="NewEventSnippet":::

1. Добавьте следующее представление в **./tutorial/views.py.**

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="NewEventViewSnippet":::

1. Откройте **./tutorial/urls.py** и добавьте `path` инструкции для `newevent` представления.

    ```python
    path('calendar/new', views.newevent, name='newevent'),
    ```

1. Сохраните изменения и обновите приложение. На странице **"Календарь"** выберите кнопку **"Новое** событие". Заполните форму и выберите **"Создать",** чтобы создать событие.

    ![Снимок экрана с новой формой события](images/create-event-01.png)
