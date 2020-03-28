---
title: Zasílání oznámení určitým zařízením (Univerzální platforma Windows) | Microsoft Docs
description: Pomocí Azure Notification Hubs se značkami v registraci můžete odesílat nejnovější zprávy do aplikace pro Univerzální platformu Windows.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 9151870836b1a616a79e54275ed185a425c11f0c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "72385604"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Kurz: Odesílání oznámení konkrétním zařízením s aplikacemi univerzální platformy Windows

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Přehled

Tento kurz ukazuje, jak používat Centra oznámení Azure k vysílání oznámení o nejnovějších zprávách. Tento kurz se týká aplikací pro Windows Store nebo Windows Phone 8.1 (jiné než Silverlight). Pokud cílíte na Windows Phone 8.1 Silverlight, přečtěte si [tématu Nabízená oznámení konkrétním zařízením s Windows Phone pomocí Azure Notification Hubs](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

V tomto kurzu se dozvíte, jak pomocí Centra oznámení Azure nabízená oznámení na konkrétní zařízení s Windows se systémem Universal Platform (UPW) aplikace. Po dokončení kurzu se můžete zaregistrovat do kategorií nejnovějších zpráv, které vás zajímají. Nabízená oznámení obdržíte pouze pro tyto kategorie.

Chcete-li povolit scénáře všesměrového vysílání, zahrňte při vytváření registrace v centru oznámení jednu nebo více *značek.* Když jsou oznámení odeslána na značku, všechna zařízení, která jsou registrována pro značku, obdrží oznámení. Další informace o značkách naleznete v [tématu Směrování a výrazy značek](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> V Sadě Visual Studio 2019 nejsou podporované projekty Windows Storu a Windows Phone verze 8.1 a starší. Další informace najdete v [tématu Visual Studio 2019 Cílení na platformu a kompatibilitu](/visualstudio/releases/2019/compatibility).

V tomto kurzu provést následující úkoly:

> [!div class="checklist"]
> * Přidání výběru kategorií do mobilní aplikace
> * Registrace oznámení
> * Posílání označených oznámení
> * Spuštění aplikace a generování oznámení

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu dokončete [kurz o odesílání oznámení do aplikací pro Univerzální platformu Windows pomocí služby Azure Notification Hubs][get-started].  

## <a name="add-category-selection-to-the-app"></a>Přidání výběru kategorií do aplikace

První krok spočívá v přidání prvků uživatelského rozhraní na stávající hlavní stránku, aby uživatelé mohli vybrat kategorie, které chtějí zaregistrovat. Vybrané kategorie se uloží do zařízení. Když se aplikace spustí, vytvoří registraci zařízení ve vašem centru oznámení s vybranými kategoriemi jako značkami.

1. Otevřete soubor projektu *MainPage.xaml* a zkopírujte `Grid` do prvku následující kód:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a vyberte **přidat** > **třídu**. V **části Přidat novou položku**pojmenujte třídu *Notifications*a vyberte **Přidat**. V případě potřeby `public` přidejte modifikátor do definice třídy.

1. Do nového souboru přidejte následující `using` příkazy:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Zkopírujte následující kód `Notifications` do nové třídy:

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    Tato třída ukládá kategorie novinek, které bude zařízení dostávat, do místního úložiště. Místo volání `RegisterNativeAsync` metody volání `RegisterTemplateAsync` registrace pro kategorie pomocí registrace šablony.

    Pokud chcete zaregistrovat více než jednu šablonu, zadejte název šablony, například *simpleWNSTemplateExample*. Názvy šablon se zadávají proto, aby je bylo možné aktualizovat nebo odstranit. Můžete zaregistrovat více než jednu šablonu, abyste ji měli pro informační zprávy a jednu pro dlaždice.

    >[!NOTE]
    > Pomocí centra oznámení může zařízení zaregistrovat více šablon pomocí stejné značky. V tomto případě příchozí zpráva, která cílí na značku, má za následek doručení více oznámení do zařízení, jednu pro každou šablonu. Tento proces umožňuje zobrazit stejnou zprávu ve více vizuálních oznámeních, například jako informační symbol a jako informační zpráva v aplikaci pro Windows Store.

    Další informace najdete v tématu [Šablony](notification-hubs-templates-cross-platform-push-messages.md).

1. V *souboru projektu App.xaml.cs* přidejte `App` do třídy následující vlastnost:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Tuto vlastnost použijete k `Notifications` vytvoření instance a přístupu k ní.

    Nahraďte v kódu zástupné symboly `<hub name>` a `<connection string with listen access>` názvem vašeho centra oznámení a připojovacím řetězcem pro **DefaultListenSharedAccessSignature**, který jste získali dříve.

   > [!NOTE]
   > Přihlašovací údaje distribuované s klientskou aplikací obvykle nejsou zabezpečené, a proto s klientskou aplikací distribuujte jenom přístupový klíč pro *naslouchání*. Přístup pro naslouchání umožňuje aplikaci registrovat oznámení, ale neumožňuje měnit stávající registrace ani odesílat oznámení. Přístupový klíč pro úplný přístup se používá v zabezpečené službě back-end k posílání oznámení a změně stávajících registrací.

1. Do *souboru MainPage.xaml.cs* přidejte následující řádek:

    ```csharp
    using Windows.UI.Popups;
    ```

1. Do *souboru MainPage.xaml.cs* přidejte následující metodu:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    Tato metoda vytvoří seznam kategorií `Notifications` a používá třídu k uložení seznamu v místním úložišti. Dále zaregistruje odpovídající značky v centru oznámení. Když se změní kategorie, registrace je znovu vytvořena s novými kategoriemi.

Aplikace teď může uchovávat sadu kategorií v místním úložišti v zařízení. Aplikace se zaregistruje v centru oznámení vždy, když uživatelé změní výběr kategorií.

## <a name="register-for-notifications"></a>Registrace oznámení

V této části provedete registraci v centru oznámení při spuštění pomocí kategorií, které jste uložili v místním úložišti.

> [!NOTE]
> Identifikátor URI kanálu přiřazený Službou nabízených oznámení Windows se může kdykoli změnit, a proto byste měli oznámení často registrovat, abyste se vyhnuli chybám v oznámeních. V tomto příkladu se oznámení registrují při každém spuštění aplikace. U aplikací, které často spouštějíte, řekněme více než jednou denně, můžete pravděpodobně přeskočit registraci, abyste zachovali šířku pásma, pokud od předchozí registrace uplynul méně než jeden den.

1. Chcete-li `notifications` použít třídu k odběru na základě kategorií, `InitNotificationsAsync` otevřete soubor *App.xaml.cs* a aktualizujte metodu.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Tento proces zajišťuje, že při spuštění aplikace načte kategorie z místního úložiště. Poté požaduje registraci těchto kategorií. Metodu `InitNotificationsAsync` jste vytvořili jako součást [odesílání oznámení do aplikací univerzální platformy Windows pomocí][get-started] kurzu Centra oznámení Azure.
2. V *souboru projektu MainPage.xaml.cs* přidejte `OnNavigatedTo` do metody následující kód:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    Tento kód aktualizuje hlavní stránku na základě stavu dříve uložených kategorií.

Aplikace je teď hotová. Může uložit sadu kategorií v místním úložišti zařízení. Když uživatelé změní výběr kategorií, uložené kategorie se použijí k registraci v centru oznámení. V další části definujete back-end, který aplikaci posílá oznámení kategorií.

## <a name="run-the-uwp-app"></a>Spuštění aplikace UPW

1. Pokud chcete v sadě Visual Studio kompilovat aplikaci a spustit ji, stiskněte F5. Uživatelské rozhraní aplikace nabízí sadu přepínačů, kterými můžete vybrat odebírané kategorie.

   ![Aplikace Nejnovější zprávy](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Povolte jednu nebo více kategorií a pak vyberte **Přihlásit se k odběru**.

   Aplikace převede vybrané kategorie na značky a u vybraných značek požádá centrum oznámení o registraci nových zařízení. Aplikace zobrazí registrované kategorie v dialogovém okně.

    ![Přepínače kategorií a tlačítko Přihlásit k odběru](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Vytvoření konzolové aplikace pro odesílání označených oznámení

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Spuštění konzolové aplikace pro odesílání označených oznámení

Spusťte aplikaci vytvořenou v předchozí části. Oznámení pro vybrané kategorie se zobrazí jako informační zprávy.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak vysílat nejnovější zprávy podle kategorií. Back-endová aplikace odesílá označená oznámení zařízením, která se zaregistrovala k přijímání oznámení pro tuto značku. Chcete-li se dozvědět, jak nabízená oznámení konkrétním uživatelům nezávisle na tom, jaké zařízení používají, přejdete k následujícímu kurzu:

> [!div class="nextstepaction"]
> [Nabízení lokalizovaných oznámení do aplikací pro Windows pomocí Azure Notification Hubs](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
