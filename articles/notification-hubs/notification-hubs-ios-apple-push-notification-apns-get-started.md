---
title: Odesílání nabízených oznámení do aplikací pro iOS pomocí Center oznámení Azure | Dokumenty společnosti Microsoft
description: V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace iOS.
services: notification-hubs
documentationcenter: ios
keywords: nabízené oznámení;nabízená oznámení;nabízená oznámení ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 032ca8d4ecbcf1fc7f3c22cbe5a0ee934fc5e17c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74407165"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Kurz: Odesílání nabízených oznámení do aplikací pro iOS pomocí Center oznámení Azure

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

V tomto kurzu použijete Centra oznámení Azure k odesílání nabízených oznámení do aplikace iOS. Vytvoříte prázdnou aplikaci pro iOS, která přijímá nabízená oznámení [služby Apple Push Notification (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vygenerujete soubor s žádostí o podepsání certifikátu
> * Požádáte aplikaci o nabízená oznámení
> * Vytvoření zřizovacího profilu pro aplikaci
> * Nakonfigurujete v centru oznámení nabízená oznámení pro iOS
> * Připojíte aplikaci pro iOS ke službě Notification Hubs
> * Odešlete nabízená oznámení
> * Ověříte, že aplikace přijímá oznámení

Kompletní kód pro tento kurz lze nalézt [na GitHubu](https://github.com/Azure/azure-notificationhubs-ios/tree/master/Samples).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Aktivní účet Azure. Pokud nemáte účet, můžete si [vytvořit bezplatný účet Azure](https://azure.microsoft.com/free).
* [Windows Azure Messaging Framework]
* Poslední verze jazyka [Xcode]
* Zařízení podporující iOS verze 10 (nebo novější)
* Členství v [programu pro vývojáře Apple](https://developer.apple.com/programs/).
  
  > [!NOTE]
  > Z důvodu požadavků na konfiguraci pro nabízená oznámení musíte nasadit a otestovat nabízená oznámení na fyzickém zařízení iOS (iPhone nebo iPad) namísto simulátoru iOS.
  
Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy Notification Hubs pro aplikace iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Připojte aplikaci iOS k centru oznámení

1. V Xcode vytvořte nový projekt iOS a vyberte šablonu **Jediné zobrazení aplikace**.

    ![Xcode – jediné zobrazení aplikace][8]

2. Když nastavujete možnosti pro nový projekt, nezapomeňte použít stejný **Název produktu** a **Identifikátor organizace**, který jste použili při nastavení identifikátoru sady na portálu pro vývojáře Apple.

3. V části Project Navigator vyberte název projektu v části **Cíle**a pak vyberte kartu **Možnosti podepisování &.** Ujistěte se, že jste vybrali příslušný **tým** pro svůj účet Apple Developer. XCode by na základě vašeho identifikátoru sady mělo automaticky stáhnout profil zřizování, který jste vytvořili dříve.

    Pokud nevidíte nový profil zřizování, který jste vytvořili v Xcode, pokuste se aktualizovat profily pro podpisové identity. Klikněte na tlačítko **Xcode** na panelu nabídek, klikněte na tlačítko **Předvolby**, klikněte na kartu **Účet**, klikněte na tlačítko **Zobrazit podrobnosti**, klikněte na podpisovou identitu a pak klikněte na tlačítko Aktualizovat v pravém dolním rohu.

    ![Xcode – profil zřizování][9]

4. Na kartě **Možnosti podepisování &** vyberte **+ Schopnosti**.  Povolte povolte **nabízená oznámení.**

    ![Xcode – možnosti nabízení][12]

5. Přidejte moduly Azure Notification Hubs SDK.

   Sadku Azure Notification Hubs SDK můžete integrovat do aplikace pomocí [cocoapodů](https://cocoapods.org) nebo ručním přidáním binárních souborů do projektu.

   - Integrace prostřednictvím kakaopodů

     Přidejte do své `podfile` aplikace následující závislosti, abyste do aplikace zahrnuli sdk centra oznámení Azure.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Spuštěním `pod install` nainstalujte nově definovaný `.xcworkspace`pod a otevřete aplikaci .

     > [!NOTE]
     > Pokud se zobrazí chyba, například **[!] Nelze najít specifikaci pro AzureNotificationHubs-iOS** při spuštění `pod install`, spusťte `pod repo update` získat nejnovější pods z `pod install`úložiště Cocoapods a pak spustit .

   - Integrace přes Krežáme

     Přidejte do své `Cartfile` aplikace následující závislosti, abyste do aplikace zahrnuli sdk centra oznámení Azure.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Dále aktualizujte a sestavte závislosti:

     ```shell
     $ carthage update
     ```

     Další informace o používání Krežalí najdete v [úložišti Carthage GitHub](https://github.com/Carthage/Carthage).

   - Integrace zkopírováním binárních souborů do projektu

     1. Stáhněte si architekturu [Azure Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs-ios/releases) poskytovanou jako soubor zip a rozbalte ho.

     2. V Xcode klikněte pravým tlačítkem na projekt a klikněte na možnost **Přidat soubory do** a přidejte složku **WindowsAzureMessaging.framework** do projektu Xcode. Vyberte **Možnosti**, ujistěte se, že je vybraná možnost **Kopírovat položky v případě potřeby**, a pak klikněte na **Přidat**.

        ![Rozbalte Azure SDK][10]

6. Přidejte do projektu nový soubor záhlaví s názvem **Constants.h**. Chcete-li tak učinit, klepněte pravým tlačítkem myši na název projektu a vyberte **nový soubor...**. Pak vyberte **Soubor záhlaví**. V tomto souboru jsou konstanty vašeho centra oznámení. Pak vyberte **Další**. Pojmenujte soubor **Constants.h**.

7. Do souboru Constants.h přidejte následující kód:

    ```objc
    #ifndef Constants_h
    #define Constants_h

    extern NSString* const NHInfoConnectionString;
    extern NSString* const NHInfoHubName;
    extern NSString* const NHUserDefaultTags;

    #endif /* Constants_h */
    ```

8. Přidejte soubor implementace pro Constants.h. Chcete-li tak učinit, klepněte pravým tlačítkem myši na název projektu a vyberte **nový soubor...**. Vyberte **Soubor cíle C**a pak vyberte **Další**. Pojmenujte soubor **Constants.m**.

    ![Přidat soubor .m](media/notification-hubs-ios-get-started/new-file-objc.png)

9. Otevřete soubor **Constants.m** a nahraďte jeho obsah následujícím kódem. Nahraďte zástupné `NotificationHubConnectionString` symboly literálu řetězce a `NotificationHubConnectionString` názvem centra a **standardem DefaultListenSharedAccessSignature**, jak jste dříve získali z portálu:

    ```objc
    #import <Foundation/Foundation.h>
    #import "Constants.h"

    NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
    NSString* const NHInfoHubName = @"NotificationHubName";
    NSString* const NHUserDefaultTags = @"notification_tags";
    ```

10. Otevřete soubor **AppDelegate.h** projektu a nahraďte jeho obsah následujícím kódem:

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end

    ```

11. Do souboru **AppDelegate.m** projektu přidejte `import` následující příkazy:

    ```objc
    #import "Constants.h"
    #import "NotificationDetailViewController.h"
    ```

12. Také v souboru **AppDelegate.m** přidejte následující `didFinishLaunchingWithOptions` řádek kódu v metodě založené na verzi iOS. Tento kód zaregistruje popisovač vašeho zařízení do APN:

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. Ve stejném souboru **AppDelegate.m** nahraďte veškerý kód po `didFinishLaunchingWithOptions` s následujícím kódem:

    ```objc
    // Tells the app that a remote notification arrived that indicates there is data to be fetched.

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
        NSLog(@"Received remote (silent) notification");
        [self logNotificationDetails:userInfo];

        //
        // Let the system know the silent notification has been processed.
        //
        completionHandler(UIBackgroundFetchResultNoData);
    }

    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).

    - (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
        NSMutableSet *tags = [[NSMutableSet alloc] init];

        // Load and parse stored tags
        NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
        if (unparsedTags.length > 0) {
            NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];
            [tags addObjectsFromArray:tagsArray];
        }

        // Register the device with the Notification Hub.
        // If the device has not already been registered, this will create the registration.
        // If the device has already been registered, this will update the existing registration.
        //
        SBNotificationHub* hub = [self getNotificationHub];
        [hub registerNativeWithDeviceToken:deviceToken tags:tags completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            } else {
                [self showAlert:@"Registered" withTitle:@"Registration Status"];
            }
        }];
    }

    // UNUserNotificationCenterDelegate methods
    //
    // Asks the delegate how to handle a notification that arrived while the app was running in the  foreground.

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
        NSLog(@"Received notification while the application is in the foreground");

        // The system calls this delegate method when the app is in the foreground. This allows the app to handle the notification
        // itself (and potentially modify the default system behavior).

        // Handle the notification by displaying custom UI.
        //
        [self showNotification:notification.request.content.userInfo];

        // Use 'options' to specify which default behaviors to enable.
        // - UNAuthorizationOptionBadge: Apply the notification's badge value to the app’s icon.
        // - UNAuthorizationOptionSound: Play the sound associated with the notification.
        // - UNAuthorizationOptionAlert: Display the alert using the content provided by the notification.
        //
        // In this case, do not pass UNAuthorizationOptionAlert because the notification was handled by the app.
        //
        completionHandler(UNAuthorizationOptionBadge | UNAuthorizationOptionSound);
    }

    // Asks the delegate to process the user's response to a delivered notification.
    //

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
        NSLog(@"Received notification while the application is in the background");

        // The system calls this delegate method when the user taps or responds to the system notification.

        // Handle the notification response by displaying custom UI
        //
        [self showNotification:response.notification.request.content.userInfo];

        // Let the system know the response has been processed.
        //
        completionHandler();
    }

    // App logic and helpers

    - (SBNotificationHub *)getNotificationHub {
        NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
        NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];

        return [[SBNotificationHub alloc] initWithConnectionString:connectionString notificationHubPath:hubName];
    }

    - (void)handleRegister {
        UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];

        UNAuthorizationOptions options =  UNAuthorizationOptionAlert | UNAuthorizationOptionSound | UNAuthorizationOptionBadge;
        [center requestAuthorizationWithOptions:(options) completionHandler:^(BOOL granted, NSError * _Nullable error) {
            if (error != nil) {
                NSLog(@"Error requesting for authorization: %@", error);
            }
        }];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }

    - (void)handleUnregister {
        //
        // Unregister the device with the Notification Hub.
        //
        SBNotificationHub *hub = [self getNotificationHub];
        [hub unregisterNativeWithCompletion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error unregistering for push: %@", error);
            } else {
                [self showAlert:@"Unregistered" withTitle:@"Registration Status"];
            }
        }];
    }

    - (void)logNotificationDetails:(NSDictionary *)userInfo {
        if (userInfo != nil) {
            UIApplicationState state = [UIApplication sharedApplication].applicationState;
            BOOL background = state != UIApplicationStateActive;
            NSLog(@"Received %@notification: \n%@", background ? @"(background) " : @"", userInfo);
        }
    }

    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
        if (title == nil) {
            title = @"Alert";
        }
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
        [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
        [self logNotificationDetails:userInfo];

        NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    Tento kód se připojuje k centru oznámení pomocí informací o připojení, které jste zadali v **souboru Constants.h**. Poté přiřadí token zařízení do centra oznámení tak, aby centrum oznámení mohlo odesílat oznámení.

### <a name="notificationdetailviewcontroller"></a>OznámeníDetailViewController

1. Podobně jako předchozí pokyny, přidejte další soubor záhlaví s názvem **NotificationDetailViewController.h**. Nahraďte obsah nového souboru záhlaví následujícím kódem:

    ```objc
    #import <UIKit/UIKit.h>

    NS_ASSUME_NONNULL_BEGIN

    @interface NotificationDetailViewController : UIViewController

    @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
    @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
    @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

    @property (strong, nonatomic) NSDictionary *userInfo;

    - (id)initWithUserInfo:(NSDictionary *)userInfo;

    @end

    NS_ASSUME_NONNULL_END
    ```

2. Přidejte soubor implementace **NotificationDetailViewController.m**. Nahraďte obsah souboru následujícím kódem, `UIViewController` který implementuje metody:

    ```objc
    #import "NotificationDetailViewController.h"

    @interface NotificationDetailViewController ()

    @end

    @implementation NotificationDetailViewController

    - (id)initWithUserInfo:(NSDictionary *)userInfo {
        self = [super initWithNibName:@"NotificationDetail" bundle:nil];
        if (self) {
            _userInfo = userInfo;
        }
        return self;
    }

    - (void)viewDidLayoutSubviews {
        [self.titleLabel sizeToFit];
        [self.bodyLabel sizeToFit];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        NSString *title = nil;
        NSString *body = nil;

        NSDictionary *aps = [_userInfo valueForKey:@"aps"];
        NSObject *alertObject = [aps valueForKey:@"alert"];
        if (alertObject != nil) {
            if ([alertObject isKindOfClass:[NSDictionary class]]) {
                NSDictionary *alertDict = (NSDictionary *)alertObject;
                title = [alertDict valueForKey:@"title"];
                body = [alertObject valueForKey:@"body"];
            } else if ([alertObject isKindOfClass:[NSString class]]) {
                body = (NSString *)alertObject;
            } else {
                NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
            }
        }

        if (title == nil) {
            title = @"<unset>";
        }

        if (body == nil) {
            body = @"<unset>";
        }

        self.titleLabel.text = title;
        self.bodyLabel.text = body;
    }

    - (IBAction)handleDismiss:(id)sender {
        [self dismissViewControllerAnimated:YES completion:nil];
    }

    @end
    ```

### <a name="viewcontroller"></a>Kontrolka zobrazení

1. Do souboru **ViewController.h** projektu přidejte `import` následující příkazy:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    ```

2. Také v **ViewController.h**, přidejte následující `@interface` deklarace vlastností za prohlášení:

    ```objc
    @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
    @property (strong, nonatomic) IBOutlet UIButton *registerButton;
    @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
    ```

3. V implementačním souboru **ViewController.m** projektu nahraďte obsah souboru následujícím kódem:

    ```objc
    #import "ViewController.h"
    #import "Constants.h"
    #import "AppDelegate.h"

    @interface ViewController ()

    @end

    @implementation ViewController

    // UIViewController methods

    - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
        // Simple method to dismiss keyboard when user taps outside of the UITextField.
        [self.view endEditing:YES];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        // Load raw tags text from storage and initialize the text field
        self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    }

    - (IBAction)handleRegister:(id)sender {
        // Save raw tags text in storage
        [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

    // Delegate processing the register action to the app delegate.
    [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
    }

    - (IBAction)handleUnregister:(id)sender {
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
    }

    @end
    ```

4. Ověřte, zda nedochází k žádným chybám tak, že sestavíte a spustíte aplikaci na vašem zařízení.

## <a name="send-test-push-notifications"></a>Odešlete nabízená oznámení

Příjem oznámení ve vaší aplikaci můžete otestovat pomocí možnosti *Testovací odeslání* na webu [Azure Portal]. Do zařízení se odešle testovací nabízené oznámení.

![Portál Azure – testovací odeslání][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Ověření, že aplikace přijímá nabízená oznámení

Chcete-li otestovat nabízená oznámení na iOS, musíte aplikaci nasadit do fyzického zařízení iOS. Nabízená oznámení Apple nelze odeslat pomocí simulátoru iOS.

1. Spusťte aplikaci a ověřte, zda byla registrace úspěšná a stiskněte klávesu **OK**.

    ![Test registrace nabízených oznámení aplikace iOS][33]

2. V dalším kroku odešlete testovací nabízené oznámení z webu [Azure Portal] podle popisu v předchozí části.

3. Nabízená odeslání se zašlou na všechna zařízení, která jsou registrovaná pro příjem oznámení z konkrétní centra oznámení.

    ![Test příjmu nabízených oznámení aplikace iOS][35]

## <a name="next-steps"></a>Další kroky

V tomto příkladu jste vysílali nabízená oznámení pro všechna vaše registrovaná zařízení iOS. Pokud se chcete naučit zasílat nabízená oznámení určitým zařízením s iOSem, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Zasílání nabízených oznámení do konkrétních zařízení](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Windows Azure Messaging Framework]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portál Azure]: https://portal.azure.com
