---
title: Odesílat oznámení na konkrétní zařízení (Univerzální platforma Windows) | Microsoft Docs
description: Pomocí služby Azure Notification Hubs s značkami v registraci můžete odesílat novinky Univerzální platforma Windows aplikaci.
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
ms.openlocfilehash: aa6714729e48ab63957b5f9a69c5c064c3c34df6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296741"
---
# <a name="tutorial-push-notifications-to-specific-windows-devices-running-universal-windows-platform-applications"></a>Kurz: nabízená oznámení na konkrétní zařízení s Windows, na kterých běží aplikace Univerzální platforma Windows

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak pomocí služby Azure Notification Hubs vysílat oznámení o novinkách. Tento kurz se zabývá aplikacemi pro Windows Store nebo Windows Phone 8,1 (bez technologie Silverlight). Pokud cílíte na Windows Phone 8,1 Silverlight, přečtěte si téma [nabízená oznámení na konkrétní Windows Phone zařízení pomocí služby Azure Notification Hubs](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

V tomto kurzu se naučíte používat službu Azure Notification Hubs k nabízeným oznámením na konkrétní zařízení s Windows, na kterých běží aplikace Univerzální platforma Windows (UWP). Po dokončení tohoto kurzu se můžete zaregistrovat na kategorie nedokončených zpráv, které vás zajímají. Pro tyto kategorie obdržíte nabízená oznámení.

Pokud chcete povolit scénáře vysílání, zahrňte při vytváření registrace v centru oznámení jednu nebo více *značek* . Po odeslání oznámení do značky obdrží oznámení všechna zařízení zaregistrovaná v této značce. Další informace o značkách najdete v tématu [výrazy směrování a značek](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Aplikace Windows Store a Windows Phone Project verze 8,1 a starší nejsou podporovány v aplikaci Visual Studio 2019. Další informace najdete v tématu [cílení a kompatibilita platformy Visual Studio 2019](/visualstudio/releases/2019/compatibility).

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Přidat výběr kategorie do mobilní aplikace
> * Registrace pro oznámení
> * Odesílat označená oznámení
> * Spuštění aplikace a generování oznámení

## <a name="prerequisites"></a>Požadavky

Dokončete [kurz: odeslání oznámení do Univerzální platforma Windows aplikací pomocí Azure Notification Hubs][get-started] před zahájením tohoto kurzu.  

## <a name="add-category-selection-to-the-app"></a>Přidat výběr kategorie do aplikace

Prvním krokem je přidání prvků uživatelského rozhraní do existující hlavní stránky, aby uživatelé mohli vybrat kategorie k registraci. Vybrané kategorie jsou uloženy v zařízení. Po spuštění aplikace se v centru oznámení vytvoří registrace zařízení s vybranými kategoriemi jako značky.

1. Otevřete soubor projektu *MainPage. XAML* a poté zkopírujte následující kód v prvku `Grid`:

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

1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt, vyberte **Přidat** **třídu** > . V okně **Přidat novou položku**pojmenujte *oznámení*třídy a vyberte **Přidat**. V případě potřeby přidejte modifikátor `public` do definice třídy.

1. Do nového souboru přidejte následující příkazy `using`:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Zkopírujte následující kód do nové třídy `Notifications`:

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

    Tato třída používá místní úložiště k uložení kategorií zpráv, které musí toto zařízení přijmout. Namísto volání metody `RegisterNativeAsync` zavolejte `RegisterTemplateAsync` pro registraci kategorií pomocí registrace šablony.

    Pokud chcete registrovat více než jednu šablonu, zadejte název šablony, například *simpleWNSTemplateExample*. Šablony pojmenujte, abyste je mohli aktualizovat nebo odstranit. Můžete zaregistrovat více než jednu šablonu, která bude mít jednu pro informační zprávy a jednu pro dlaždice.

    >[!NOTE]
    > Pomocí Notification Hubs může zařízení registrovat více šablon pomocí stejné značky. V takovém případě příchozí zpráva, která cílí na značku, má za následek doručování více oznámení do zařízení, jednu pro každou šablonu. Tento proces umožňuje zobrazit stejnou zprávu ve více vizuálních oznámeních, jako je například označení jako označení a informační zpráva v aplikaci pro Windows Store.

    Další informace najdete v tématu [šablony](notification-hubs-templates-cross-platform-push-messages.md).

1. V souboru projektu *App.XAML.cs* přidejte do třídy `App` následující vlastnost:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Tato vlastnost slouží k vytvoření a přístup k instanci @no__t 0.

    V kódu Nahraďte zástupné symboly `<hub name>` a `<connection string with listen access>` názvem vašeho centra oznámení a připojovacím řetězcem pro **DefaultListenSharedAccessSignature**, který jste získali dříve.

   > [!NOTE]
   > Vzhledem k tomu, že přihlašovací údaje, které jsou distribuované pomocí klientské aplikace, nejsou obvykle zabezpečené, distribuujte jenom klíč pro přístup k *naslouchání* vaší klientské aplikace. S přístupem k naslouchání se vaše aplikace může zaregistrovat pro oznámení, ale existující registrace se nedají změnit a oznámení nejde odeslat. Úplný přístupový klíč se používá v zabezpečené back-endové službě pro odesílání oznámení a změny stávajících registrací.

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

    Tato metoda vytvoří seznam kategorií a pomocí třídy `Notifications` uloží seznam do místního úložiště. Registruje také odpovídající značky v centru oznámení. Při změně kategorií se registrace znovu vytvoří s novými kategoriemi.

Vaše aplikace teď může do zařízení uložit sadu kategorií v místním úložišti. Aplikace se registruje v centru oznámení, kdykoli uživatelé změní výběr kategorie.

## <a name="register-for-notifications"></a>Registrace pro oznámení

V této části se v centru oznámení při spuštění zaregistrujete pomocí kategorií, které jste uložili v místním úložišti.

> [!NOTE]
> Vzhledem k tomu, že identifikátor URI kanálu, který je přiřazený službou oznamování systému Windows (WNS), se může kdykoli změnit, měli byste se pravidelně registrovat pro oznámení, aby nedocházelo k chybám oznámení. Tento příklad registruje pro oznámení pokaždé, když se aplikace spustí. Pro aplikace, které často spouštíte, například více než jednou denně, můžete pravděpodobně přeskočit registraci, abyste zachovali šířku pásma, pokud od předchozí registrace uplynul méně než jeden den.

1. Chcete-li použít třídu `notifications` k přihlášení k odběru na základě kategorií, otevřete soubor *App.XAML.cs* a pak aktualizujte metodu `InitNotificationsAsync`.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Tento proces zajistí, že se při spuštění aplikace načte kategorie z místního úložiště. Pak si vyžádá registraci těchto kategorií. Vytvořili jste metodu `InitNotificationsAsync` jako součást [odesílání oznámení do Univerzální platforma Windows aplikací pomocí Azure Notification Hubs][get-started] kurzu.
2. V souboru projektu *MainPage.XAML.cs* přidejte následující kód do metody `OnNavigatedTo`:

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

Aplikace je teď dokončená. Může ukládat sadu kategorií do místního úložiště zařízení. Když uživatelé změní výběr kategorie, použijí se uložené kategorie k registraci v centru oznámení. V další části definujete back-end, který do této aplikace může posílat oznámení kategorií.

## <a name="run-the-uwp-app"></a>Spuštění aplikace pro UWP

1. V aplikaci Visual Studio vyberte F5 pro zkompilování a spuštění aplikace. Uživatelské rozhraní aplikace poskytuje sadu přepínačů, které vám umožní vybrat kategorie k přihlášení k odběru.

   ![Porušující aplikace News](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Povolte jeden nebo více přepínačů kategorie a potom vyberte možnost **přihlásit k odběru**.

   Aplikace převede vybrané kategorie na značky a požádá o nové registrace zařízení pro vybrané značky z centra oznámení. Aplikace zobrazí v dialogovém okně registrované kategorie.

    ![Tlačítko pro přepnutí kategorie a přihlášení k odběru](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Vytvoření konzolové aplikace pro odesílání oznámení s příznakem

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Spuštění konzolové aplikace pro posílání oznámení s příznakem

Spusťte aplikaci vytvořenou v předchozí části. Oznámení pro vybrané kategorie se zobrazí jako informační zpráva.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vysílat dodatečné novinky podle kategorie. Back-endové aplikace přenáší oznámení s označením do zařízení, která jsou zaregistrovaná pro příjem oznámení pro tuto značku. Pokud se chcete dozvědět, jak zasílat oznámení konkrétním uživatelům nezávisle na používaném zařízení, přejděte k následujícímu kurzu:

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
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
