---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 19352df7abff23ed44521a11e7907c84c8c0327f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
V této části odešlete nejnovější zprávy jako šablonové oznámení se značkami z konzolové aplikace .NET. 

1. Ve Visual Studiu vytvořte novou konzolovou aplikaci Visual C#:
   
      ![Odkaz Konzolová aplikace][13]

2. V hlavní nabídce sady Visual Studio vyberte **Nástroje** > **Správce balíčků knihoven** > **Konzola Správce balíčků** a pak v okně konzoly zadejte následující řetězec:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. Stiskněte **Enter**.  
    Tato akce přidá referenci na sadu SDK služby Azure Notification Hubs pomocí [balíčku NuGet Microsoft.Azure.Notification Hubs].

4. Otevřete soubor Program.cs a přidejte následující příkaz `using`:
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. Do třídy `Program` přidejte následující metodu, nebo ji nahraďte, pokud už existuje:
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    Tento kód odešle šablonové oznámení pro každou z šesti značek v poli řetězců. Použití značek zajišťuje, že zařízení budou přijímat oznámení jenom pro registrované kategorie.

5. V předchozím kódu nahraďte zástupné symboly `<hub name>` a `<connection string with full access>` názvem vašeho centra oznámení a připojovacím řetězcem pro *DefaultFullSharedAccessSignature* z řídicího panelu vašeho centra oznámení.

6. Do metody **Main** přidejte následující řádky:
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. Sestavte konzolovou aplikaci.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[balíčku NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
