---
title: Přidání nabízených oznámení do aplikace pro univerzální platformu Windows (UPW) | Dokumentace Microsoftu
description: Další informace o použití Azure App Service Mobile Apps a Azure Notification Hubs k odesílání nabízených oznámení do aplikace pro univerzální platformu Windows (UPW).
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 7efd853e7b66933cac811625d7510139864f41f3
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001850"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Přidání nabízených oznámení do aplikace pro Windows

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení [Windows úvodní](app-service-mobile-windows-store-dotnet-get-started.md) projekt tak, aby na zařízení přijde nabízené oznámení pokaždé, když se vložení záznamu.

Pokud použijete serverový projekt stažené rychlý start, budete potřebovat balíček rozšíření nabízené oznámení. Zobrazit [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) Další informace.

## <a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registrace aplikace pro nabízená oznámení

Budete potřebovat k odeslání aplikace do Microsoft Store a potom nakonfigurujte svůj serverový projekt pro integraci s [služby oznámení Windows (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) k odesílání nabízených oznámení.

1. V Průzkumníku řešení Visual Studio, klikněte pravým tlačítkem na projekt aplikace UPW, klikněte na tlačítko **Store** > **přidružit aplikaci Store...** .

    ![Propojit aplikaci s Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. V průvodci klikněte na tlačítko **Další**, přihlaste se pomocí účtu Microsoft, zadejte název pro vaši aplikaci v **rezervovat nový název aplikace**, pak klikněte na tlačítko **rezervy**.
3. Po registraci aplikace je úspěšně vytvořen, zadat nový název aplikace, klikněte na tlačítko **Další**a potom klikněte na tlačítko **přidružit**. To přidá požadované informace o registraci Microsoft Store do manifestu aplikace.
4. Přejděte [portál pro registraci aplikací](https://apps.dev.microsoft.com/) a přihlaste se pomocí svého účtu Microsoft. Klikněte na Windows Store aplikaci, kterou jste k v předchozím kroku.
5. Na stránce registrace, poznamenejte si hodnoty v rámci **tajných klíčů aplikací** a **SID balíčku**, které potom použijete ke konfiguraci back-endu mobilní aplikace.

    ![Propojit aplikaci s Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Tajný klíč klienta a SID balíčku jsou důležitá pověření zabezpečení. Tyto hodnoty s nikým nesdílejte ani je nedistribuujte s vaší aplikací. **Id aplikace** je pomocí tajného klíče, použít ke konfiguraci ověřování Account Microsoft.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) také obsahuje pokyny pro konfiguraci aplikací pro UPW pro nabízená oznámení.

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurace back-endu k odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Aktualizovat server k odesílání nabízených oznámení

Pomocí níže uvedeného postupu, který odpovídá typu vašeho back-endový projekt&mdash;buď [back-endem .NET](#dotnet) nebo [back-end Node.js](#nodejs).

### <a name="dotnet"></a>Projekt back-end .NET

1. V sadě Visual Studio, klikněte pravým tlačítkem na projekt serveru a klikněte na tlačítko **spravovat balíčky NuGet**, vyhledejte Microsoft.Azure.NotificationHubs a pak klikněte na tlačítko **nainstalovat**. Tím se nainstaluje klientské knihovně pro Notification Hubs.
2. Rozbalte **řadiče**otevřete TodoItemController.cs a přidejte následující příkazy using:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. V **PostTodoItem** metodu, přidejte následující kód po volání **InsertAsync**:

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the Mobile App.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create the notification hub client.
    NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Define a WNS payload
    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                            + item.Text + @"</text></binding></visual></toast>";
    try
    {
        // Send the push notification.
        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    Tento kód říká centra oznámení k odesílání nabízených oznámení po vložení nové položky.

4. Znovu publikujte projekt serveru.

### <a name="nodejs"></a>Projekt back-end Node.js
1. Pokud jste tak již neučinili, [stáhnout projekt rychlý Start](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) nebo použijte jiný [online editoru na webu Azure Portal](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Nahraďte stávající kód v souboru todoitem.js následujícími způsoby:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs
    logger.info('Running TodoItem.insert');

    // Define the WNS payload that contains the new item Text.
    var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                + context.item.text + "</text></binding></visual></toast>";

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a WNS native toast notification.
                context.push.wns.sendToast(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute()
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;
    ```

    Tím se odešle oznámení s informační zprávou nabízených oznámení Windows, který obsahuje item.text při vložení nové položky seznamu úkolů.

3. Při úpravách souboru na místním počítači, znovu publikujte projekt serveru.

## <a id="update-app"></a>Přidání nabízených oznámení do vaší aplikace
V dalším kroku musí aplikaci zaregistrovat pro nabízená oznámení na spuštění. Pokud už jste povolili ověřování, ujistěte se, že uživatel přihlásí před pokusem o registraci pro nabízená oznámení.

1. Otevřít **App.xaml.cs** soubor projektu a přidejte následující `using` příkazy:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Ve stejném souboru přidejte následující **InitNotificationsAsync** definici metody **aplikace** třídy:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        // Get a channel URI from WNS.
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        // Register the channel URI with Notification Hubs.
        await App.MobileService.GetPush().RegisterAsync(channel.Uri);
    }
    ```

    Tento kód načte parametr ChannelURI pro aplikaci z WNS a pak zaregistruje tento parametr ChannelURI pomocí mobilní aplikace App Service.

3. V horní části **OnLaunched** obslužné rutině událostí ve **App.xaml.cs**, přidejte **asynchronní** modifikátor definici metody a přidejte následující volání do nového  **InitNotificationsAsync** metody, jako v následujícím příkladu:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Zaručí se tak, že je parametr ChannelURI krátkodobé a jednorázové zaregistrován při každém spuštění aplikace.

4. Znovu sestavte svůj projekt aplikace UPW. Vaše aplikace je teď připravena přijímat oznámení informačního nápisu.

## <a id="test"></a>Nabízená oznámení ve vaší aplikaci

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Další kroky

Další informace o nabízených oznámení:

* [Jak používat spravovaného klienta pro Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) šablony poskytují flexibilitu pro odesílání nabízených oznámení napříč platformami a lokalizovaných nabízených oznámení. Informace o postupu registrace šablony.
* [Diagnostikujte problémy nabízená oznámení](../notification-hubs/notification-hubs-push-notification-fixer.md) existují různé důvody, proč oznámení může získáte nebo nekončí na zařízeních. V tomto tématu se dozvíte, jak analyzovat a zjistěte příčinu selhání nabízené oznámení.

Vezměte v úvahu pokračováním jednu z následujících kurzů:

* [Přidání ověřování do vaší aplikace](app-service-mobile-windows-store-dotnet-get-started-users.md) Zjistěte, jak ověřovat uživatele vaší aplikace pomocí zprostředkovatele identity.
* [Povolení offline synchronizace u aplikace](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) zjistěte, jak přidat aplikace pomocí back-endu mobilní aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací &mdash; zobrazovat, přidávat a upravovat data &mdash; i v případě, že nemají připojení k síti.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
