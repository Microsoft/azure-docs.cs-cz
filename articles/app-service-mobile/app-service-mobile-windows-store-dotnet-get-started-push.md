---
title: Přidání nabízených oznámení do aplikace Univerzální platforma Windows (UWP) | Microsoft Docs
description: Naučte se používat Azure App Service Mobile Apps a Azure Notification Hubs k odesílání nabízených oznámení do aplikace pro Univerzální platforma Windows (UWP).
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: e3e82c971fee7f7dd95e6f9ef72631e8e82ebe7f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025261"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Přidání nabízených oznámení do aplikace pro Windows

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj koncových a integrovaných služeb od centrálního vývoje mobilních aplikací. Vývojáři **mohou pomocí sestavování**, **testování** a **distribuce** služeb nastavit kanál průběžné integrace a doručování. Po nasazení aplikace mohou vývojáři sledovat stav a využití své aplikace pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **push** . Vývojáři můžou také využít **ověřování** k ověřování uživatelů a **datových** služeb, aby zachovaly a synchronizovaly data aplikací v cloudu.
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) ještě dnes.

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu [Windows Quick Start](app-service-mobile-windows-store-dotnet-get-started.md) , aby bylo při každém vložení záznamu nabízené oznámení odesíláno do zařízení.

Pokud nepoužíváte stažený projekt serveru pro rychlé zahájení, budete potřebovat balíček rozšíření nabízených oznámení. Další informace najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

## <a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registrace aplikace pro nabízená oznámení

Musíte odeslat aplikaci do Microsoft Store a potom nakonfigurovat projekt serveru pro integraci se službou [Windows Notification Services (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) , abyste mohli odeslat nabízení.

1. V aplikaci Visual Studio Průzkumník řešení klikněte pravým tlačítkem na projekt aplikace UWP, klikněte na **uložit** > **přidružit aplikaci k obchodu...** .

    ![Přidružit aplikaci k Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. V průvodci klikněte na **Další**, přihlaste se pomocí svého účet Microsoft, zadejte název vaší aplikace do pole **rezervovat nový název aplikace**a pak klikněte na **rezervovat**.
3. Po úspěšném vytvoření registrace aplikace vyberte název nové aplikace, klikněte na **Další**a pak klikněte na **přidružit**. Tím se do manifestu aplikace přidá požadované registrační informace Microsoft Store.
4. Přejděte na [portál pro registraci aplikací](https://apps.dev.microsoft.com/) a přihlaste se pomocí svého účet Microsoft. Klikněte na aplikaci pro Windows Store, kterou jste přidružili v předchozím kroku.
5. Na stránce registrace si poznamenejte hodnotu v části **tajné klíče aplikace** a **identifikátor SID balíčku**, který budete dál používat ke konfiguraci back-endu mobilní aplikace.

    ![Přidružit aplikaci k Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Tajný klíč klienta a SID balíčku jsou důležitá pověření zabezpečení. Tyto hodnoty s nikým nesdílejte ani je nedistribuujte s vaší aplikací. **ID aplikace** se používá s tajným klíčem ke konfiguraci ověřování pomocí účtu Microsoft.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) taky obsahuje pokyny pro konfiguraci aplikací pro UWP pro nabízená oznámení.

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurace back-endu pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Aktualizace serveru pro odesílání nabízených oznámení

Použijte níže uvedený postup, který odpovídá vašemu typu back-endu @ no__t-0either [.NET back-end](#dotnet) nebo [Node. js back-end](#nodejs).

### <a name="dotnet"></a>Projekt back-endu .NET

1. V aplikaci Visual Studio klikněte pravým tlačítkem myši na serverový projekt, klikněte na **Spravovat balíčky NuGet**, vyhledejte Microsoft. Azure. NotificationHubs a klikněte na **nainstalovat**. Tím se nainstaluje Klientská knihovna Notification Hubs.
2. Rozbalte položku **řadiče**, otevřete TodoItemController.cs a přidejte následující příkazy using:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. Do metody **PostTodoItem** přidejte po volání **InsertAsync**následující kód:

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

    Tento kód informuje centrum oznámení o odeslání nabízeného oznámení po vložení nové položky.

4. Publikujte projekt serveru znovu.

### <a name="nodejs"></a>Back-end projekt Node. js
1. Nastavte svůj back-end projekt.
2. Existující kód nahraďte v souboru TodoItem. js následujícím kódem:

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

    Tím se pošle oznámení WNS informační zprávy, která obsahuje položku. text při vložení nové položky ToDo.

3. Při úpravách souboru na místním počítači znovu publikujte projekt serveru.

## <a id="update-app"></a>Přidání nabízených oznámení do aplikace
V dalším kroku se musí vaše aplikace při spuštění zaregistrovat pro nabízená oznámení. Pokud jste již povolili ověřování, ujistěte se, že se uživatel přihlásí před pokusem o registraci nabízených oznámení.

1. Otevřete soubor projektu **App.XAML.cs** a přidejte následující příkazy `using`:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Ve stejném souboru přidejte do třídy **App** následující definici metody **InitNotificationsAsync** :

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

    Tento kód načte parametr channeluri pro aplikaci z WNS a pak zaregistruje tento parametr channeluri do mobilní aplikace App Service.

3. V horní části obslužné rutiny události s možností **spuštění** v **App.XAML.cs**přidejte modifikátor **Async** do definice metody a přidejte následující volání do nové metody **InitNotificationsAsync** , jako v následujícím příkladu:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    To zaručuje, že krátkodobý parametr channeluri je zaregistrován při každém spuštění aplikace.

4. Sestavte projekt aplikace pro UWP znovu. Vaše aplikace je teď připravena přijímat oznámení informačního nápisu.

## <a id="test"></a>Testování nabízených oznámení ve vaší aplikaci

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Další kroky

Další informace o nabízených oznámeních:

* [Jak používat spravovaného klienta pro Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Šablony vám poskytnou flexibilitu při odesílání nabízených oznámení mezi platformami a lokalizovaných nabízených oznámení. Přečtěte si, jak registrovat šablony.
* [Diagnostikovat problémy s nabízenými oznámeními](../notification-hubs/notification-hubs-push-notification-fixer.md) Existují různé důvody, proč mohou být oznámení vyřazena nebo nekončí na zařízeních. V tomto tématu se dozvíte, jak analyzovat a zjistit hlavní příčinu selhání nabízených oznámení.

Zvažte pokračování v jednom z následujících kurzů:

* [Přidání ověřování do vaší aplikace](app-service-mobile-windows-store-dotnet-get-started-users.md) Zjistěte, jak ověřovat uživatele vaší aplikace pomocí zprostředkovatele identity.
* [Povolit offline synchronizaci vaší aplikace](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Naučte se, jak pomocí back-endu mobilní aplikace přidat do aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací &mdash; zobrazovat, přidávat a upravovat data &mdash; i v případě, že nemají připojení k síti.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
