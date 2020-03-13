---
title: Přidání nabízených oznámení do aplikace Xamarin. iOS
description: Naučte se používat Azure App Service k odesílání nabízených oznámení do vaší aplikace Xamarin. iOS.
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f9c70491d06f61931ebabda859ff3a86ed035b44
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249279"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Přidání nabízených oznámení do aplikace Xamarin. iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu [Xamarin. iOS s rychlým startem](app-service-mobile-xamarin-ios-get-started.md) , aby bylo při každém vložení záznamu nabízené oznámení odesíláno do zařízení.

Pokud nepoužíváte stažený projekt serveru pro rychlé zahájení, budete potřebovat balíček rozšíření nabízených oznámení. Další informace najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

## <a name="prerequisites"></a>Požadavky

* Dokončete kurz [rychlý Start pro Xamarin. iOS](app-service-mobile-xamarin-ios-get-started.md) .
* Fyzické zařízení s iOS. V simulátoru iOS se nabízená oznámení nepodporují.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrace aplikace pro nabízená oznámení na portálu pro vývojáře od společnosti Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Konfigurace mobilní aplikace pro posílání nabízených oznámení

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizace projektového serveru pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Konfigurace projektu Xamarin. iOS

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Přidání nabízených oznámení do aplikace

1. Do **QSTodoService**přidejte následující vlastnost, aby **AppDelegate** mohl získat mobilního klienta:

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

2. Do horní části souboru **AppDelegate.cs** přidejte následující příkaz `using`.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. V **AppDelegate**Přepište událost **FinishedLaunching** :

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

4. Ve stejném souboru Přepište událost `RegisteredForRemoteNotifications`. V tomto kódu se chystáte k oznámení jednoduché šablony, která bude odeslána na všech podporovaných platformách serverem.

    Další informace o šablonách s Notification Hubs najdete v tématu [šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

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

5. Potom přepsat událost **DidReceivedRemoteNotification** :

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

Vaše aplikace se teď aktualizovala tak, aby podporovala nabízená oznámení.

## <a name="test"></a>Testování nabízených oznámení ve vaší aplikaci

1. Stisknutím tlačítka **Spustit** Sestavte projekt a spusťte aplikaci na zařízení s technologií iOS a potom kliknutím na **OK** potvrďte nabízená oznámení.

   > [!NOTE]
   > Nabízená oznámení musíte explicitně přijmout z vaší aplikace. Tato žádost nastane jenom při prvním spuštění aplikace.

2. V aplikaci zadejte úlohu a potom klikněte na ikonu Plus ( **+** ).
3. Ověřte, zda bylo přijato oznámení, a pak kliknutím na tlačítko **OK** zavřete oznámení.
4. Opakujte krok 2 a hned aplikaci zavřete a pak ověřte, že se zobrazilo oznámení.

Úspěšně jste dokončili tento kurz.
