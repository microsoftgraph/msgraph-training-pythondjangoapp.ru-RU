<!-- markdownlint-disable MD002 MD041 -->

В этом руководстве рассказывается о создании веб-приложения Python Django, которое использует API Microsoft Graph для получения сведений о календаре для пользователя.

> [!TIP]
> Если вы предпочитаете просто скачать завершенный учебник, вы можете скачать его двумя способами.
>
> - Быстро [скачать Python, чтобы](https://developer.microsoft.com/graph/quick-start?platform=option-Python) получить рабочий код за несколько минут.
> - Скачайте или клонировать [репозиторий GitHub.](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp)

## <a name="prerequisites"></a>Предварительные условия

Перед началом этого руководства необходимо установить [Python](https://www.python.org/) [(с](https://pypi.org/project/pip/)пипсом) на компьютере разработки. Если у вас нет Python, посетите предыдущую ссылку для скачать параметры.

Вы также должны иметь личную учетную запись Майкрософт с почтовым ящиком в Outlook.com или учетную запись Microsoft work или school. Если у вас нет учетной записи Майкрософт, существует несколько вариантов получения бесплатной учетной записи:

- Вы можете [зарегистрироваться для новой личной учетной записи Майкрософт.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)
- Вы можете [зарегистрироваться в программе разработчиков Office 365,](https://developer.microsoft.com/office/dev-program) чтобы получить бесплатную подписку на Office 365.

> [!NOTE]
> Этот учебник был написан с помощью Python версии 3.9.2 и django версии 3.2. Действия в этом руководстве могут работать с другими версиями, но они не были проверены.

## <a name="feedback"></a>Отзывы

В репозитории [GitHub](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp)обратите внимание на этот учебник.
