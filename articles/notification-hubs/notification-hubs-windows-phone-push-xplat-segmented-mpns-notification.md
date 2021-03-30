---
title: Posílání nabízených oznámení do konkrétních telefonů Windows pomocí Azure Notification Hubs | Microsoft Docs
description: V tomto kurzu se naučíte používat službu Azure Notification Hubs k zasílání nabízených oznámení určitým (ne všem) zařízením Windows Phone 8 nebo Windows Phone 8.1 registrovaným v back-endové aplikaci.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 67700ec643a27f8ae5c581fe9de8e3295a1925e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998233"
---
# <a name="tutorial-send-push-notifications-to-specific-windows-phones-using-azure-notification-hubs"></a>Kurz: odeslání nabízených oznámení do konkrétních telefonů Windows pomocí Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

V tomto kurzu si ukážeme, jak používat službu Azure Notification Hubs k odesílání nabízených oznámení určitým zařízením Windows Phone 8 nebo Windows Phone 8.1. Pokud jsou vaše aplikace určeny pro Windows Phone 8.1 (ne Silverlight), přečtěte si v tomto kurzu informace k verzi [Univerzální platforma Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

V tomto scénáři při registraci zařízení v centru oznámení přidáte zařízení jednu nebo více *značek*. Při posílání oznámení značce toto oznámení přijde všem zařízením, která si danou značku zaregistrovala. Další informace o značkách naleznete [v tématu značky v registracích](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Sada SDK centra oznámení Windows Phone nepodporuje použití služby nabízených oznámení Windows (WNS) s aplikacemi pro Windows Phone 8.1 Silverlight. Pokud chcete s aplikacemi pro Windows Phone 8.1 Silverlight používat WNS místo MPNS, postupujte podle kurzu Notification Hubs – Windows Phone Silverlight, který používá rozhraní REST API.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání výběru kategorií do mobilní aplikace
> * Používat značky k registraci oznámení
> * Posílání označených oznámení
> * Otestování aplikace

## <a name="prerequisites"></a>Požadavky

Dokončete [kurz Zasílání nabízených oznámení aplikacím pro Windows Phone službou Azure Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md). V tomto kurzu aktualizujete mobilní aplikaci, abyste si mohli zaregistrovat kategorie nejnovějších zpráv, které vás zajímají, a dostávat nabízená oznámení jenom k těmto kategoriím.

## <a name="add-category-selection-to-the-mobile-app"></a>Přidání výběru kategorií do mobilní aplikace

V prvním kroku přidejte na stávající stránku prvky uživatelského rozhraní, které umožní uživateli vybrat registrované kategorie. Kategorie, které uživatel vybere, jsou uložené v zařízení. Při spuštění aplikace se v centru oznámení provede registrace zařízení s vybranými kategoriemi ve formě značek.

1. Otevřete `MainPage.xaml` soubor a potom nahraďte `Grid` prvky s názvem `TitlePanel` a `ContentPanel` následujícím kódem:

    ```xml
    <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
        <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
        <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
    </StackPanel>

    <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
        <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
        <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
        <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
        <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
        <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. Přidejte třídu s názvem `Notifications` do projektu. Přidejte `public` modifikátor do definice třídy. Potom `using` do nového souboru přidejte následující příkazy:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    using System.IO.IsolatedStorage;
    using System.Windows;
    ```
3. Zkopírujte následující kód do nové `Notifications` třídy:

    ```csharp
    private NotificationHub hub;

    // Registration task to complete registration in the ChannelUriUpdated event handler
    private TaskCompletionSource<Registration> registrationTask;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
        return categories != null ? categories.Split(',') : new string[0];
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        var categoriesAsString = string.Join(",", categories);
        var settings = IsolatedStorageSettings.ApplicationSettings;
        if (!settings.Contains("categories"))
        {
            settings.Add("categories", categoriesAsString);
        }
        else
        {
            settings["categories"] = categoriesAsString;
        }
        settings.Save();

        return await SubscribeToCategories();
    }

    public async Task<Registration> SubscribeToCategories()
    {
        registrationTask = new TaskCompletionSource<Registration>();

        var channel = HttpNotificationChannel.Find("MyPushChannel");

        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
            channel.ChannelUriUpdated += channel_ChannelUriUpdated;

            // This is optional, used to receive notifications while the app is running.
            channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
        }

        // If channel.ChannelUri is not null, complete the registrationTask here.  
        // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
        if (channel.ChannelUri != null)
        {
            await RegisterTemplate(channel.ChannelUri);
        }

        return await registrationTask.Task;
    }

    async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
    {
        await RegisterTemplate(e.ChannelUri);
    }

    async Task<Registration> RegisterTemplate(Uri channelUri)
    {
        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                            "<wp:Toast>" +
                                                "<wp:Text1>$(messageParam)</wp:Text1>" +
                                            "</wp:Toast>" +
                                        "</wp:Notification>";

        // The stored categories tags are passed with the template registration.

        registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(),
            templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

        return await registrationTask.Task;
    }

    // This is optional. It is used to receive notifications while the app is running.
    void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
    {
        StringBuilder message = new StringBuilder();
        string relativeUri = string.Empty;

        message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

        // Parse out the information that was part of the message.
        foreach (string key in e.Collection.Keys)
        {
            message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

            if (string.Compare(
                key,
                "wp:Param",
                System.Globalization.CultureInfo.InvariantCulture,
                System.Globalization.CompareOptions.IgnoreCase) == 0)
            {
                relativeUri = e.Collection[key];
            }
        }

        // Display a dialog of all the fields in the toast.
        System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
        {
            MessageBox.Show(message.ToString());
        });
    }
    ```

    Třída ukládá kategorie novinek, které bude zařízení dostávat, do izolovaného úložiště. Třída také obsahuje metody registrace těchto kategorií pomocí [šablony](notification-hubs-templates-cross-platform-push-messages.md) registrace oznámení.
4. V `App.xaml.cs` souboru projektu přidejte do třídy následující vlastnost `App` . Nahraďte zástupné symboly `<hub name>` a `<connection string with listen access>` názvem centra oznámení a připojovacím řetězcem *DefaultListenSharedAccessSignature*, který jste získali dříve.

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

   > [!NOTE]
   > Obecně platí, že přihlašovací údaje distribuované klientskou aplikací nejsou příliš bezpečné, a proto byste měli s klientskou aplikací distribuovat jenom přístupový klíč pro naslouchání. Přístup pro naslouchání umožňuje aplikaci registrovat oznámení, ale nedovolí měnit stávající registrace ani odesílat oznámení. Plný přístupový klíč se používá v zabezpečené back-endové službě k posílání oznámení a změně stávajících registrací.

5. Do `MainPage.xaml.cs` přidejte následující řádek:

    ```csharp
    using Windows.UI.Popups;
    ```
6. Do souboru projektu MainPage.xaml.cs přidejte následující metodu:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldCheckBox.IsChecked == true) categories.Add("World");
        if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
        if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
        if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
        if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
        if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
            result.RegistrationId);
    }
    ```

    Tato metoda vytvoří seznam kategorií a pomocí `Notifications` třídy uloží seznam do místního úložiště a zaregistruje odpovídající značky do vašeho centra oznámení. Při změně kategorií se vytvoří registrace s novými kategoriemi.

Aplikace teď dokáže do místního úložiště v zařízení uložit sadu kategorií a zaregistrovat ji v centru oznámení pokaždé, když uživatel změní vybrané kategorie.

## <a name="register-for-notifications"></a>Registrace oznámení

Tento postup provede při spuštění registraci v centru oznámení. Použije k tomu kategorie uložené v místním úložišti.

> [!NOTE]
> Identifikátor URI kanálu přiřazený službě MPNS (Microsoft Push Notification Service) se může kdykoliv změnit, a proto byste měli oznámení často registrovat, abyste se vyhnuli chybám v oznámeních. Tento příklad registruje oznámení při každém spuštění aplikace. Pokud se aplikace spouštějí často, třeba častěji než jednou denně, pravděpodobně můžete registraci přeskočit kvůli úspoře šířky pásma, protože od předchozí registrace neuplynul ani den.

1. Otevřete soubor App. XAML. cs a přidejte `async` k `Application_Launching` metodě modifikátor a nahraďte Notification Hubs registračním kódem, který jste přidali v části [Začínáme s Notification Hubs] s následujícím kódem:

    ```csharp
    private async void Application_Launching(object sender, LaunchingEventArgs e)
    {
        var result = await notifications.SubscribeToCategories();

        if (result != null)
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
    }
    ```

    Tento kód zajistí, aby aplikace při každém spuštění načetla kategorie z místního úložiště a vyžadovala registraci těchto kategorií.
2. V souboru projektu MainPage. XAML. cs přidejte následující kód, který implementuje `OnNavigatedTo` metodu:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
        if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
        if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
        if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
        if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
        if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
    }
    ```

    Tento kód aktualizuje hlavní stránku podle stavu dříve uložených kategorií.

