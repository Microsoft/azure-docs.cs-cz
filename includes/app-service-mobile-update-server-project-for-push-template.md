---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857353"
---
V této části aktualizujete kód v existujícím projektu Mobile Apps back-end pro odeslání nabízeného oznámení pokaždé, když se přidá nová položka. Tento proces využívá funkci [šablony](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) Azure Notification Hubs, která umožňuje nabízená oznámení mezi platformami. Různí klienti jsou registrováni pro nabízená oznámení pomocí šablon a jedinou univerzální nabízenou instalací lze získat na všechny klientské platformy.

Vyberte jeden z následujících postupů, který odpovídá vašemu typu&mdash;back-end projektu buď back-end [.NET](#dotnet) , nebo back- [End Node. js](#nodejs).

### <a name="dotnet"></a>Projekt back-end .NET

1. V aplikaci Visual Studio klikněte pravým tlačítkem myši na serverový projekt. Pak vyberte **Spravovat balíčky NuGet**. Vyhledejte a pak vyberte nainstalovat. `Microsoft.Azure.NotificationHubs` Tento proces nainstaluje knihovnu Notification Hubs pro odesílání oznámení z back-endu.
2. V projektu serveru otevřete Controllers > **TodoItemController.cs**. Pak přidejte následující příkazy using:

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

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

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

    Tento proces pošle oznámení šablony obsahující položku. Text při vložení nové položky

4. Publikujte projekt serveru znovu.

### <a name="nodejs"></a>Back-end projekt Node. js

1. Nastavte svůj back-end projekt.
2. Nahraďte existující kód v TodoItem. js následujícím kódem:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    Tento proces pošle oznámení šablony, které obsahuje položku. text při vložení nové položky.

3. Při úpravách souboru na místním počítači znovu publikujte projekt serveru.
