---
title: Posílání lokalizovaných oznámení aplikacím pro Windows službou Azure Notification Hubs | Microsoft Docs
description: Přečtěte si, jak používat službu Azure Notification Hubs k posílání lokalizovaných oznámení s nejnovějšími zprávami.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: d91320d8f78942ca916084cbf29a88818d95f531
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92314621"
---
# <a name="tutorial-send-localized-push-notifications-to-windows-apps-using-azure-notification-hubs"></a>Kurz: posílání lokalizovaných nabízených oznámení do aplikací pro Windows pomocí Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C #](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>Přehled

V tomto kurzu se naučíte zasílat lokalizovaná nabízená oznámení mobilním zařízením registrovaným ve službě Notification Hubs. V tomto kurzu budete aktualizovat aplikace vytvořené v [kurzu o zasílání oznámení určitým zařízením (Univerzální platforma Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md), aby podporovaly následující scénáře:

- Aplikace pro Windows Store umožňuje klientským zařízením zadat jazyk a přihlásit se k odběru různých kategorií hlavních zpráv.
- Back-endové aplikace vysílá oznámení pomocí **značek** a funkcí **šablon** služby Azure Notification Hubs.

Po dokončení kurzu si budete moct v mobilní aplikaci zaregistrovat kategorie, které vás zajímají, a také budete moct zadat jazyk, ve kterém chcete zasílat oznámení. Back-endová aplikace posílá oznámení lokalizovaná pro daný jazyk a zařízení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Aktualizovat aplikace pro Windows, aby podporovaly informace o národním prostředí
> * Aktualizovat back-endovou aplikaci, aby posílala lokalizovaná oznámení
> * Otestování aplikace

## <a name="prerequisites"></a>Požadavky

Dokončete [kurz o zasílání oznámení určitým zařízením (Univerzální platforma Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

V [kurzu o zasílání oznámení určitým zařízením (Univerzální platforma Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) jste vytvořili aplikaci, která k přihlášení odběru oznámení pro různé **kategorie** novinek používala **značky**. V tomto kurzu použijete funkci **šablony** služby Notification Hubs, která umožňuje jednoduše doručovat **lokalizovaná** oznámení nejdůležitějších zpráv.

Šablony nabízejí způsob, jak na vyšší úrovni zadat formát, ve kterém má určité zařízení dostávat oznámení. Šablona přesně určuje formát datové části tím, že odkazuje na vlastnosti, které jsou součástí zprávy odeslané back-endovou aplikací. V tomto kurzu back-endová aplikace pošle zprávu, která je určená pro všechna národní prostředí a obsahuje všechny podporované jazyky:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Zařízení si zaregistrují šablonu, která odkazuje na správnou vlastnost. Například aplikace pro Windows Store, která má dostávat zprávy s informacemi v angličtině, si zaregistruje následující šablonu s odpovídajícími značkami:

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

Další informace o šablonách najdete v tématu [šablony nabízených oznámení](notification-hubs-templates-cross-platform-push-messages.md).

## <a name="update-windows-app-to-support-locale-information"></a>Aktualizovat aplikace pro Windows, aby podporovaly informace o národním prostředí

1. Otevřete řešení sady Visual Studio, které jste vytvořili v [kurzu o zasílání oznámení určitým zařízením (Univerzální platforma Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).
2. Aktualizujte soubor řešení `MainPage.xaml` tak, aby zahrnoval pole se seznamem národního prostředí:

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
3. Ve `Notifications` třídě přidejte parametr národního prostředí do  `StoreCategoriesAndSubscribe` `SubscribeToCategories` metod a.

    ```csharp
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(locale, categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    Namísto volání `RegisterNativeAsync` metody zavolejte `RegisterTemplateAsync` . Zaregistrujte určitý formát oznámení, ve kterém šablona závisí na národním prostředí. Zadejte také název šablony (localizedWNSTemplateExample), protože možná budete chtít zaregistrovat více šablon (například jednu pro informační oznámení a druhou pro dlaždice). Pojmenování je také důležité, abyste mohli šablony aktualizovat nebo odstranit.

    Pokud si zařízení zaregistruje více šablon se stejnou značkou, doručí se do zařízení příchozí zpráva v několika oznámeních (jedno pro každou šablonu) určených pro výslednou značku. Toto chování je užitečné, pokud se má stejná logická zpráva zobrazit v několika oznámeních, třeba jako oznámení a informační zpráva v aplikaci pro Windows Store.
4. Pokud chcete načíst uložené národní prostředí, přidejte následující metodu:

    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

5. V `MainPage.xaml.cs` souboru aktualizujte obslužnou rutinu kliknutí na tlačítko a načtěte aktuální hodnotu pole se seznamem národního prostředí a poskytněte ji volání `Notifications` třídy:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
6. Nakonec v `App.xaml.cs` souboru aktualizujte `InitNotificationsAsync` metodu pro načtení národního prostředí a použijte ji při přihlášení k odběru:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```



## <a name="run-the-uwp-application"></a>Spuštění aplikace UWP

1. Spusťte aplikaci Univerzální platforma Windows. Počkejte, až se zobrazí zpráva o **úspěšné registraci**.

    ![Mobilní aplikace a registrace](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
2. Vyberte **kategorie** a **národní prostředí** a klikněte na **Přihlásit k odběru**. Aplikace převede vybrané kategorie na značky a u vybraných značek požádá centrum oznámení o registraci nových zařízení.

    ![Mobilní aplikace](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
3. Zobrazí se **potvrzovací** zpráva o **odběrech**.

    ![Zpráva o odběru](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)

## <a name="update-console-app-to-send-localized-notifications"></a>Aktualizovat konzolovou aplikaci pro odesílání lokalizovaných oznámení

Při posílání šablonových oznámení stačí zadat sadu vlastností. V tomto kurzu odešle back-endová aplikace sadu vlastností, které obsahují lokalizovanou verzi aktuálních zpráv, jako například:

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

V této části v řešení aktualizujete projekt konzolové aplikace. Změňte metodu `SendTemplateNotificationAsync` konzolové aplikace vytvořené dříve přidáním následujícího kódu:

> [!IMPORTANT]
> Zadejte v kódu název a připojovací řetězec pro plný přístup k centru oznámení.

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(
        "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications.
    // This includes APNS, FCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Toto jednoduché volání přináší lokalizované zprávy na **všechna** zařízení, bez ohledu na platformu, jako vaše sestavení centra oznámení a poskytuje správnou nativní datovou část pro všechna zařízení, která se přihlásila ke konkrétní značce.

## <a name="run-console-app-to-send-localized-notification"></a>Spustit konzolovou aplikaci pro odeslání lokalizovaného oznámení
Spusťte **konzolovou aplikaci** a odešlete oznámení pro každou kategorii a v každém podporovaném jazyce. Ověřte, že dostáváte oznámení jenom o odebíraných kategoriích a že zpráva odpovídá vybranému národnímu prostředí.

![Oznámení](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili zasílat lokalizovaná nabízená oznámení určitým zařízením, která mají zaregistrované určité značky. Pokud se chcete naučit posílat nabízená oznámení určitým uživatelům, kteří mohou používat několik zařízení, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Nabízená oznámení odesílaná konkrétním uživatelům](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)