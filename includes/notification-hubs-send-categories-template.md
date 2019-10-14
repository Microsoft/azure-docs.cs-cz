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
ms.openlocfilehash: 299f92484000cb5c59291a5af87f24d89a771fee
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296732"
---
V této části se v konzolové aplikaci .NET odesílají zprávy s upozorněním na označenou šablonu.

1. V aplikaci Visual Studio vytvořte novou aplikaci vizuální C# konzoly:
    1. V nabídce vyberte **soubor** > **Nový** **projekt** > .
    1. V části **vytvořit nový projekt**vyberte C# v seznamu šablon položku **aplikace konzoly (.NET Framework)** a vyberte **Další**.
    1. Zadejte název aplikace.
    1. Pro **řešení**zvolte možnost **Přidat do řešení**a vyberte **vytvořit** a vytvořte projekt.

1. Vyberte **nástroje** > **správce balíčků NuGet** > **Konzola správce balíčků** a pak v okně konzoly spusťte následující příkaz:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Tato akce přidá odkaz na sadu Azure Notification Hubs SDK pomocí balíčku [Microsoft. Azure. NotificationHubs] .

1. Otevřete soubor *program.cs* a přidejte následující příkaz `using`:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. Ve třídě `Program` přidejte následující metodu, nebo ji nahraďte, pokud již existuje:

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

   Tento kód pošle oznámení šablony pro každou šest značek v poli řetězců. Použití značek zajišťuje, aby zařízení přijímala oznámení pouze pro registrované kategorie.

1. V předchozím kódu Nahraďte zástupné symboly `<hub name>` a `<connection string with full access>` s názvem centra oznámení a připojovacím řetězcem pro *DefaultFullSharedAccessSignature* z řídicího panelu centra oznámení.

1. Do metody `Main()` přidejte následující řádky:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Sestavte konzolovou aplikaci.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft. Azure. NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
