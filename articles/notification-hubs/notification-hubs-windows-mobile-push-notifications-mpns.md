---
title: "Začínáme se službou Azure Notification Hubs pro aplikace pro Windows Phone | Dokumentace Microsoftu"
description: "V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace Windows Phone 8 nebo Windows Phone 8.1 Silverlight."
services: notification-hubs
documentationcenter: windows
keywords: "nabízené oznámení,nabízená oznámení,nabízení windows phone"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/06/2018
ms.author: jawh
ms.openlocfilehash: 38d60001293a3bae6eb9f90179abb8af7815fbe7
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-notification-hubs-for-windows-phone-apps"></a>Začínáme se službou Azure Notification Hubs pro aplikace pro Windows Phone
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Přehled
> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).
> 
> 

V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace Windows Phone 8 nebo Windows Phone 8.1 Silverlight. Pokud cílíte na Windows Phone 8.1 (ne Silverlight), přečtěte si informace k verzi [Univerzální pro Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
V tomto kurzu vytvoříte prázdnou aplikaci pro Windows Phone 8, která obdrží nabízená oznámení pomocí služby nabízených oznámení Microsoft (MPNS). Jakmile budete hotovi, budete moci používat vaše centra oznámení k vysílání nabízených oznámení pro všechna zařízení používající vaši aplikaci.

> [!NOTE]
> Sada SDK centra oznámení Windows Phone nepodporuje použití služby nabízených oznámení Windows (WNS) s aplikacemi pro Windows Phone 8.1 Silverlight. Chcete-li používat WNS (namísto MPNS) s aplikacemi pro Windows Phone 8.1 Silverlight, postupujte podle [kurzu Centra oznámení – Windows Phone Silverlight], který používá rozhraní REST API.
> 
> 

Tento kurz představuje scénář jednoduchého vysílání přes centra oznámení.

## <a name="prerequisites"></a>Požadavky
V tomto kurzu budete potřebovat následující:

* [Visual Studio 2012 Express pro Windows Phone] nebo novější verzi

Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy Notification Hubs pro aplikace Windows Phone 8.

## <a name="create-your-notification-hub"></a>Vytvoření centra oznámení
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>V části <b>Notification Services</b> vyberte <b>Windows Phone (MPNS)</b> a pak zaškrtněte políčko <b>Povolit neověřená nabízená oznámení</b>.</p>
</li>
</ol>

&emsp;&emsp;![Azure Portal – Povolit neověřená nabízená oznámení](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

Centrum se teď vytvoří a nakonfiguruje pro odeslání neověřeného oznámení pro Windows Phone.

> [!NOTE]
> Tento kurz využívá MPNS v neověřeném režimu. Neověřený režim MPNS přichází s omezeními na oznámení, která můžete odeslat na každý kanál. Centra oznámení podporují [ověřený režim MPNS](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) povolením odeslání vašeho certifikátu.
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>Připojování aplikace k centru oznámení
1. V sadě Visual Studio vytvořte novou aplikaci pro Windows Phone 8.
   
    ![Visual Studio – Nový projekt – aplikace Windows Phone][13]
   
    Ve Visual Studio 2013 Update 2 nebo novější verzi místo toho vytvořte aplikaci Windows Phone Silverlight.
   
    ![Visual Studio – Nový projekt – Prázdná aplikace – Windows Phone Silverlight][11]
2. Ve Visual Studiu klikněte pravým tlačítkem na řešení a potom na **Spravovat balíčky NuGet**.
   
    Zobrazí se dialogové okno **Správa balíčků NuGet**.
3. Vyhledejte `WindowsAzure.Messaging.Managed` a klikněte na tlačítko **Instalovat** a pak přijměte podmínky použití.
   
    ![Visual Studio – Správce balíčků NuGet][20]
   
    Tento správce stáhne, nainstaluje a přidá odkaz na knihovnu zasílání zpráv Azure pro Windows pomocí <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">balíčku NuGet WindowsAzure.Messaging.Managed</a>.
4. Otevřete soubor App.xaml.cs a přidejte následující příkazy `using`:
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. Přidejte následující kód v horní části metody **Application_Launching** v souboru App.xaml.cs:
   
        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }
   
        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
   
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
   
   > [!NOTE]
   > Hodnota **MyPushChannel** je index, který se použije k vyhledání existujícího kanálu v kolekci [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx). Pokud zde není k dispozici, vytvořte novou položku s tímto názvem.
   > 
   > 
   
    Nezapomeňte vložit název rozbočovače a připojovací řetězec s názvem **DefaultListenSharedAccessSignature**, který jste získali v předchozím oddílu.
    Tento kód načte identifikátor URI kanálu pro aplikaci z MPNS a pak zaregistruje tento kanál URI pomocí centra oznámení. Také zaručuje, že kanál URI je registrován v centru oznámení pokaždé, když je aplikace spuštěna.
   
   > [!NOTE]
   > V tomto kurzu se odešle informační zpráva do zařízení. Při odesílání oznámení dlaždice musíte místo toho volat metodu **BindToShellTile** na kanálu. Pro podporu obou oznámení a oznámení v dlaždici volejte jak metodu **BindToShellTile**, tak i metodu **BindToShellToast**.
   > 
   > 
6. V Průzkumníku řešení rozbalte **Vlastnosti**, otevřete soubor `WMAppManifest.xml`, klikněte na kartu **Možnosti** a ujistěte se, že je zaškrtnuta schopnost **ID_CAP_PUSH_NOTIFICATION**.
   
    ![Visual Studio – možnosti aplikace pro Windows Phone][14]
   
    Tato možnost zajistí, že vaše aplikace může přijímat nabízená oznámení Bez toho se nezdaří jakýkoliv pokus o odeslání nabízených oznámení do aplikace.
7. Stiskněte klávesu `F5` a spusťte aplikaci.
   
    V aplikaci se zobrazí zpráva registrace.
8. Zavřete aplikaci.  
   
   > [!NOTE]
   > Pro příjem nabízených oznámení nesmí být aplikace spuštěná v popředí.
   >

## <a name="next-steps"></a>Další kroky
V tomto jednoduchém příkladu jste vysílali nabízená oznámení pro všechna vaše zařízení Windows Phone 8. 

Chcete-li se zaměřit na konkrétní uživatele, využijte kurz [Použití centra oznámení pro nabízená oznámení uživatelům]. 

Pokud chcete segmentovat uživatele podle zájmových skupin, můžete si přečíst kurz [Používání centra oznámení k odesílání novinek]. 

Další informace o centrech oznámení najdete v [Průvodce centry oznámení].

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express pro Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Průvodce centry oznámení]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Použití centra oznámení pro nabízená oznámení uživatelům]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Používání centra oznámení k odesílání novinek]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[kurzu Centra oznámení – Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

