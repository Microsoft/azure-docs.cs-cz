---
title: Azure Notification Hubs Rich push
description: Naučte se odesílat bohatá nabízená oznámení do aplikace pro iOS z Azure. Ukázky kódu napsané v cíli – C a C#.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 33626b7aee615d07ef88dd9fbca46e6512e2cafc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90090359"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure Notification Hubs Rich push

## <a name="overview"></a>Přehled

Aby bylo možné zapojovat uživatele s okamžitým bohatým obsahem, může aplikace chtít vložit přesahující prostý text. Tato oznámení povýší interakce uživatelů a prezentují obsah, jako jsou adresy URL, zvuky, obrázky/kupóny a další. Tento kurz se sestaví v kurzu [informování uživatelů](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) a ukazuje, jak odesílat nabízená oznámení, která obsahují datové části (například Image).

Tento kurz je kompatibilní se systémy iOS 7 a 8.

  ![Tři snímky obrazovky: obrazovka aplikace s tlačítkem pro odeslání nabízených oznámení, úvodní obrazovka na zařízení a logo Windows s tlačítkem zpět.][IOS1]

Na nejvyšší úrovni:

1. Back-end aplikace:
   * Ukládá bohatou datovou část (v tomto případě image) do back-endu databáze nebo místního úložiště.
   * Odešle ID tohoto bohatého oznámení do zařízení.
2. Aplikace na zařízení:
   * Kontaktuje back-end požadující bohatou datovou část s ID, které přijímá.
   * Po dokončení načítání dat pošle uživateli oznámení na zařízení a zobrazí datovou část hned, když se uživatelé klepnutím zobrazí další informace.

## <a name="webapi-project"></a>Projekt WebAPI

