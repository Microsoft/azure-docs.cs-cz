---
title: Posílání nabízených oznámení do aplikací Windows Phone pomocí Azure Notification Hubs | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace Windows Phone 8 nebo Windows Phone 8.1 Silverlight.
services: notification-hubs
documentationcenter: windows
keywords: nabízené oznámení,nabízená oznámení,nabízení windows phone
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e91d250b8cc9b80f2c97910c7fa972af32fa9104
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88998335"
---
# <a name="tutorial-send-push-notifications-to-windows-phone-apps-using-notification-hubs"></a>Kurz: odesílání nabízených oznámení do Windows Phone aplikací pomocí Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

V tomto kurzu se dozvíte, jak ze služby Azure Notification Hubs odesílat nabízená oznámení aplikacím pro Windows Phone 8 nebo Windows Phone 8.1 Silverlight. Pokud jsou vaše aplikace určeny pro Windows Phone 8.1 (ne Silverlight), přečtěte si v tomto kurzu informace k verzi [Univerzální platforma Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

V tomto kurzu vytvoříte prázdnou aplikaci pro Windows Phone 8, která obdrží nabízená oznámení pomocí služby nabízených oznámení Microsoft (MPNS). Po vytvoření aplikace můžete centrum oznámení používat k vysílání nabízených oznámení všem zařízením, na kterých běží vaše aplikace.

> [!NOTE]
> Sada SDK centra oznámení Windows Phone nepodporuje použití služby nabízených oznámení Windows (WNS) s aplikacemi pro Windows Phone 8.1 Silverlight. Chcete-li používat WNS (namísto MPNS) s aplikacemi pro Windows Phone 8.1 Silverlight, postupujte podle [kurzu Centra oznámení – Windows Phone Silverlight], který používá rozhraní REST API.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoříte centrum oznámení.
> * Vytvoření aplikace pro Windows Phone
> * Odeslání zkušebního oznámení

## <a name="prerequisites"></a>Předpoklady

* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Visual Studio 2015 Express s komponentami pro vývoj mobilních aplikací](https://www.visualstudio.com/vs/older-downloads/)

Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy Notification Hubs pro aplikace Windows Phone 8.

## <a name="create-your-notification-hub"></a>Vytvoření centra oznámení

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-windows-phone-mpns-settings"></a>Konfigurace nastavení Windows Phone (MPNS)

1. V **NASTAVENÍ OZNÁMENÍ** vyberte **Windows Phone (MPNS)**.
2. Vyberte, že chcete **povolit ověřování nabízených oznámení**.
3. Na panelu nástrojů vyberte **Uložit**.

    ![Azure Portal – Povolit neověřená nabízená oznámení](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

    Centrum se teď vytvoří a nakonfiguruje pro odeslání neověřeného oznámení pro Windows Phone.

    > [!NOTE]
    > Tento kurz využívá MPNS v neověřeném režimu. Neověřený režim MPNS přichází s omezeními na oznámení, která můžete odeslat na každý kanál. Centra oznámení podporují [ověřený režim MPNS](/previous-versions/windows/apps/ff941099(v=vs.105)) povolením odeslání vašeho certifikátu.

## <a name="create-a-windows-phone-application"></a>Vytvoření aplikace pro Windows Phone

V této části vytvoříte aplikaci pro Windows Phone, která k registraci používá centrum oznámení.

1. V sadě Visual Studio vytvořte novou aplikaci pro Windows Phone 8.

    ![Visual Studio – Nový projekt – aplikace Windows Phone][13]

    Ve Visual Studio 2013 Update 2 nebo novější verzi místo toho vytvořte aplikaci Windows Phone Silverlight.

    ![Visual Studio – Nový projekt – Prázdná aplikace – Windows Phone Silverlight][11]
2. Ve Visual Studiu klikněte pravým tlačítkem na řešení a potom na **Spravovat balíčky NuGet**.
3. Vyhledejte `WindowsAzure.Messaging.Managed` a klikněte na tlačítko **Instalovat** a pak přijměte podmínky použití.

    ![Visual Studio – Správce balíčků NuGet][20]
4. Otevřete soubor App.xaml.cs a přidejte následující příkazy `using`:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    ```

5. Přidejte následující kód na začátek `Application_Launching` metody v `App.xaml.cs` :

    ```csharp
    private void Application_Launching(object sender, LaunchingEventArgs e)
    {

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
    }
    ```

   > [!NOTE]
   > Hodnota `MyPushChannel` je index, který se používá k vyhledání existujícího kanálu v kolekci [HttpNotificationChannel](/previous-versions/ff402781(v=vs.110)) . Pokud zde není k dispozici, vytvořte novou položku s tímto názvem.

    Vložte název vašeho centra a připojovací řetězec `DefaultListenSharedAccessSignature` s názvem, který jste si poznamenali v předchozí části.
    Tento kód načte identifikátor URI kanálu pro aplikaci z MPNS a pak zaregistruje tento kanál URI pomocí centra oznámení. Také zaručuje, že kanál URI je registrován v centru oznámení pokaždé, když je aplikace spuštěna.

   > [!NOTE]
   > V tomto kurzu se odešle informační zpráva do zařízení. Když odešlete oznámení na dlaždici, musíte místo toho zavolat `BindToShellTile` metodu na kanál. Chcete-li podporovat informační zprávy a oznámení dlaždic, zavolejte jak `BindToShellTile` a  `BindToShellToast` .

6. V Průzkumníku řešení rozbalte **Vlastnosti**, otevřete soubor `WMAppManifest.xml`, klikněte na kartu **Možnosti** a ujistěte se, že je zaškrtnuta schopnost **ID_CAP_PUSH_NOTIFICATION**. Vaše aplikace teď přijímá nabízená oznámení.

    ![Visual Studio – možnosti aplikace pro Windows Phone][14]
7. Stiskněte klávesu `F5` a spusťte aplikaci. V aplikaci se zobrazí zpráva registrace.
8. Zavřete aplikaci nebo přepněte na domovskou stránku.

   > [!NOTE]
   > Pro příjem nabízených oznámení nesmí být aplikace spuštěná v popředí.

## <a name="test-send-a-notification"></a>Odeslání zkušebního oznámení

1. Na webu Azure Portal přepněte na kartu Přehled.
2. Vyberte **Poslat na zkoušku**.

    ![Tlačítko Poslat na zkoušku](./media/notification-hubs-windows-phone-get-started/test-send-button.png)
3. V okně **Poslat na zkoušku** proveďte následující kroky:

    1. V položce **Platformy** vyberte **Windows Phone**.
    2. V položce **Typ oznámení** vyberte **Informační zpráva**.
    3. Vybrat **Odeslat**
    4. **Výsledek** si můžete prohlédnout v seznamu dole v okně.

        ![Okno Poslat na zkoušku](./media/notification-hubs-windows-phone-get-started/test-send-window.png)
4. Potvrďte, že oznámení zobrazilo v emulátoru Windows Phone nebo na zařízení Windows Phone.

    ![Oznámení na zařízení Windows Phone](./media/notification-hubs-windows-phone-get-started/notification-on-windows-phone.png)

## <a name="next-steps"></a>Další kroky

V tomto jednoduchém příkladu jste vysílali nabízená oznámení pro všechna vaše zařízení Windows Phone 8. Pokud se chcete naučit zasílat nabízená oznámení určitým zařízením, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Nabízená oznámení odesílaná konkrétním zařízením](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)

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
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[MPNS authenticated mode]: /previous-versions/windows/apps/ff941099(v=vs.105)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: /previous-versions/windows/apps/jj662938(v=vs.105)
[tile catalog]: /previous-versions/windows/apps/hh202948(v=vs.105)
[kurzu Centra oznámení – Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari
