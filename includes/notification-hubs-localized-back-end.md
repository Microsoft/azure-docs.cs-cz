---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 520a0b4ec42b9a32fbd30c28c7ce311b5445f23d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "74260694"
---
Když odesíláte oznámení šablon, stačí zadat sadu vlastností. V tomto scénáři sada vlastností obsahuje lokalizovanou verzi aktuálních zpráv.

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

### <a name="send-notifications-using-a-c-console-app"></a>Odesílání oznámení pomocí konzolové aplikace v jazyce C#

V této části se dozvíte, jak odesílat oznámení pomocí konzolové aplikace. Kód vysílá oznámení do zařízení se systémem Windows Store i iOS. Změňte metodu `SendTemplateNotificationAsync` konzolové aplikace vytvořené dříve přidáním následujícího kódu:

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Apple requires the apns-push-type header for all requests
    var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Metoda SendTemplateNotificationAsync zajišťuje lokalizované části zpráv pro **všechna** vaše zařízení bez ohledu na platformu. Vaše centrum oznámení sestaví a zajišťuje správnou nativní datovou část pro všechna zařízení, která se přihlásila ke konkrétní značce.

### <a name="sending-notification-with-mobile-services"></a>Odesílání oznámení pomocí Mobile Services

V Plánovači Mobile Services použijte následující skript:

```csharp
var azure = require('azure');
var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
var notification = {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin", "World News in Mandarin!"
}
notificationHubService.send('World', notification, function(error) {
    if (!error) {
        console.warn("Notification successful");
    }
});
```
