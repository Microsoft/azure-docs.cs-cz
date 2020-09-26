---
title: Posílání nabízených oznámení do systému iOS pomocí Notification Hubs Azure a verze 2.0.4 sady iOS SDK
description: V tomto kurzu se naučíte používat Azure Notification Hubs a službu Apple Push Notification Service k odesílání nabízených oznámení do zařízení se systémem iOS (verze 2.0.4).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: ffa562a734e0e6f898aaff89622362080bf1a053
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318190"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-version-204"></a>Kurz: odesílání nabízených oznámení do aplikací pro iOS pomocí Azure Notification Hubs (verze 2.0.4)

V tomto kurzu se dozvíte, jak pomocí služby Azure Notification Hubs odesílat nabízená oznámení do aplikace pro iOS pomocí Azure Notification Hubs SDK verze 2.0.4.

Tento kurz se zabývá následujícími kroky:

- Vytvořte si ukázkovou aplikaci pro iOS.
- Připojte svoji aplikaci pro iOS k Azure Notification Hubs.
- Odesílat testovací nabízená oznámení.
- Ověřte, že aplikace přijímá oznámení.

Kompletní kód pro tento kurz si můžete stáhnout [z GitHubu](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu budete potřebovat následující požadavky:

- Počítač Mac se systémem [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)spolu s platným certifikátem pro vývojáře nainstalovaným do řetězce klíčů.
- IPhone nebo iPad se systémem iOS verze 10 nebo novější.
- Vaše fyzické zařízení je zaregistrované na [portálu Apple](https://developer.apple.com/)a přidruženo k vašemu certifikátu.

Než budete pokračovat, přečtěte si předchozí kurz na webu Začínáme s [Azure Notification Hubs pro aplikace pro iOS](ios-sdk-get-started.md)a nastavte a nakonfigurujte přihlašovací údaje pro nabízené oznámení v centru oznámení. I když nemáte žádné předchozí zkušenosti s vývojem pro iOS, měli byste být schopni postupovat podle těchto kroků.

> [!NOTE]
> Z důvodu požadavků na konfiguraci pro nabízená oznámení musíte místo emulátoru iOS nasadit a otestovat nabízená oznámení na fyzickém zařízení iOS (iPhone nebo iPad).

## <a name="connect-your-ios-app-to-notification-hubs"></a>Připojte aplikaci iOS k centru oznámení

1. V Xcode vytvořte nový projekt iOS a vyberte šablonu aplikace s **jedním zobrazením**   .

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Vybrat šablonu":::

2. Při nastavování možností pro nový projekt nezapomeňte použít stejný **název produktu**   a **identifikátor organizace**   , který jste použili při nastavení identifikátoru sady prostředků na portálu pro vývojáře Apple.

3. V části Navigátor projektu vyberte název vašeho projektu v části **cíle**a pak vyberte kartu **možnosti podepisování &**   . Ujistěte se, že jste vybrali příslušný **tým**   pro svůj účet Apple Developer. XCode by na základě vašeho identifikátoru sady mělo automaticky stáhnout profil zřizování, který jste vytvořili dříve.

   Pokud nevidíte nový profil zřizování, který jste vytvořili v Xcode, pokuste se aktualizovat profily pro podpisové identity. Klikněte na **Xcode**   na panelu nabídek, klikněte na **Předvolby**, klikněte na kartu **účet**   , klikněte na tlačítko **Zobrazit podrobnosti**   , klikněte na svou podpisovou identitu a pak klikněte na tlačítko Aktualizovat v pravém dolním rohu.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Zobrazit podrobnosti":::

4. Na kartě **Možnosti podpisového &**   Vyberte **+ funkce**. Dvojím kliknutím na **nabízená oznámení**   ji povolte.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Funkce":::

5. Přidejte moduly Azure Notification Hubs SDK.

   Sadu Azure Notification Hubs SDK můžete integrovat do své aplikace pomocí [Cocoapods](https://cocoapods.org/)   nebo ručním přidáním binárních souborů do projektu.

   - Integrace přes Cocoapods: přidejte do svého souboru podfile následující závislosti, které budou zahrnovat sadu Azure Notification Hubs SDK do vaší aplikace:

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Spuštěním instalace pod nainstalujete instalaci nově definovaného bodu pod a otevřete. xcworkspace.

         Pokud se zobrazí chyba, jako je například, **že se nepovedlo najít specifikaci pro AzureNotificationHubs-iOS** při spuštění po instalaci, spusťte příkaz, `pod repo update` abyste získali nejnovější lusky z úložiště Cocoapods a pak spustíte pod instalací.

   - Integrace přes Carthage: přidejte do svého Cartfile následující závislosti, které budou zahrnovat sadu Azure Notification Hubs SDK do vaší aplikace:

      ```ruby
      github "Azure/azure-notificationhubs-ios"
      ```

      - Potom aktualizujte závislosti sestavení:

      ```shell
      $ carthage update
      ```

      Další informace o použití Carthage najdete v [úložišti GitHub Carthage](https://github.com/Carthage/Carthage).

   - Integrace kopírováním binárních souborů do projektu: můžete je integrovat zkopírováním binárních souborů do projektu následujícím způsobem:

        - Stáhněte si rozhraní [Azure Notification HUBS SDK](https://github.com/Azure/azure-notificationhubs-android/releases)   , které je k dispozici jako soubor zip, a rozbalte ho.

        - V Xcode klikněte pravým tlačítkem na projekt a kliknutím na **Přidat soubory do**   přidejte do svého projektu Xcode složku **WindowsAzureMessaging. Framework**   . Vyberte **Možnosti**   a ujistěte se, že je vybraná možnost **Kopírovat položky v případě potřeby**   , a pak klikněte na **Přidat**.

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Přidat rozhraní":::

6. Přidejte nový hlavičkový soubor do projektu s názvem **konstanty. h**. Provedete to tak, že kliknete pravým tlačítkem myši na název projektu a vyberete **nový soubor...**. Pak vyberte **hlavičkový soubor**. V tomto souboru jsou konstanty vašeho centra oznámení. Pak vyberte **Další**. Pojmenujte soubor **konstanty. h**.

7. Do souboru konstanty. h přidejte následující kód:

   ```objc
   #ifndef Constants_h
   #define Constants_h
   extern NSString* const NHInfoConnectionString;
   extern NSString* const NHInfoHubName;
   extern NSString* const NHUserDefaultTags;
   #endif /* Constants_h */
   ```

8. Přidejte implementační soubor pro konstanty. h. Provedete to tak, že kliknete pravým tlačítkem myši na název projektu a vyberete **nový soubor...**. Vyberte **cíl-C soubor**a pak vyberte **Další**. Pojmenujte soubor **konstantami. m**.

   :::image type="content" source="media/ios-sdk/image5.png" alt-text="Přidat implementační soubor":::

9. Otevřete soubor **konstanty. m**   a nahraďte jeho obsah následujícím kódem. Nahraďte zástupné symboly řetězcového literálu  `NotificationHubConnectionString`   a  `NotificationHubConnectionString`   názvem centra a **DefaultListenSharedAccessSignature**, v uvedeném pořadí, jak jste předtím získali z portálu:

   ```objc
   #import <Foundation/Foundation.h>
   #import "Constants.h"

   NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
   NSString* const NHInfoHubName = @"NotificationHubName";NSString* const NHUserDefaultTags = @"notification_tags";
   ```

10. Otevřete soubor projektu **AppDelegate. h**   a nahraďte jeho obsah následujícím kódem:

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,   UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end
    ```

11. V souboru projektu **AppDelegate. m**   přidejte následující  `import`   příkazy:

    ```objc
    #import "Constants.h"
    #import "NotificationDetailViewController.h"
    ```

12. Také v souboru **AppDelegate. m**   přidejte následující řádek kódu do  `didFinishLaunchingWithOptions`   metody na základě vaší verze iOS. Tento kód zaregistruje popisovač vašeho zařízení do APN:

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. Ve stejném souboru **AppDelegate. m**   nahraďte veškerý kód za  `didFinishLaunchingWithOptions`   následujícím kódem:

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

    Tento kód se připojí k centru oznámení pomocí informací o připojení, které jste zadali v **konstantách. h**. Pak přiřadí token zařízení do centra oznámení tak, aby centrum mohlo odesílat oznámení.

### <a name="create-notificationdetailviewcontroller-header-file"></a>Vytvořit soubor hlaviček NotificationDetailViewController

1. Podobně jako v předchozích pokynech přidejte další hlavičkový soubor s názvem **NotificationDetailViewController. h**. Nahraďte obsah nového hlavičkového souboru následujícím kódem:

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

2. Přidejte implementační soubor **NotificationDetailViewController. m**. Nahraďte obsah souboru následujícím kódem, který implementuje metody UIViewController:

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

### <a name="viewcontroller"></a>Soubor viewcontroller

1. V souboru projektu **soubor viewcontroller. h**   přidejte následující  `import`   příkazy:

   ```objc
   #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
   #import <UserNotifications/UserNotifications.h>
   ```

2. V **soubor viewcontroller. h**přidejte následující deklarace vlastností za  `@interface`   deklaraci:

   ```objc
   @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
   @property (strong, nonatomic) IBOutlet UIButton *registerButton;
   @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
   ```

3. V implementačním souboru **soubor viewcontroller. m**projektu   nahraďte obsah souboru následujícím kódem:

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

Příjem oznámení ve vaší aplikaci můžete otestovat pomocí možnosti **Odeslat test**   v [Azure Portal](https://portal.azure.com/). Do zařízení se odešle testovací nabízené oznámení.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Odeslat test":::

Nabízená oznámení se většinou posílají ve službě back-end, jako je služba Mobile Apps, nebo v technologii ASP.NET pomocí kompatibilní knihovny. Pokud pro váš back-end není dostupná žádná knihovna, můžete k posílání oznámení použít také REST API přímo.

Tady je seznam některých dalších kurzů, které byste mohli chtít zkontrolovat při odesílání oznámení:

- Azure Mobile Apps: Příklad odesílání oznámení z back-endu Mobile Apps, který je integrovaný do Notification Hubs, najdete v tématu [Přidání nabízených oznámení do vaší aplikace pro iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).
- ASP.NET: [použijte Notification Hubs k odesílání nabízených oznámení uživatelům](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- Sada SDK pro Azure Notification Hubs Java: Přečtěte si téma [použití Notification Hubs z Java](notification-hubs-java-push-notification-tutorial.md)   k odesílání oznámení z Java. Tato metoda prošla pro potřeby vývoje pro Android testováním v Eclipse.
- PHP: [použití Notification Hubs z php](notification-hubs-php-push-notification-tutorial.md).

## <a name="verify-that-your-app-receives-push-notifications"></a>Ověření, že aplikace přijímá nabízená oznámení

Chcete-li otestovat nabízená oznámení na iOS, musíte aplikaci nasadit do fyzického zařízení iOS. Nabízená oznámení Apple nemůžete odesílat pomocí simulátoru iOS.

1. Spusťte aplikaci a ověřte, že registrace proběhla úspěšně, a pak stiskněte **OK**.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Zaregistrovat":::

2. Dále odešlete testovací nabízené oznámení z [Azure Portal](https://portal.azure.com/), jak je popsáno v předchozí části.

3. Nabízené oznámení se odešle na všechna zařízení, která jsou zaregistrovaná pro příjem oznámení z daného centra oznámení.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Odeslat test":::

## <a name="next-steps"></a>Další kroky

V tomto jednoduchém příkladu vysíláte nabízená oznámení do všech registrovaných zařízení se systémem iOS. Pokud chcete zjistit, jak odesílat nabízená oznámení na konkrétní zařízení s iOS, přejděte k následujícímu kurzu:

[Kurz: nabízená oznámení na konkrétní zařízení](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Další informace najdete v následujících článcích:

- [Přehled služby Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Rozhraní REST API pro Notification Hubs](/rest/api/notificationhubs/)
- [Sada SDK Notification Hubs pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK na GitHubu](https://github.com/Azure/azure-notificationhubs)
- [Registrovat do back-endu aplikace](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Správa registrací](notification-hubs-push-notification-registration-management.md)
- [Práce s značkami](notification-hubs-tags-segment-push-message.md)
- [Práce s vlastními šablonami](notification-hubs-templates-cross-platform-push-messages.md)
- [Řízení přístupu Service Bus pomocí sdílených přístupových podpisů](../service-bus-messaging/service-bus-sas.md)
- [Generování tokenů SAS prostřednictvím kódu programu](/rest/api/eventhub/generate-sas-token)
- [Apple Security: běžné šifrování](https://developer.apple.com/security/)
- [Čas v epocha systému UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)