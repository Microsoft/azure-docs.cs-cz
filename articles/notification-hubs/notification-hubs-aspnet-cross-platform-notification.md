---
title: Odesílání oznámení pro různé platformy uživatelům pomocí Azure Notification Hubs (ASP.NET)
description: Naučte se používat šablony Notification Hubs k posílání, v jediném požadavku, nezávislá oznámení platformy, které cílí na všechny platformy.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: cea0d63c20af781fcfc6ba5d7c06061b12992702
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212029"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Odesílání oznámení pro různé platformy uživatelům pomocí Notification Hubs

V předchozím kurzu informujte [Informování uživatelů pomocí Notification Hubs], zjistili jste, jak nabízet oznámení pro všechna zařízení, která jsou zaregistrovaná pro konkrétního ověřeného uživatele. V tomto kurzu bylo pro odeslání oznámení na každou z podporovaných klientských platforem vyžadováno více požadavků. Azure Notification Hubs podporuje šablony, pomocí kterých můžete určit, jak konkrétní zařízení chce dostávat oznámení. Tato metoda zjednodušuje odesílání oznámení pro různé platformy.

V tomto článku se dozvíte, jak využít výhod šablon k odeslání v rámci jediné žádosti nezávislá oznámení platformy, které cílí na všechny platformy. Podrobnější informace o šablonách najdete v tématu [Přehled Azure Notification Hubs][Templates].

> [!IMPORTANT]
> Windows Phone projekty 8,1 a starší nejsou podporovány v aplikaci Visual Studio 2017. Další informace najdete v tématu [Cílení na platformy a kompatibilita v sadě Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Pomocí Notification Hubs může zařízení registrovat více šablon se stejnou značkou. V tomto případě příchozí zpráva, která cílí na značku, má za následek doručení více oznámení do zařízení, jednu pro každou šablonu. Tento proces umožňuje zobrazit stejnou zprávu ve více vizuálních oznámeních, jako je například označení jako označení a informační zpráva v aplikaci pro Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Odesílání oznámení pro různé platformy pomocí šablon

Pokud chcete odesílat oznámení pro různé platformy pomocí šablon, udělejte toto:

1. V Průzkumník řešení v aplikaci Visual Studio rozbalte složku **řadiče** a pak otevřete soubor RegisterController.cs.

2. Vyhledejte blok kódu v `Put` metodě, která vytvoří novou registraci, a poté `switch` nahraďte obsah následujícím kódem:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Tento kód volá metodu specifickou pro platformu pro vytvoření registrace šablony namísto nativní registrace. Vzhledem k tomu, že registrace šablon jsou odvozeny z nativních registrací, nemusíte měnit existující registrace.

3. V kontroleru nahraďte `sendNotification`metodunásledujícímkódem: `Notifications`

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Tento kód pošle oznámení na všechny platformy současně, a to bez nutnosti zadat nativní datovou část. Notification Hubs sestaví a doručuje správnou datovou část na každé zařízení s poskytnutou hodnotou *značky* , jak je uvedeno v zaregistrovaných šablonách.

4. Publikujte projekt back-endu WebApi znovu.

5. Znovu spusťte klientskou aplikaci a ověřte, zda byla registrace úspěšná.

6. Volitelné Nasaďte klientskou aplikaci do druhého zařízení a pak aplikaci spusťte.
    Na každém zařízení se zobrazí oznámení.

## <a name="next-steps"></a>Další kroky

Teď, když jste dokončili tento kurz, získáte další informace o Notification Hubs a šablonách v těchto tématech:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Přehled služby Azure Notification Hubs][Templates]: Obsahuje podrobnější informace o šablonách.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Informování uživatelů pomocí Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
