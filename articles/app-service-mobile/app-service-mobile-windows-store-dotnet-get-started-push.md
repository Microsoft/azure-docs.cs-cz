---
title: Přidání nabízených oznámení do aplikace pro univerzální platformu Windows (UWP) | Microsoft Docs
description: Naučte se používat Azure App Service Mobile Apps a Azure Notification Hubs k odesílání nabízených oznámení do aplikace pro univerzální platformu Windows (UWP).
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
ms.openlocfilehash: 9e3ed6d19b0f830923745ad0263c5c4f920c0f51
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597854"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Přidání nabízených oznámení do aplikace pro Windows
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled
V tomto kurzu přidáte nabízená oznámení [Windows úvodní](app-service-mobile-windows-store-dotnet-get-started.md) projektu tak, aby nabízených oznámení se odešle do zařízení pokaždé, když vložení záznamu.

Pokud použijete serverový projekt stažené rychlý start, budete potřebovat balíček rozšíření nabízená oznámení. V tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) Další informace.

## <a name="configure-hub"></a>Konfigurace centra oznámení
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registrace aplikace pro nabízená oznámení
Budete muset odeslat v aplikaci Microsoft Store a pak nakonfigurujete svůj projekt server k integraci s Windows oznámení služby (WNS) k odesílání nabízených.

1. V Průzkumníku řešení Visual Studio, klikněte pravým tlačítkem na projekt aplikace UPW, klikněte na tlačítko **úložiště** > **přidružit aplikaci ve Store...** .

    ![Propojit aplikaci se Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
2. V průvodci klikněte na tlačítko **Další**, přihlaste se pomocí účtu Microsoft, zadejte název aplikace v rámci **rezervovat nový název aplikace**, pak klikněte na tlačítko **rezervy**.
3. Po registraci aplikace je úspěšně vytvořen, vyberte nový název aplikace, klikněte na **Další**a potom klikněte na **přidružit**. Tento postup přidá požadované informace o registraci Microsoft Store do manifestu aplikace.
4. Přejděte na [Windows Dev Center](https://dev.windows.com/en-us/overview), přihlaste se pomocí účtu Microsoft, klikněte na novou registraci aplikace v **Moje aplikace**, pak rozbalte **služby**  >   **Nabízená oznámení**.
5. V **nabízená oznámení** klikněte na tlačítko **Web služeb Live Services** pod **mobilní služby Microsoft Azure**.
6. Na stránce registrace si poznamenejte hodnoty v části **tajné klíče aplikace** a **SID balíčku**, které budou vedle použít ke konfiguraci váš back-end mobilní aplikace.

    ![Propojit aplikaci se Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Tajný klíč klienta a SID balíčku jsou důležitá pověření zabezpečení. Tyto hodnoty s nikým nesdílejte ani je nedistribuujte s vaší aplikací. **Id aplikace** ke konfiguraci ověřování Account Microsoft se používá s tajným klíčem.
   >
   >

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurace back-end k odesílání nabízených oznámení
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Aktualizace serveru k odesílání nabízených oznámení
Použijte následující postup, který odpovídá typu vašeho projektu back-end&mdash;buď [.NET back-end](#dotnet) nebo [back-end Node.js](#nodejs).

### <a name="dotnet"></a>Rozhraní .NET back-end projektu
1. V sadě Visual Studio, klikněte pravým tlačítkem na serverový projekt a klikněte na tlačítko **spravovat balíčky NuGet**, vyhledejte Microsoft.Azure.NotificationHubs a pak klikněte na tlačítko **nainstalovat**. Tím se nainstaluje knihovnu klienta služby Notification Hubs.
2. Rozbalte položku **řadiče**, otevřete TodoItemController.cs a přidejte následující příkazy:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. V **PostTodoItem** metoda, přidejte následující kód po volání **InsertAsync**:

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

    Tento kód informuje centra oznámení k odesílání nabízených oznámení po vložení nové položky.
4. Znovu publikujte serverový projekt.

### <a name="nodejs"></a>Projektu back-end Node.js
1. Pokud jste zatím žádný nevytvořili, [stažení projektu pro rychlý Start](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) nebo použijte jiný [online editor na webu Azure portal](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Nahraďte stávající kód v souboru todoitem.js s následujícími službami:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
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

    Tím se odešle oznámení s informační zprávou WNS, který obsahuje item.text při vložení nová položka todo.
3. Při úpravách souboru v místním počítači, znovu publikujte serverový projekt.

## <a id="update-app"></a>Přidání nabízených oznámení do vaší aplikace
V dalším kroku musí aplikaci zaregistrovat pro nabízená oznámení na spuštění. Pokud už jste povolili ověřování, ujistěte se, že uživatel přihlásí než se pokusíte zaregistrovat pro nabízená oznámení.

1. Otevřete **App.xaml.cs** souboru projektu a přidejte následující `using` příkazy:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
2. Do stejného souboru přidejte následující **InitNotificationsAsync** metoda definici tak, aby **aplikace** třídy:

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Tento kód načte ChannelURI pro aplikaci z WNS a pak zaregistruje tento ChannelURI pomocí aplikace služby mobilní aplikace.
3. V horní části **OnLaunched** obslužné rutiny událostí v **App.xaml.cs**, přidejte **asynchronní** modifikátor k definici metoda a přidejte následující volání do nového  **InitNotificationsAsync** metoda jako v následujícím příkladu:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    To zaručuje, že krátkodobou ChannelURI je zaregistrován pokaždé, když je aplikace spuštěna.
4. Znovu sestavte projekt aplikace UPW. Vaše aplikace je teď připravena přijímat oznámení informačního nápisu.

## <a id="test"></a>Nabízená oznámení v aplikaci
[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Další kroky
Další informace o nabízených oznámení:

* [Jak používat spravovaného klienta pro Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) šablony poskytují flexibilitu k odesílání nabízených oznámení napříč platformami a lokalizované nabízených oznámení. Zjistěte, jak zaregistrovat šablony.
* [Diagnostikovat problémy nabízená oznámení](../notification-hubs/notification-hubs-push-notification-fixer.md) existuje různých důvodů, proč oznámení může získat vyřadit ani nekončí na zařízení. Toto téma ukazuje, jak analyzovat a zjistěte příčinu selhání nabízená oznámení.

Vezměte v úvahu pokračovat na jednu z následujících kurzů:

* [Přidání ověřování do aplikace](app-service-mobile-windows-store-dotnet-get-started-users.md) zjistěte, jak ověřovat uživatele vaší aplikace pomocí zprostředkovatele identity.
* [Zapnutí offline synchronizace pro vaši aplikaci](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) informace o postupu přidání vaší aplikace pomocí back-end mobilní aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací &mdash; zobrazovat, přidávat a upravovat data &mdash; i v případě, že nemají připojení k síti.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