1. V aplikaci Visual Studio otevřete projekt **projekt appbackend** , který jste vytvořili v kurzu pro [upozorňování uživatelů](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Získejte image, se kterou chcete uživatele informovat, a vložte ji do složky **img** v adresáři projektu.
3. Klikněte na **Zobrazit všechny soubory** v Průzkumník řešení a klikněte pravým tlačítkem myši na složku, kterou chcete **zahrnout do projektu**.
4. Když je vybraná image, změňte její **akci sestavení** v okně **vlastnosti** na **Integrovaný prostředek**.

    ![Snímek obrazovky Průzkumník řešení. Je vybrán soubor obrázku a v podokně vlastností je vložený prostředek uveden jako akce sestavení.][IOS2]
5. Do `Notifications.cs` přidejte následující `using` příkaz:

    ```csharp
    using System.Reflection;
    ```

6. Třídu nahraďte `Notifications` následujícím kódem. Nezapomeňte nahradit zástupné symboly přihlašovacími údaji centra oznámení a názvem souboru obrázku:

    ```csharp
    public class Notification {
        public int Id { get; set; }
        // Initial notification message to display to users
        public string Message { get; set; }
        // Type of rich payload (developer-defined)
        public string RichType { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
        }

        public Notification CreateNotification(string message, string richType, string payload) {
            var notification = new Notification() {
                Id = notifications.Count,
                Message = message,
                RichType = richType,
                Payload = payload,
                Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Stream ReadImage(int id) {
            var assembly = Assembly.GetExecutingAssembly();
            // Placeholder: image file name (for example, logo.png).
            return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
        }
    }
    ```

7. V aplikaci `NotificationsController.cs` předefinujte znovu `NotificationsController` následující kód. Tím se na zařízení pošle počáteční tiché ID oznámení, které umožňuje načtení image na straně klienta:

    ```csharp
    // Return http response with image binary
    public HttpResponseMessage Get(int id) {
        var stream = Notifications.Instance.ReadImage(id);

        var result = new HttpResponseMessage(HttpStatusCode.OK);
        result.Content = new StreamContent(stream);
        // Switch in your image extension for "png"
        result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

        return result;
    }

    // Create rich notification and send initial silent notification (containing id) to client
    public async Task<HttpResponseMessage> Post() {
        // Replace the placeholder with image file name
        var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // Silent notification with content available
        var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

        // Send notification to apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

8. Teď tuto aplikaci znovu nasaďte na web Azure, aby byla přístupná ze všech zařízení. Klikněte pravým tlačítkem na projekt **AppBackend** a vyberte **Publikovat**.
9. Jako cíl publikování vyberte **web Azure** . Přihlaste se pomocí svého účtu Azure a vyberte existující nebo nový web a poznamenejte si vlastnost **cílová adresa URL** na kartě **připojení** . Na tuto adresu URL odkazujeme jako na *koncový bod back-end* později v tomto kurzu. Vyberte **Publikovat**.

## <a name="modify-the-ios-project"></a>Úprava projektu iOS

Teď, když jste změnili back-end aplikace tak, aby odesílaly jenom *ID* oznámení, změňte aplikaci pro iOS tak, aby zpracovala toto ID, a načtěte bohatou zprávu z back-endu:

1. Otevřete svůj projekt pro iOS a povolte vzdálená oznámení tak, že v části **cíle** spustíte hlavní cíl aplikace.
2. Vyberte **Možnosti**, povolit **režimy na pozadí** a zaškrtněte políčko **Vzdálená oznámení** .

    ![Snímek obrazovky s projektem iOS, na kterém se zobrazuje obrazovka možnosti Režimy pozadí jsou zapnuté a zaškrtávací políčko pro vzdálené oznámení je zaškrtnuté.][IOS3]
3. Otevřete `Main.storyboard` a ujistěte se, že máte kontroler zobrazení (v tomto kurzu se označuje jako kontroler domácí zobrazení) z kurzu pro [upozorňování uživatele](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
4. Přidejte do scénáře **navigační kontroler** a ovládací prvek – přetáhněte kontroler zobrazení domů, aby byl **kořenovým zobrazením** navigace. Ujistěte se, že je zaškrtnuto políčko **je prvotní kontroler zobrazení** v inspektoru atributů pouze pro navigační kontroler.
5. Přidejte do scénáře **kontroler zobrazení** a přidejte **zobrazení obrázku**. Toto je stránka, kterou uživatelé uvidí, až se po výběru zobrazí další informace kliknutím na oznámení. Scénář by měl vypadat takto:

    ![Snímek obrazovky scénáře. K dispozici jsou tři obrazovky aplikací: navigační zobrazení, domácí zobrazení a zobrazení obrázku.][IOS4]
6. Ve scénáři klikněte na **kontroler domovského zobrazení** a ujistěte se, že se v inspektoru identity **HomeViewController** jako **vlastní třída** a **ID scénáře** .
7. Proveďte stejné pro kontroler zobrazení obrázku, jako **imageViewController**.
8. Pak vytvořte novou třídu kontroleru zobrazení s názvem **imageViewController** pro zpracování právě vytvořeného uživatelského rozhraní.
9. V **imageViewController. h** přidejte do deklarací rozhraní kontroleru následující kód. Nezapomeňte ovládat – přetáhněte z zobrazení image ve scénáři na tyto vlastnosti, abyste provedli propojení dvou:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```

10. V `imageViewController.m` přidejte následující na konci `viewDidload` :

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```

11. V nástroji `AppDelegate.m` importujte vytvořený kontroler imagí:

    ```objc
    #import "imageViewController.h"
    ```

12. Přidejte oddíl rozhraní s následující deklarací:

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```

13. V nástroji `AppDelegate` se ujistěte, že vaše aplikace registruje pro tichá oznámení v nástroji `application: didFinishLaunchingWithOptions` :

    ```objc
    // Software version
    self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

    // Register for remote notifications for iOS8 and previous versions
    if (self.iOS8) {
        NSLog(@"This device is running with iOS8.");

        // Action
        UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
        richPushAction.identifier = @"richPushMore";
        richPushAction.activationMode = UIUserNotificationActivationModeForeground;
        richPushAction.authenticationRequired = NO;
        richPushAction.title = @"More";

        // Notification category
        UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
        richPushCategory.identifier = @"richPush";
        [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

        // Notification categories
        NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge
                                                                                    categories:richPushCategories];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    }
    else {
        // Previous iOS versions
        NSLog(@"This device is running with iOS7 or earlier versions.");

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    }

    return YES;
    ```

14. Pokud `application:didRegisterForRemoteNotificationsWithDeviceToken` chcete, aby se změny uživatelského rozhraní scénáře projevily v účtu, nahraďte následující implementaci:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```

15. Pak přidejte následující metody k `AppDelegate.m` načtení obrázku z koncového bodu a odeslání místního oznámení po dokončení načítání. Nezapomeňte zástupný text nahradit `{backend endpoint}` koncovým bodem back-end:

    ```objc
    NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

    // Helper: retrieve notification content from backend with rich notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
        // Check if authenticated
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                delegate:nil
                                delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200) {
                // From NSData to UIImage
                self.imagePayload = [UIImage imageWithData:data];

                completion(nil);
            }
            else {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(error);
                else {
                    completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    // Handle silent push notifications when id is sent from backend
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
        self.userInfo = userInfo;
        int richId = [[self.userInfo objectForKey:@"richId"] intValue];
        NSString* richType = [self.userInfo objectForKey:@"richType"];

        // Retrieve image data
        if ([richType isEqualToString:@"img"]) {  
            [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                if (!error){
                    // Send local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                    // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                    localNotification.userInfo = self.userInfo;
                    localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];

                    // iOS8 categories
                    if (self.iOS8) {
                        localNotification.category = @"richPush";
                    }

                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    handler(UIBackgroundFetchResultNewData);
                }
                else{
                    handler(UIBackgroundFetchResultFailed);
                }
            }];
        }
        // Add "else if" here to handle more types of rich content such as url, sound files, etc.
    }
    ```

16. Pořídí předchozí místní oznámení otevřením kontroleru zobrazení imagí v `AppDelegate.m` nástroji pomocí následujících metod:

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
        imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
        // Pass data/image to image view controller
        imgViewController.imagePayload = img;

        // Redirect
        [navigationController pushViewController:imgViewController animated:YES];
    }

    // Handle local notification sent above in didReceiveRemoteNotification
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        if (application.applicationState == UIApplicationStateActive) {
            // Show in-app alert with an extra "more" button
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
            [alert show];
        }
        // App becomes active from user's tap on notification
        else {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
    }

    // Handle buttons in in-app alerts and redirect with data/image
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
        // Handle "more" button
        if (buttonIndex == 1)
        {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        // Add "else if" here to handle more buttons
    }

    // Handle notification setting actions in iOS8
    - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
        // Handle richPush related buttons
        if ([identifier isEqualToString:@"richPushMore"]) {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        completionHandler();
    }
    ```

## <a name="run-the-application"></a>Spuštění aplikace

1. V XCode spusťte aplikaci na fyzickém zařízení s iOS (nabízená oznámení nebudou v simulátoru fungovat).
2. V uživatelském rozhraní aplikace pro iOS zadejte uživatelské jméno a heslo stejné hodnoty pro ověřování a klikněte na **Přihlásit**.
3. Klikněte na **Odeslat nabízení** oznámení a měla by se zobrazit upozornění v aplikaci. Pokud kliknete na **Další**, zobrazí se obrázek, který jste se rozhodli zahrnout do back-endu vaší aplikace.
4. Můžete také kliknout na tlačítko **Odeslat nabízení oznámení** a hned stisknout tlačítko domů zařízení. Za chvíli vám obdržíte nabízené oznámení. Pokud na něj klepnete nebo kliknete na další, dostanete se do aplikace a obsahu s bohatou imagí.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
