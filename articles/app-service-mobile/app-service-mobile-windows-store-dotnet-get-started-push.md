---
title: Přidání nabízených oznámení do aplikace UPW
description: Přečtěte si, jak pomocí mobilních aplikací služby Azure App Service a center oznámení Azure odesílat nabízená oznámení do aplikace univerzální platformy Windows (UPW).
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6f200e9649a00bfe890d46f86e62404f1a7e844f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366286"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Přidání nabízených oznámení do aplikace pro Windows

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu [rychlého spuštění systému Windows](app-service-mobile-windows-store-dotnet-get-started.md) tak, aby nabízené oznámení je odeslána do zařízení při každém vložení záznamu.

Pokud nepoužíváte stažený projekt serveru rychlého startu, budete potřebovat balíček rozšíření nabízených oznámení. Další informace [najdete v tématu Práce s back-endovým serverem SDK .NET pro Mobilní aplikace Azure.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registrace aplikace pro nabízená oznámení

Je třeba odeslat aplikaci do Microsoft Storu a potom nakonfigurovat projekt serveru tak, aby se integroval se [službou Nabízených oznámení systému Windows (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) a odeslat nabízenou položku.

1. V Průzkumníku řešení Visual Studia klikněte pravým tlačítkem myši na projekt aplikace UPW a klikněte na **Store** > **Associate App with the Store...**.

    ![Přidružení aplikace k Microsoft Storu](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. V průvodci klikněte na **Další**, přihlaste se pomocí účtu Microsoft, zadejte název aplikace do **pole Rezervovat nový název aplikace**a klikněte na **Rezervovat**.
3. Po úspěšném vytvoření registrace aplikace vyberte nový název aplikace, klikněte na **Další**a potom klikněte na **Přidružit**. Tím přidáte požadované informace o registraci v obchodě Microsoft Store do manifestu aplikace.
4. Přejděte na [portál pro registraci aplikací](https://apps.dev.microsoft.com/) a přihlaste se pomocí svého účtu Microsoft. Klikněte na aplikaci pro Windows Store, kterou jste přidružili v předchozím kroku.
5. Na stránce registrace poznamenejte hodnotu v části **Tajné klíče aplikace** a **sid balíčku**, který budete dále používat ke konfiguraci back-endu mobilní aplikace.

    ![Přidružení aplikace k Microsoft Storu](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Tajný klíč klienta a SID balíčku jsou důležitá pověření zabezpečení. Tyto hodnoty s nikým nesdílejte ani je nedistribuujte s vaší aplikací. **ID aplikace** se používá s tajným klíčem ke konfiguraci ověřování účtu Microsoft.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) má také pokyny pro konfiguraci upw aplikací pro nabízená oznámení.

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurace back-endu pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a name="update-the-server-to-send-push-notifications"></a><a id="update-service"></a>Aktualizace serveru pro odesílání nabízených oznámení

Použijte níže uvedený postup, který&mdash;odpovídá typu back-endového projektu [.NET back-end](#dotnet) nebo [Node.js back-end](#nodejs).

### <a name="net-backend-project"></a><a name="dotnet"></a>Back-endový projekt ROZHRANÍ .NET

1. V sadě Visual Studio klikněte pravým tlačítkem myši na serverový projekt a klikněte na **Spravovat balíčky NuGet**, vyhledejte Microsoft.Azure.NotificationHubs a klikněte na **Nainstalovat**. Tím nainstalujete klientskou knihovnu Centra oznámení.
2. Rozbalte **řadiče**, otevřete TodoItemController.cs a přidejte následující příkazy:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. V **postTodoItem** metoda, přidejte následující kód po volání **InsertAsync**:

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

    Tento kód říká centru oznámení odeslat nabízené oznámení po vložení nové položky.

4. Znovu publikujte projekt serveru.

### <a name="nodejs-backend-project"></a><a name="nodejs"></a>Back-endový projekt Node.js
1. Nastavte back-endový projekt.
2. Nahraďte existující kód v souboru todoitem.js následujícím:

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

    To odešle informační zprávu WNS, která obsahuje item.text při vložení nové položky todo.

3. Při úpravách souboru v místním počítači znovu publikujte projekt serveru.

## <a name="add-push-notifications-to-your-app"></a><a id="update-app"></a>Přidání nabízených oznámení do aplikace
Dále se aplikace musí zaregistrovat pro nabízená oznámení při spuštění. Pokud jste již povolili ověřování, ujistěte se, že uživatel přihlášení před pokusem o registraci pro nabízená oznámení.

1. Otevřete soubor **projektu App.xaml.cs** `using` a přidejte následující příkazy:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Ve stejném souboru přidejte do třídy **App** následující definici metody **InitNotificationsAsync:**

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

    Tento kód načte ChannelURI pro aplikaci z WNS a pak zaregistruje, že ChannelURI s vaší mobilní aplikace Služby aplikací.

3. V horní části **onLaunched** obslužné rutiny události v **App.xaml.cs**přidejte modifikátor **async** do definice metody a přidejte následující volání do nové metody **InitNotificationsAsync,** jako v následujícím příkladu:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    To zaručuje, že krátkodobá ChannelURI je registrována při každém spuštění aplikace.

4. Znovu sestavte projekt aplikace UPW. Vaše aplikace je teď připravena přijímat oznámení informačního nápisu.

## <a name="test-push-notifications-in-your-app"></a><a id="test"></a>Testování nabízených oznámení v aplikaci

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a name="next-steps"></a><a id="more"></a>Další kroky

Další informace o nabízených oznámeních:

* [Jak používat spravovaného klienta pro Mobilní aplikace Azure](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Šablony poskytují flexibilitu při odesílání nabízených oznámení napříč platformami a lokalizovaných nabízených oznámení. Přečtěte si, jak registrovat šablony.
* [Diagnostika problémů s nabízená oznámení](../notification-hubs/notification-hubs-push-notification-fixer.md) Existují různé důvody, proč oznámení mohou být zrušena nebo nekončí na zařízeních. Toto téma ukazuje, jak analyzovat a zjistit hlavní příčinu selhání nabízených oznámení.

Zvažte pokračování v jednom z následujících kurzů:

* [Přidání ověřování do vaší aplikace](app-service-mobile-windows-store-dotnet-get-started-users.md) Zjistěte, jak ověřovat uživatele vaší aplikace pomocí zprostředkovatele identity.
* [Povolení offline synchronizace pro aplikaci](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Přečtěte si, jak přidat offline podporu aplikace pomocí back-endu mobilní aplikace. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací &mdash; zobrazovat, přidávat a upravovat data &mdash; i v případě, že nemají připojení k síti.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
