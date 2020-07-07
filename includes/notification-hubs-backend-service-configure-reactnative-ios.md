---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: e67f53e26f2081e1aa12f7c0e6a219c240f5027a
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060484"
---
### <a name="configure-required-ios-packages"></a>Konfigurovat požadované balíčky iOS

Balíček je [automaticky propojený](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) při sestavování aplikace. Stačí udělat instalaci nativních lusků:

```bash
npx pod-install
```

### <a name="configure-infoplist-and-entitlementsplist"></a>Konfigurace info. plist a Nároks. plist

1. Do složky "PushDemo/iOS" a otevřete pracovní prostor "PushDemo. xcworkspace", vyberte horní projekt "PushDemo" a vyberte kartu možnosti podepisování &.

1. Aktualizujte identifikátor sady tak, aby odpovídal hodnotě použité v zřizovacím profilu.

1. Přidejte dvě nové funkce pomocí tlačítka-"+":

    - Funkce režimu pozadí a vytaktování vzdálených oznámení.
    - Možnost nabízených oznámení

### <a name="handle-push-notifications-for-ios"></a>Zpracování nabízených oznámení pro iOS

1. Otevřete AppDelegate. h a přidejte následující import:

    ```objective-c
    #import <UserNotifications/UNUserNotificationCenter.h>
    ```

1. Aktualizujte seznam protokolů, které podporuje "AppDelegate", přidáním `UNUserNotificationCenterDelegate` :

    ```objective-c
    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate>
    ```

1. Otevřete "AppDelegate. m" a nakonfigurujte všechna požadovaná zpětná volání iOS:

    ```objective-c
    #import <UserNotifications/UserNotifications.h>
    #import <RNCPushNotificationIOS.h>

    ...

    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }

    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }

    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }

    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }

    // IOS 10+ Required for localNotification event
    - (void)userNotificationCenter:(UNUserNotificationCenter *)center
    didReceiveNotificationResponse:(UNNotificationResponse *)response
             withCompletionHandler:(void (^)(void))completionHandler
    {
      [RNCPushNotificationIOS didReceiveNotificationResponse:response];
      completionHandler();
    }

    // IOS 4-10 Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }
    ```
