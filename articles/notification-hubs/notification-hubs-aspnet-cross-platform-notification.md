---
title: Odesílání oznámení pro různé platformy uživatelům pomocí Azure Notification Hubs (ASP.NET)
description: Naučte se používat šablony Notification Hubs k posílání, v jediném požadavku, nezávislá oznámení platformy, které cílí na všechny platformy.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: thsomasu
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 10/02/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f8f9357fff4773467dd93046ece8533514eb444
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92313887"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Odesílání oznámení pro různé platformy pomocí Azure Notification Hubs

Tento kurz sestaví v předchozím kurzu a [pošle oznámení konkrétním uživatelům pomocí Azure Notification Hubs]. V tomto kurzu se dozvíte, jak zasílat oznámení do všech zařízení, která jsou zaregistrovaná konkrétnímu ověřenému uživateli. Tento přístup vyžadoval více požadavků pro odeslání oznámení na každou podporovanou klientskou platformu. Azure Notification Hubs podporuje šablony, pomocí kterých můžete určit, jak konkrétní zařízení chce dostávat oznámení. Tato metoda zjednodušuje odesílání oznámení pro různé platformy.

Tento článek ukazuje, jak využít výhod šablon k odeslání oznámení, které cílí na všechny platformy. V tomto článku se používá jedna žádost o odeslání neutrálního oznámení platformy. Podrobnější informace o šablonách najdete v tématu [přehled Notification Hubs][Templates].

> [!IMPORTANT]
> Windows Phone projekty 8,1 a starší nejsou podporovány v aplikaci Visual Studio 2019. Další informace najdete v tématu [cílení a kompatibilita platformy Visual Studio 2019](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Pomocí Notification Hubs může zařízení registrovat více šablon pomocí stejné značky. V takovém případě příchozí zpráva, která cílí na značku, má za následek doručování více oznámení do zařízení, jednu pro každou šablonu. Tento proces umožňuje zobrazit stejnou zprávu ve více vizuálních oznámeních, jako je například označení jako označení a informační zpráva v aplikaci pro Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Odesílání oznámení pro různé platformy pomocí šablon

V této části se používá vzorový kód, který jste vytvořili v tématu [posílání oznámení pro konkrétní uživatele pomocí kurzu služby Azure Notification Hubs] . [Kompletní ukázku si můžete stáhnout z GitHubu](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Pokud chcete odesílat oznámení pro různé platformy pomocí šablon, udělejte toto:

1. V aplikaci Visual Studio v **Průzkumník řešení**rozbalte složku **řadiče** a pak otevřete soubor *RegisterController.cs* .

1. Vyhledejte blok kódu v `Put` metodě, která vytvoří novou registraci, a poté nahraďte `switch` obsah následujícím kódem:

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

1. V **Průzkumník řešení**ve složce **Controllers** otevřete soubor **NotificationsController.cs** . Nahraďte metodu `Post` následujícím kódem:

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

    Tento kód pošle oznámení na všechny platformy současně. Neurčíte nativní datovou část. Notification Hubs sestaví a doručuje správnou datovou část na každé zařízení s poskytnutou hodnotou značky, jak je uvedeno v zaregistrovaných šablonách.

1. Znovu publikujte projekt webového rozhraní API.

1. Opětovným spuštěním klientské aplikace ověřte, zda byla registrace úspěšná.

1. Volitelně nasaďte klientskou aplikaci do druhého zařízení a pak aplikaci spusťte. Na každém zařízení se zobrazí oznámení.

## <a name="next-steps"></a>Další kroky

Teď, když jste dokončili tento kurz, získáte další informace o Notification Hubs a šablonách v těchto článcích:

* Jiný scénář použití šablon najdete v kurzu [nabízená oznámení na konkrétní zařízení s Windows, která běží na Univerzální platforma Windows aplikacích][Use Notification Hubs to send breaking news] .
* Podrobnější informace o šablonách najdete v tématu [přehled Notification Hubs][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Posílání oznámení konkrétním uživatelům pomocí Azure Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hub How to for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)