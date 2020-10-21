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
ms.custom: mvc, devx-track-csharp
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 2c77eba69fd914e8ecc7d08a1b16f61ceefe101b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320572"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Kurz: odesílání oznámení na konkrétní zařízení s Univerzální platforma Windows aplikacemi

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak pomocí služby Azure Notification Hubs vysílat oznámení o novinkách. Tento kurz se zabývá aplikacemi pro Windows Store nebo Windows Phone 8,1 (bez technologie Silverlight). Pokud cílíte na Windows Phone 8,1 Silverlight, přečtěte si téma [nabízená oznámení na konkrétní Windows Phone zařízení pomocí služby Azure Notification Hubs](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

V tomto kurzu se naučíte používat službu Azure Notification Hubs k nabízeným oznámením na konkrétní zařízení s Windows, na kterých běží aplikace Univerzální platforma Windows (UWP). Po dokončení tohoto kurzu se můžete zaregistrovat na kategorie nedokončených zpráv, které vás zajímají. Pro tyto kategorie obdržíte nabízená oznámení.

Pokud chcete povolit scénáře vysílání, zahrňte při vytváření registrace v centru oznámení jednu nebo více *značek* . Po odeslání oznámení do značky obdrží oznámení všechna zařízení zaregistrovaná v této značce. Další informace o značkách najdete v tématu [výrazy směrování a značek](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Aplikace Windows Store a Windows Phone Project verze 8,1 a starší nejsou podporovány v aplikaci Visual Studio 2019. Další informace najdete v tématu [cílení a kompatibilita platformy Visual Studio 2019](/visualstudio/releases/2019/compatibility).

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Přidání výběru kategorií do mobilní aplikace
> * Registrace oznámení
> * Posílání označených oznámení
> * Spuštění aplikace a generování oznámení

## <a name="prerequisites"></a>Předpoklady

Před zahájením tohoto kurzu dokončete [kurz o odesílání oznámení do aplikací pro Univerzální platformu Windows pomocí služby Azure Notification Hubs][get-started].  

## <a name="add-category-selection-to-the-app"></a>Přidání výběru kategorií do aplikace

První krok spočívá v přidání prvků uživatelského rozhraní na stávající hlavní stránku, aby uživatelé mohli vybrat kategorie, které chtějí zaregistrovat. Vybrané kategorie se uloží do zařízení. Po spuštění aplikace se v centru oznámení vytvoří registrace zařízení s vybranými kategoriemi jako značky.

1. Otevřete soubor projektu *MainPage. XAML* a poté zkopírujte následující kód v `Grid` elementu:

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

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt, vyberte **Přidat**  >  **třídu**. V okně **Přidat novou položku**pojmenujte *oznámení*třídy a vyberte **Přidat**. V případě potřeby přidejte `public` modifikátor do definice třídy.

1. `using`Do nového souboru přidejte následující příkazy:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Zkopírujte následující kód do nové `Notifications` třídy:

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

    Tato třída ukládá kategorie novinek, které bude zařízení dostávat, do místního úložiště. Namísto volání `RegisterNativeAsync` metody zavolejte `RegisterTemplateAsync` k registraci pro kategorie pomocí registrace šablony.

    Pokud chcete registrovat více než jednu šablonu, zadejte název šablony, například *simpleWNSTemplateExample*. Názvy šablon se zadávají proto, aby je bylo možné aktualizovat nebo odstranit. Můžete zaregistrovat více než jednu šablonu, která bude mít jednu pro informační zprávy a jednu pro dlaždice.

    >[!NOTE]
    > Pomocí Notification Hubs může zařízení registrovat více šablon pomocí stejné značky. V takovém případě příchozí zpráva, která cílí na značku, má za následek doručování více oznámení do zařízení, jednu pro každou šablonu. Tento proces umožňuje zobrazit stejnou zprávu ve více vizuálních oznámeních, jako je například označení jako označení a informační zpráva v aplikaci pro Windows Store.

    Další informace najdete v tématu [Šablony](notification-hubs-templates-cross-platform-push-messages.md).

1. V souboru projektu *App.XAML.cs* přidejte do třídy následující vlastnost `App` :

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Tato vlastnost slouží k vytvoření instance a k jejímu přístupu `Notifications` .

    Nahraďte v kódu zástupné symboly `<hub name>` a `<connection string with listen access>` názvem vašeho centra oznámení a připojovacím řetězcem pro **DefaultListenSharedAccessSignature**, který jste získali dříve.

   > [!NOTE]
   > Přihlašovací údaje distribuované s klientskou aplikací obvykle nejsou zabezpečené, a proto s klientskou aplikací distribuujte jenom přístupový klíč pro *naslouchání*. Přístup pro naslouchání umožňuje aplikaci registrovat oznámení, ale neumožňuje měnit stávající registrace ani odesílat oznámení. Přístupový klíč pro úplný přístup se používá v zabezpečené službě back-end k posílání oznámení a změně stávajících registrací.

1. Do souboru *MainPage.XAML.cs* přidejte následující řádek:

    ```csharp
    using Windows.UI.Popups;
    ```

1. Do souboru *MainPage.XAML.cs* přidejte následující metodu:

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

    Tato metoda vytvoří seznam kategorií a pomocí `Notifications` třídy uloží seznam do místního úložiště. Dále zaregistruje odpovídající značky v centru oznámení. Při změně kategorií se registrace znovu vytvoří s novými kategoriemi.

Aplikace teď může uchovávat sadu kategorií v místním úložišti v zařízení. Aplikace se zaregistruje v centru oznámení vždy, když uživatelé změní výběr kategorií.

## <a name="register-for-notifications"></a>Registrace oznámení

V této části provedete registraci v centru oznámení při spuštění pomocí kategorií, které jste uložili v místním úložišti.

> [!NOTE]
> Identifikátor URI kanálu přiřazený Službou nabízených oznámení Windows se může kdykoli změnit, a proto byste měli oznámení často registrovat, abyste se vyhnuli chybám v oznámeních. V tomto příkladu se oznámení registrují při každém spuštění aplikace. Pro aplikace, které často spouštíte, například více než jednou denně, můžete pravděpodobně přeskočit registraci, abyste zachovali šířku pásma, pokud od předchozí registrace uplynul méně než jeden den.

1. Chcete-li použít `notifications` třídu k přihlášení k odběru na základě kategorií, otevřete soubor *App.XAML.cs* a pak aktualizujte `InitNotificationsAsync` metodu.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Tento proces zajistí, že se při spuštění aplikace načte kategorie z místního úložiště. Pak si vyžádá registraci těchto kategorií. Tuto metodu jste vytvořili `InitNotificationsAsync` jako součást [odesílání oznámení Univerzální platforma Windows aplikací pomocí Azure Notification Hubs][get-started] kurzu.
2. Do souboru projektu *MainPage.XAML.cs* přidejte následující kód do `OnNavigatedTo` metody:

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

Aplikace je teď hotová. Může ukládat sadu kategorií do místního úložiště zařízení. Když uživatelé změní výběr kategorie, použijí se uložené kategorie k registraci v centru oznámení. V další části definujete back-end, který aplikaci posílá oznámení kategorií.

## <a name="run-the-uwp-app"></a>Spuštění aplikace pro UWP

1. Pokud chcete v sadě Visual Studio kompilovat aplikaci a spustit ji, stiskněte F5. Uživatelské rozhraní aplikace nabízí sadu přepínačů, kterými můžete vybrat odebírané kategorie.

   ![Aplikace Nejnovější zprávy](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Povolte jeden nebo více přepínačů kategorie a potom vyberte možnost **přihlásit k odběru**.

   Aplikace převede vybrané kategorie na značky a u vybraných značek požádá centrum oznámení o registraci nových zařízení. Aplikace zobrazí v dialogovém okně registrované kategorie.

    ![Přepínače kategorií a tlačítko Přihlásit k odběru](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Vytvoření konzolové aplikace pro odesílání oznámení s příznakem

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Spuštění konzolové aplikace pro posílání oznámení s příznakem

Spusťte aplikaci vytvořenou v předchozí části. Oznámení pro vybrané kategorie se zobrazí jako informační zprávy.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak vysílat nejnovější zprávy podle kategorií. Back-endové aplikace přenáší oznámení s označením do zařízení, která jsou zaregistrovaná pro příjem oznámení pro tuto značku. Pokud se chcete dozvědět, jak zasílat oznámení konkrétním uživatelům nezávisle na používaném zařízení, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
> [Doručovat lokalizovaná oznámení do aplikací pro Windows pomocí Azure Notification Hubs](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

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
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)