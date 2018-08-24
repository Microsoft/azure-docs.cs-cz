---
title: Přidání nabízených oznámení do aplikace Xamarin.iOS pomocí služby Azure App Service
description: Další informace o použití služby Azure App Service k odesílání nabízených oznámení do aplikace Xamarin.iOS
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 32a8c36d223e2b0c12f5d82ec748af66ae841b01
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819018"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Přidání nabízených oznámení do aplikace Xamarin.iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení [Xamarin.iOS úvodní](app-service-mobile-xamarin-ios-get-started.md) projekt tak, aby na zařízení přijde nabízené oznámení pokaždé, když se vložení záznamu.

Pokud použijete serverový projekt stažené rychlý start, budete potřebovat balíček rozšíření nabízené oznámení. Zobrazit [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) Další informace.

## <a name="prerequisites"></a>Požadavky

* Dokončení [rychlý start Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) kurzu.
* Fyzickém zařízení iOS. Nabízená oznámení nepodporuje simulátoru iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrace aplikace pro nabízená oznámení na portálu pro vývojáře Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Konfigurace mobilní aplikace k odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizovat projekt serveru k odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Konfigurace projektu Xamarin.iOS

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Přidání nabízených oznámení do aplikace

1. V **QSTodoService**, přidejte následující vlastnost tak, aby **AppDelegate** můžete získat tak mobilního klienta:

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

2. Přidejte následující `using` příkaz do horní části **AppDelegate.cs** souboru.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. V **AppDelegate**, přepsat **FinishedLaunching** události:

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

4. Ve stejném souboru přepsat `RegisteredForRemoteNotifications` událostí. V tomto kódu při registraci pro jednoduchá Šablona oznámení, které se odešlou server na všech podporovaných platformách.

    Další informace o šablonách pomocí Notification Hubs najdete v tématu [šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

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

5. Nakonec přepsání **DidReceivedRemoteNotification** události:

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

Vaše aplikace je nyní aktualizována o podporu nabízených oznámení.

## <a name="test"></a>Nabízená oznámení ve vaší aplikaci

1. Stisknutím klávesy **spustit** tlačítko se projekt sestavil a aplikace se spustila v podporuje zařízení s Iosem a potom klikněte na **OK** přijímat nabízená oznámení.

   > [!NOTE]
   > Je nutné explicitně přijmout nabízená oznámení z vaší aplikace. Tento požadavek dochází pouze při prvním spuštění aplikace.

2. V aplikaci, zadejte úlohu a potom klikněte na znaménko plus (**+**) ikonu.
3. Ověřte, že přijetí oznámení a pak klikněte na **OK** chcete oznámení zavřít.
4. Opakujte krok 2 a okamžitě zavřete aplikaci a potom ověřte, že se zobrazí oznámení.

Úspěšně jste dokončili tento kurz.
