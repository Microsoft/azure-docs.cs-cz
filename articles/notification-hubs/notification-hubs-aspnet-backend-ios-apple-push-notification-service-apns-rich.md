---
title: Rozšířené nabízení center oznámení Azure
description: Přečtěte si, jak posílat bohatá nabízená oznámení do aplikace pro iOS z Azure. Ukázky kódu napsané v objective-C a C#.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9da629929ca88f406dc503710477104be94c47e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212202"
---
# <a name="azure-notification-hubs-rich-push"></a>Rozšířené nabízení center oznámení Azure

## <a name="overview"></a>Přehled

Aby bylo možné zaujmout uživatele s okamžitým bohatým obsahem, aplikace může chtít posunout za prostý text. Tato oznámení propagují uživatelské interakce a prezentují obsah, jako jsou adresy URL, zvuky, obrázky/kupóny a další. Tento kurz vychází z tématu [Upozornit uživatele](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) a ukazuje, jak odesílat nabízená oznámení, která obsahují datové části (například obrázek).

Tento výukový program je kompatibilní s iOS 7 & 8.

  ![][IOS1]

Na vysoké úrovni:

1. Back-end aplikace:
   * Ukládá bohatou datovou část (v tomto případě obrázek) do back-endové databáze/místního úložiště.
   * Odešle ID tohoto rozšířeného oznámení do zařízení.
2. Aplikace na zařízení:
   * Kontaktuje back-end požadující bohatou datovou část s ID, které obdrží.
   * Odešle uživatelům oznámení na zařízení po dokončení načítání dat a zobrazí datovou část okamžitě, když uživatelé klepnou další informace

## <a name="webapi-project"></a>Projekt WebAPI

1. V sadě Visual Studio otevřete projekt **AppBackend,** který jste vytvořili v kurzu [Upozornit uživatele.](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)
2. Získejte obrázek, se kterým chcete uživatele upozornit, a vložte jej do složky **img** v adresáři projektu.
3. V Průzkumníku řešení klikněte na **Zobrazit všechny soubory** a klikněte pravým tlačítkem myši na složku, která chcete zahrnout do **projectu**.
4. S vybranou bitovou kopii změňte jeho okno Akce sestavení ve vlastnostech na **Vložený prostředek**.

    ![][IOS2]
5. V `Notifications.cs`aplikace přidejte následující příkaz using:

    ```csharp
    using System.Reflection;
    ```
6. Aktualizujte `Notifications` celou třídu pomocí následujícího kódu. Nezapomeňte nahradit zástupné symboly přihlašovacími údaji centra oznámení a názvem souboru obrázku.

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

   > [!NOTE]
   > (nepovinné) Další informace o přidání a získání zdrojů projektu naleznete v naleznete v informacích o [tom,](https://support.microsoft.com/kb/319292) jak přidat a získat prostředky projektu.

7. V `NotificationsController.cs`, předefinovat 'NotificationsController s následujícími úryvky. Tím odešlete počáteční tiché bohaté id oznámení do zařízení a umožňuje na straně klienta načítání bitové kopie:

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
8. Teď tuto aplikaci znovu nasadíme na web Azure, aby byla přístupná ze všech zařízení. Klikněte pravým tlačítkem na projekt **AppBackend** a vyberte **Publikovat**.
9. Jako cíl publikování vyberte web Azure. Přihlaste se pomocí svého účtu Azure a vyberte existující nebo nový web a poznamenejte si **vlastnost cílové adresy URL** na kartě **Připojení.** Budeme odkazovat na tuto adresu URL jako *koncový bod back-endu* dále v tomto kurzu. Klikněte na **Publikovat**.

## <a name="modify-the-ios-project"></a>Úprava projektu iOS

Teď, když jste upravili back-end aplikace tak, aby odeslal pouze *id* oznámení, změníte aplikaci pro iOS tak, aby zpracovávala toto id a načetla bohatou zprávu z back-endu.

1. Otevřete svůj projekt iOS a povolte vzdálená oznámení tak, že přejdete na hlavní cíl aplikace v části **Cíle.**
2. Klikněte na **Možnosti**, zapněte **režimy na pozadí**a zaškrtněte **políčko Vzdálená oznámení.**

    ![][IOS3]
3. Otevřete `Main.storyboard`a ujistěte se, že máte řadič zobrazení (označovaný jako řadič domovského zobrazení v tomto kurzu) z kurzu [Upozornit uživatele.](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)
4. Přidejte **navigační ovladač** do scénáře a přetažením ovládacího prvku do řadiče domovského zobrazení, aby se z něj stalo **kořenové zobrazení** navigace. Ujistěte se, že je vybrán **ovladač Počáteční zobrazení** v atributech pouze pro navigační kontrolér.
5. Přidejte do scénáře **řadič zobrazení** a přidejte zobrazení **obrázků**. Toto je stránka, kterou uživatelé uvidí, jakmile se rozhodnou dozvědět se více kliknutím na oznámení. Váš scénář by měl vypadat takto:

    ![][IOS4]
6. Klikněte na **řadič domovského zobrazení** ve scénáři a ujistěte se, že má **homeViewController** jako **vlastní třídy** a **Storyboard ID** pod inspektoridentity.
7. Proveďte totéž pro řadič zobrazení obrazu jako **imageViewController**.
8. Potom vytvořte novou třídu kontroleru zobrazení s názvem **imageViewController** pro zpracování právě vytvořeného uhlavního zobrazení.
9. V **imageViewController.h**přidejte do deklarací rozhraní řadiče následující. Ujistěte se, že control-drag z zobrazení obrázku scénáře do těchto vlastností propojit dva:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```
10. V `imageViewController.m`písmenu a) přidejte na konec `viewDidload`textu následující :

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```
11. V `AppDelegate.m`aplikaci importujte vytvořený řadič obrázků:

    ```objc
    #import "imageViewController.h"
    ```
12. Přidejte oddíl rozhraní s následujícím prohlášením:

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
13. V `AppDelegate`aplikaci se ujistěte, že `application: didFinishLaunchingWithOptions`se aplikace registruje pro tichá oznámení v :

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

14. Nahraďte v `application:didRegisterForRemoteNotificationsWithDeviceToken` následující implementaci pro vzít v úvahu změny ui scénáře:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```
15. Potom přidejte následující `AppDelegate.m` metody k načtení obrázku z koncového bodu a odeslat místní oznámení po dokončení načítání. Nezapomeňte nahradit zástupný `{backend endpoint}` symbol koncovým bodem back-endu:

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
16. Zpracovat místní oznámení výše otevřením řadiče zobrazení obrazu v `AppDelegate.m` pomocí následujících metod:

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

1. V XCode spusťte aplikaci na fyzickém zařízení se systémem iOS (nabízená oznámení nebudou v simulátoru fungovat).
2. V uživatelském rozhraní aplikace pro iOS zadejte uživatelské jméno a heslo se stejnou hodnotou pro ověřování a klikněte na **Přihlásit se**.
3. Klikněte na **Odeslat nabízenou nabídku** a měla by se zobrazit výstraha v aplikaci. Pokud kliknete na **další**, budete převedeni na obrázek, který jste se rozhodli zahrnout do back-endu aplikace.
4. Můžete také kliknout na **tlačítko Odeslat push** a okamžitě stisknout domovské tlačítko zařízení. Za několik okamžiků obdržíte nabízené oznámení. Pokud na něj klepnete nebo kliknete na Další, budete převedeni do aplikace a bohatého obsahu obrázků.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
