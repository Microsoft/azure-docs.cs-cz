---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 0e7118ff6a2860351a7bfa38637f1d767b0f4a2d
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42817437"
---
V této části můžete aktualizovat kód v existujícím projektu back-end Mobile Apps k odesílání nabízených oznámení pokaždé, když se přidá nová položka. Tento proces využívá k tomu [šablony](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) vložení funkce služby Azure Notification Hubs, která umožňuje různé platformy. Různé klienty jsou registrovány pro nabízená oznámení pomocí šablony a můžete získat jednu univerzální nabízených oznámení pro všechny klientské platformy.

Vyberte jednu z následujících postupů, které odpovídá typu vašeho projektu back-end&mdash;buď [.NET back-endu](#dotnet) nebo [back-end Node.js](#nodejs).

### <a name="dotnet"></a>Projekt back-end .NET

1. V sadě Visual Studio klikněte pravým tlačítkem na projekt serveru. Potom vyberte **spravovat balíčky NuGet**. Vyhledejte `Microsoft.Azure.NotificationHubs`a pak vyberte **nainstalovat**. Tímto postupem se nainstalují v knihovně Notification Hubs k odesílání oznámení z back-endu.
2. V projektu serveru, otevřete **řadiče** > **TodoItemController.cs**. Pak přidejte následující příkazy using:

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

    Tento proces odešle šablony oznámení, který obsahuje položky. Text při vložení nové položky.

4. Znovu publikujte projekt serveru.

### <a name="nodejs"></a>Projekt back-end Node.js

1. Pokud jste tak již neučinili, [stáhnout projekt rychlý start back-end](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), nebo použijte jiný [online editoru na webu Azure Portal](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Nahraďte existující kód ve třídě todoitem.js následujícím kódem:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see http://aka.ms/nodejshubs.
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

    Tento proces odešle oznámení šablonu, která obsahuje item.text při vložení nové položky.

3. Při úpravách souboru na místním počítači, znovu publikujte projekt serveru.
