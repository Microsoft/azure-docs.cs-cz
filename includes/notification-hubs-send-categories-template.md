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
ms.openlocfilehash: bfd5d42d83046c9c5b0bc3a78fabec08da5da646
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027321"
---
V této části odešlete nejnovější zprávy jako šablonové oznámení se značkami z konzolové aplikace .NET.

1. Ve Visual Studiu vytvořte novou konzolovou aplikaci Visual C#:
    1. V nabídce vyberte **soubor**  >  **Nový**  >  **projekt**.
    1. V části **vytvořit nový projekt** vyberte **Konzolová aplikace (.NET Framework)** pro C# v seznamu šablon a vyberte **Další**.
    1. Zadejte název aplikace.
    1. Pro **řešení** zvolte možnost **Přidat do řešení** a vyberte **vytvořit** a vytvořte projekt.

1. Vyberte **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků** a pak v okně konzoly spusťte následující příkaz:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Tato akce přidá odkaz na sadu Azure Notification Hubs SDK pomocí balíčku [Microsoft. Azure. NotificationHubs] .

1. Otevřete soubor *program. cs* a přidejte následující `using` příkaz:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. Do třídy `Program` přidejte následující metodu, nebo ji nahraďte, pokud už existuje:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Tento kód odešle šablonové oznámení pro každou z šesti značek v poli řetězců. Použití značek zajišťuje, že zařízení budou přijímat oznámení jenom pro registrované kategorie.

1. V předchozím kódu nahraďte zástupné symboly `<hub name>` a `<connection string with full access>` názvem vašeho centra oznámení a připojovacím řetězcem pro *DefaultFullSharedAccessSignature* z řídicího panelu vašeho centra oznámení.

1. Do `Main()` metody přidejte následující řádky:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Sestavte konzolovou aplikaci.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: /previous-versions/azure/reference/dn223264(v=azure.100)
[Add push notifications for Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft. Azure. NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/