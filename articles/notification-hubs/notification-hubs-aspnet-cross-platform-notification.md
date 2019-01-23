---
title: Odesílání oznámení napříč platformami pro uživatele s Azure Notification Hubs (ASP.NET)
description: Zjistěte, jak používat šablony služby Notification Hubs k odesílání, v jedné žádosti více platforem oznámení, který cílí na všech platformách.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 637bae0a3f6bba712662e894b75c8bd663e91b4a
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446627"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Odesílání oznámení napříč platformami pro uživatele s Notification Hubs

V předchozím kurzu [informování uživatelů pomocí Notification Hubs], jste zjistili, jak nabízená oznámení pro všechna zařízení, které jsou registrované na konkrétní ověřeného uživatele. V tomto kurzu byla potřeba více požadavků k odesílání oznámení do každé podporované klientské platformy. Azure Notification Hubs podporuje šablony, pomocí nichž můžete určit, jak chce dostávat oznámení konkrétní zařízení. Tato metoda zjednodušuje posílání oznámení napříč platformami.

Tento článek ukazuje, jak využít výhod šablony k odeslání v jedné žádosti více platforem oznámení, který cílí na všech platformách. Podrobnější informace o šablonách najdete v článku [Přehled služby Azure Notification Hubs][Templates].

> [!IMPORTANT]
> Projekty Windows Phone 8.1 a starší se nepodporují v sadě Visual Studio 2017. Další informace najdete v tématu [Cílení na platformy a kompatibilita v sadě Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> S Notification Hubs můžete zařízení zaregistrovat více šablon se stejnou značkou. V takovém případě příchozí zprávy, která se zaměřuje výsledky značky v několika oznámení doručit do zařízení, jeden pro každou šablonu. Tento proces umožní zobrazit stejné zprávy v několika visual oznámení, jako je například jako Odznáček i jako oznámení s informační zprávou v aplikaci Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Posílání oznámení napříč platformami pomocí šablon

K odesílání oznámení napříč platformami pomocí šablon, postupujte takto:

1. V Průzkumníku řešení v sadě Visual Studio, rozbalte **řadiče** složku a pak otevřete soubor RegisterController.cs.

2. Vyhledejte blok kódu `Put` metodu, která se vytvoří nová registrace a potom nahraďte `switch` obsah následujícím kódem:

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
        case "gcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Tento kód volá metodu specifické pro platformu pro vytvoření šablony registrace místo registraci nativního. Protože registrace šablon jsou odvozeny z nativní registrací, není nutné upravovat existující registrace.

3. V `Notifications` řadič, nahraďte `sendNotification` metodu s následujícím kódem:

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

    Tento kód odešle oznámení pro všechny platformy ve stejnou dobu, aniž by se museli zadávat nativní datové části. Notification Hubs sestavení a zajišťuje správné datové části do všech zařízení za poskytnutý *značka* hodnoty, jak je uvedeno v registrovaných šablony.

4. Opakované publikování projektu back-end WebApi.

5. Znovu spusťte klientskou aplikaci a potom ověřte, že byla registrace úspěšná.

6. (Volitelné) Nasazení aplikace klienta do druhé zařízení a pak spusťte aplikaci.
    Oznámení se zobrazí na každé zařízení.

## <a name="next-steps"></a>Další postup

Teď, když jste dokončili tento kurz, přečtěte si další informace o Notification Hubs a šablon v těchto tématech:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Přehled služby Azure Notification Hubs][Templates]: Obsahuje podrobné informace o šablonách.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Informování uživatelů pomocí Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