Hotová aplikace teď do místního úložiště v zařízení uloží sadu kategorií. Tato sada se použije k registraci v centru oznámení pokaždé, když uživatel změní vybrané kategorie. V dalším kroku definujte back-end, který aplikaci posílá oznámení týkající se kategorií.

## <a name="send-tagged-notifications"></a>Posílání označených oznámení

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Otestování aplikace

1. Pokud chcete v sadě Visual Studio kompilovat aplikaci a spustit ji, stiskněte F5.

    ![Mobilní aplikace s kategoriemi][1]

    Uživatelské rozhraní aplikace nabízí sadu přepínačů, kterými můžete vybrat odebírané kategorie.
2. Zapněte jeden nebo více přepínačů kategorií a klikněte na **Přihlásit k odběru**.

    Aplikace převede vybrané kategorie na značky a u vybraných značek požádá centrum oznámení o registraci nových zařízení. Zaregistrované kategorie se vrátí a zobrazí v dialogovém okně.

    ![Odebíraná zpráva][2]
3. Jakmile obdržíte potvrzení o dokončeném odběru kategorií, spusťte konzolovou aplikaci, která odešle oznámení týkající se každé kategorie. Ověřte, že dostáváte jenom oznámení týkající se odebíraných kategorií.

    ![Oznámení][3]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili posílat nabízená oznámení určitým zařízením, která mají zaregistrované značky. Pokud se chcete naučit posílat nabízená oznámení určitým uživatelům, kteří mohou používat více zařízení, pokračujte následujícím kurzem: 

> [!div class="nextstepaction"]
>[Nabízená oznámení odesílaná konkrétním uživatelům](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png

<!-- URLs.-->
[Začínáme se službou Notification Hubs]: notification-hubs-windows-mobile-push-notifications-mpns.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Phone]: ??
