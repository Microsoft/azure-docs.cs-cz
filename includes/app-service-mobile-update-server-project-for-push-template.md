---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68857353"
---
V této části aktualizujete kód v existujícím projektu back-end u mobilních aplikací a odešlete nabízené oznámení pokaždé, když je přidána nová položka. Tento proces je založen na funkci [šablony](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) Azure Notification Hubs, která umožňuje nabízená oznámení napříč platformami. Různí klienti jsou registrováni pro nabízená oznámení pomocí šablon a jeden univerzální nabízený tisk se může dostat na všechny klientské platformy.

Zvolte jeden z následujících postupů, které&mdash;odpovídají typu back-endového projektu, buď [back-endu .NET,](#dotnet) nebo [back-endu Node.js](#nodejs).

### <a name="net-back-end-project"></a><a name="dotnet"></a>Back-endový projekt .NET

1. V sadě Visual Studio klikněte pravým tlačítkem myši na projekt serveru. Pak vyberte **Spravovat nugetové balíčky**. Vyhledejte `Microsoft.Azure.NotificationHubs`položku a vyberte **možnost Instalovat**. Tento proces nainstaluje knihovnu Centra oznámení pro odesílání oznámení ze back-endu.
2. V projektu serveru otevřete**TodoItemController.cs** **řadiče** > . Pak přidejte následující pomocí příkazů:

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

    Tento proces odešle oznámení šablony, která obsahuje položku. Text při vložení nové položky.

4. Znovu publikujte projekt serveru.

### <a name="nodejs-back-end-project"></a><a name="nodejs"></a>Back-endový projekt Node.js

1. Nastavte back-endový projekt.
2. Nahraďte existující kód v souboru todoitem.js následujícím kódem:

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

    Tento proces odešle oznámení šablony, které obsahuje item.text při vložení nové položky.

3. Při úpravách souboru v místním počítači znovu publikujte projekt serveru.
