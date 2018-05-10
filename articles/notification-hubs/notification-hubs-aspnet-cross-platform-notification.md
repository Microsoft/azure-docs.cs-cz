---
title: Odesílat oznámení napříč platformami uživatelům s Azure Notification Hubs (ASP.NET)
description: Zjistěte, jak používat šablony Notification Hubs k odesílání v jedné žádosti, bez ohledu na platformu oznámení, že cílem všechny platformy.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 95793aac3c25563e3af39f3c47cebdd06e25e35f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Odesílat oznámení napříč platformami uživatelům s centry oznámení
V předchozích kurzu [upozorněte uživatele s Notification Hubs], jste se dozvěděli, jak nabízená oznámení pro všechna zařízení, které jsou registrovány pro konkrétního ověřeného uživatele. V tomto kurzu bylo potřeba víc požadavků na odeslání oznámení do každé podporované klientské platformy. Centra oznámení Azure podporuje šablony, pomocí kterých můžete zadat, jak chce dostávat oznámení konkrétní zařízení. Tato metoda zjednodušuje odesílání oznámení napříč platformami. 

Tento článek ukazuje, jak chcete využít výhod šablony odeslat v jedné žádosti, bez ohledu na platformu oznámení, že cílem všechny platformy. Podrobnější informace o šablonách najdete v článku [přehledu této služby Azure][Templates].

> [!IMPORTANT]
> Visual Studio 2017 nepodporuje projekty Windows Phone 8.1 a starší. Další informace najdete v tématu [Cílení na platformy a kompatibilita v sadě Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> S Notification Hubs můžete zaregistrovat zařízení několik šablon se stejnou značkou. V takovém případě příchozí zprávy zacílený výsledky značky ve více oznámení doručit do zařízení, jeden pro každé šablony. Tento proces vám umožňuje zobrazit stejná zpráva v několika visual oznámení, například jako oznámení "BADGE" i jako informační zpráva v aplikaci Windows Store.
> 
> 

Odesílat oznámení napříč platformami pomocí šablon, postupujte takto:

1. V Průzkumníku řešení v sadě Visual Studio, rozbalte **řadiče** složku a pak otevřete soubor RegisterController.cs.

2. Vyhledejte blok kódu **Put** metodu, která vytvoří novou registraci a potom můžete nahradit `switch` obsahu s následujícím kódem:
   
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
   
    Tento kód volá metodu specifické pro platformu pro vytvoření šablony registrace místo nativní registrace. Vzhledem k tomu, že šablona registrace odvozena z nativní registrací, nemusíte upravit existující registrace.

3. V **oznámení** řadiče, nahraďte **sendNotification** metoda následujícím kódem:
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Tento kód odešle oznámení na všechny platformy ve stejnou dobu, bez dochází k určení nativní datové části. Notification Hubs sestavení a doručí správné datové části pro každé zařízení poskytnutým *značky* hodnoty zadané v registrovaných šablony.

4. Znovu publikujte projektu WebApi back-end.

5. Znovu spustit klientskou aplikaci a potom ověřit, že registrace proběhla úspěšně.

6. (Volitelné) Nasazení aplikace klienta do druhé zařízení a pak spusťte aplikaci.
    Oznámení se zobrazí na každé zařízení.

## <a name="next-steps"></a>Další postup
Teď, když jste dokončili tento kurz, získáte další informace o Notification Hubs a šablon v těchto tématech:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Přehled služby Azure Notification Hubs][Templates]: obsahuje podrobnější informace o šablonách.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[upozorněte uživatele s Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
