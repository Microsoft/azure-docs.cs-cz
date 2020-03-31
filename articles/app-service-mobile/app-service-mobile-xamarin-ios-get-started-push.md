---
title: Přidání nabízených oznámení do aplikace Xamarin.iOS
description: Přečtěte si, jak pomocí služby Azure App Service odesílat nabízená oznámení do aplikace Xamarin.iOS.
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f9c70491d06f61931ebabda859ff3a86ed035b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249279"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Přidání nabízených oznámení do aplikace Xamarin.iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu [rychlého startu Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) tak, aby nabízené oznámení je odeslána do zařízení při každém vložení záznamu.

Pokud nepoužíváte stažený projekt serveru rychlého startu, budete potřebovat balíček rozšíření nabízených oznámení. Další informace [najdete v tématu Práce s back-endovým serverem SDK .NET pro Mobilní aplikace Azure.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="prerequisites"></a>Požadavky

* Dokončete kurz [rychlého startu Xamarin.iOS.](app-service-mobile-xamarin-ios-get-started.md)
* Fyzické iOS zařízení. Nabízená oznámení nejsou podporovány simulátoru iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrace aplikace pro nabízená oznámení na vývojářském portálu Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Konfigurace mobilní aplikace pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizace projektu serveru pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Konfigurace projektu Xamarin.iOS

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Přidání nabízených oznámení do aplikace

1. V **QSTodoService**přidejte následující vlastnost, aby **AppDelegate** mohl získat mobilního klienta:

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Přidejte `using` následující příkaz do horní části **souboru AppDelegate.cs.**

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. V **aplikaci AppDelegate**přepište událost **FinishedLaunching:**

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. Ve stejném souboru přepište `RegisteredForRemoteNotifications` událost. V tomto kódu se registrujete pro jednoduché oznámení šablony, které bude odesláno napříč všemi podporovanými platformami serverem.

    Další informace o šablonách pomocí center oznámení naleznete v [tématu Templates](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Potom přepsat **DidReceivedRemoteNotification** událost:

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

Vaše aplikace je teď aktualizována, aby podporovala nabízená oznámení.

## <a name="test-push-notifications-in-your-app"></a><a name="test"></a>Testování nabízených oznámení v aplikaci

1. Stisknutím tlačítka **Spustit** sestavíte projekt a spustíte aplikaci v zařízení podporujícím iOS a pak klikněte na **OK,** abyste přijali nabízená oznámení.

   > [!NOTE]
   > Z aplikace musíte explicitně přijímat nabízená oznámení. K tomuto požadavku dochází pouze při prvním spuštění aplikace.

2. V aplikaci zadejte úkol a klikněte**+** na ikonu plus ( ).
3. Ověřte, zda je přijato oznámení, a klepnutím na tlačítko **OK** oznámení zavřete.
4. Opakujte krok 2 a okamžitě zavřete aplikaci a ověřte, zda se zobrazí oznámení.

Úspěšně jste dokončili tento kurz.
