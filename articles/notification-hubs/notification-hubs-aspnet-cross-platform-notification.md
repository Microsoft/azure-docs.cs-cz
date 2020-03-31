---
title: Odesílání oznámení napříč platformami uživatelům pomocí Azure Notification Hubs (ASP.NET)
description: Naučte se, jak pomocí šablon Centra oznámení odesílat v jedné žádosti oznámení bez ohledu na platformu, které cílí na všechny platformy.
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
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: fc3cec348517244c8a7f54d2d3d17298c58e4262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127048"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Odesílání oznámení napříč platformami pomocí Azure Notification Hubs

Tento kurz vychází z předchozího kurzu [Odesílání oznámení konkrétním uživatelům pomocí Centra oznámení Azure]. Tento kurz popisuje, jak nabízená oznámení do všech zařízení, která jsou registrována na konkrétníověřeného uživatele. Tento přístup vyžaduje více požadavků na odeslání oznámení na každou podporovanou klientskou platformu. Azure Notification Hubs podporuje šablony, pomocí kterých můžete určit, jak konkrétní zařízení chce přijímat oznámení. Tato metoda zjednodušuje odesílání oznámení mezi platformami.

Tento článek ukazuje, jak využít výhod šablon odeslat oznámení, které se zaměřuje na všechny platformy. Tento článek používá jeden požadavek k odeslání neutrální oznámení platformy. Podrobnější informace o šablonách naleznete v tématu [Přehled center oznámení][Templates].

> [!IMPORTANT]
> Projekty Windows Phone 8.1 a starší nejsou v Sadě Visual Studio 2019 podporované. Další informace najdete v [tématu Visual Studio 2019 Cílení na platformu a kompatibilitu](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Pomocí centra oznámení může zařízení zaregistrovat více šablon pomocí stejné značky. V tomto případě příchozí zpráva, která cílí na značku, má za následek doručení více oznámení do zařízení, jednu pro každou šablonu. Tento proces umožňuje zobrazit stejnou zprávu ve více vizuálních oznámeních, například jako informační symbol a jako informační zpráva v aplikaci pro Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Odesílání oznámení pro více platforem pomocí šablon

Tato část používá ukázkový kód, který jste vytvořili v odesílání oznámení konkrétním uživatelům pomocí kurzu [Centra oznámení Azure.] Ukázku si můžete stáhnout z [GitHubu](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Chcete-li odesílat oznámení pro různé platformy pomocí šablon, postupujte takto:

1. V sadě Visual Studio v **Průzkumníkovi řešení**rozbalte složku **Řadiče** a otevřete *soubor RegisterController.cs.*

1. Vyhledejte blok kódu `Put` v metodě, která vytvoří novou `switch` registraci, a nahraďte obsah následujícím kódem:

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

    Tento kód volá metodu specifickou pro platformu k vytvoření registrace šablony namísto nativní registrace. Vzhledem k tomu, že registrace šablon jsou odvozeny z nativních registrací, není nutné stávající registrace měnit.

1. V **Průzkumníku řešení**otevřete ve složce **Řadiče** *soubor NotificationsController.cs.* Nahraďte metodu `Post` následujícím kódem:

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

    Tento kód odešle oznámení na všechny platformy ve stejnou dobu. Nezadáte nativní datovou část. Centra oznámení vytvoří a doručuje správnou datovou část do každého zařízení s zadanou hodnotou značky, jak je uvedeno v registrovaných šablonách.

1. Znovu publikujte projekt webového rozhraní API.

1. Spusťte klientskou aplikaci znovu a ověřte, zda byla registrace úspěšná.

1. Volitelně nasadit klientskou aplikaci do druhého zařízení a pak spustit aplikaci. Na každém zařízení se zobrazí oznámení.

## <a name="next-steps"></a>Další kroky

Teď, když jste dokončili tento kurz, se dozvíte více o centerch oznámení a šablonách v těchto článcích:

* Jiný scénář pro použití šablon najdete v tématu [Nabízená oznámení pro konkrétní zařízení se systémem Windows, kde][Use Notification Hubs to send breaking news] se používá kurz pro aplikace na platformě Windows.
* Podrobnější informace o šablonách naleznete v tématu [Přehled center oznámení][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Odesílání oznámení konkrétním uživatelům pomocí Azure Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
