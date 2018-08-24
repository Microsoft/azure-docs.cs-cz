---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 87514a139c097ebec3804c8c5ff82a1a21ccdd8c
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811575"
---
**Objective-C**:

1. V **QSAppDelegate.m**, importujte sady iOS SDK a **QSTodoService.h**:

    ```objc
    #import <MicrosoftAzureMobile/MicrosoftAzureMobile.h>
    #import "QSTodoService.h"
    ```

2. V `didFinishLaunchingWithOptions` v **QSAppDelegate.m**, vložte následující řádky bezprostředně před `return YES;`:

    ```objc
    UIUserNotificationSettings* notificationSettings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeAlert | UIUserNotificationTypeBadge | UIUserNotificationTypeSound categories:nil];
    [[UIApplication sharedApplication] registerUserNotificationSettings:notificationSettings];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```

3. V **QSAppDelegate.m**, přidejte následující metody obslužné rutiny. Vaše aplikace je nyní aktualizována o podporu nabízených oznámení. 

    ```objc
    // Registration with APNs is successful
    - (void)application:(UIApplication *)application
    didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {

        QSTodoService *todoService = [QSTodoService defaultService];
        MSClient *client = todoService.client;

        [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];
    }

    // Handle any failure to register
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
    (NSError *)error {
        NSLog(@"Failed to register for remote notifications: %@", error);
    }

    // Use userInfo in the payload to display an alert.
    - (void)application:(UIApplication *)application
            didReceiveRemoteNotification:(NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);

        NSDictionary *apsPayload = userInfo[@"aps"];
        NSString *alertString = apsPayload[@"alert"];

        // Create alert with notification content.
        UIAlertController *alertController = [UIAlertController
                                        alertControllerWithTitle:@"Notification"
                                        message:alertString
                                        preferredStyle:UIAlertControllerStyleAlert];

        UIAlertAction *cancelAction = [UIAlertAction
                                        actionWithTitle:NSLocalizedString(@"Cancel", @"Cancel")
                                        style:UIAlertActionStyleCancel
                                        handler:^(UIAlertAction *action)
                                        {
                                            NSLog(@"Cancel");
                                        }];

        UIAlertAction *okAction = [UIAlertAction
                                    actionWithTitle:NSLocalizedString(@"OK", @"OK")
                                    style:UIAlertActionStyleDefault
                                    handler:^(UIAlertAction *action)
                                    {
                                        NSLog(@"OK");
                                    }];

        [alertController addAction:cancelAction];
        [alertController addAction:okAction];

        // Get current view controller.
        UIViewController *currentViewController = [[[[UIApplication sharedApplication] delegate] window] rootViewController];
        while (currentViewController.presentedViewController)
        {
            currentViewController = currentViewController.presentedViewController;
        }

        // Display alert.
        [currentViewController presentViewController:alertController animated:YES completion:nil];

    }
    ```

**Kód SWIFT**:

1. Přidání souboru **ClientManager.swift** s následujícím obsahem. Nahraďte *AppUrl %* adresou URL back-endu mobilní aplikace Azure.

    ```swift
    class ClientManager {
        static let sharedClient = MSClient(applicationURLString: "%AppUrl%")
    }
    ```

2. V **ToDoTableViewController.swift**, nahraďte `let client` řádek, který se inicializuje `MSClient` se tento řádek:

    ```swift
    let client = ClientManager.sharedClient
    ```

3. V **AppDelegate.swift**, nahraďte obsah metody `func application` následujícím způsobem:

    ```swift
    func application(application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        application.registerUserNotificationSettings(
            UIUserNotificationSettings(forTypes: [.Alert, .Badge, .Sound],
                categories: nil))
        application.registerForRemoteNotifications()
        return true
    }
    ```

4. V **AppDelegate.swift**, přidejte následující metody obslužné rutiny. Vaše aplikace je nyní aktualizována o podporu nabízených oznámení.

    ```swift
    func application(application: UIApplication,
        didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData) {
        ClientManager.sharedClient.push?.registerDeviceToken(deviceToken) { error in
            print("Error registering for notifications: ", error?.description)
        }
    }

    func application(application: UIApplication,
        didFailToRegisterForRemoteNotificationsWithError error: NSError) {
        print("Failed to register for remote notifications: ", error.description)
    }

    func application(application: UIApplication,
        didReceiveRemoteNotification userInfo: [NSObject: AnyObject]) {

        print(userInfo)

        let apsNotification = userInfo["aps"] as? NSDictionary
        let apsString       = apsNotification?["alert"] as? String

        let alert = UIAlertController(title: "Alert", message: apsString, preferredStyle: .Alert)
        let okAction = UIAlertAction(title: "OK", style: .Default) { _ in
            print("OK")
        }
        let cancelAction = UIAlertAction(title: "Cancel", style: .Default) { _ in
            print("Cancel")
        }

        alert.addAction(okAction)
        alert.addAction(cancelAction)

        var currentViewController = self.window?.rootViewController
        while currentViewController?.presentedViewController != nil {
            currentViewController = currentViewController?.presentedViewController
        }

        currentViewController?.presentViewController(alert, animated: true) {}

    }
    ```
