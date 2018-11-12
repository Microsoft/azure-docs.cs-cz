---
title: Azure Notification Hubs bohaté Push
description: Zjistěte, jak odesílat bohaté nabízená oznámení do aplikací pro iOS z Azure. Ukázky kódu napsané v jazyce Objective-C a C#.
documentationcenter: ios
services: notification-hubs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: 8aad769da4d1c831dc0222c39daf86801a4f850b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244283"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure Notification Hubs bohaté Push
## <a name="overview"></a>Přehled
Aby bylo možné zapojení uživatelů pomocí rychlých formátovaný obsah aplikace chtít nabízet nad rámec prostý text. Tato oznámení podporovat interakcí s uživateli a k dispozici obsah, jako jsou adresy URL, zvuky, Image/kupóny a další. Tento kurz vychází [oznamování uživatelům pomocí](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) tématu a ukazuje, jak odesílat nabízená oznámení, které zahrnují datových částí (například obrázek).

Tento kurz je kompatibilní se systémem iOS 7 a 8.

  ![][IOS1]

Na vysoké úrovni:

1. Back-endu aplikace:
   * Ukládá bohaté datová část (v tomto případě obrázek) v back-endového úložiště databáze/místní funkce
   * ID tohoto bohaté oznámení se odešle do zařízení.
2. Aplikace na zařízení:
   * Kontaktuje požaduje bohaté datové části s ID přijme back-endu
   * Odešle oznámení uživatelům na zařízení po dokončení načítání dat a datové části se zobrazí okamžitě, když uživatel klepnutím na další informace

## <a name="webapi-project"></a>Projektem WebAPI
1. V sadě Visual Studio, otevřete **AppBackend** projekt, který jste vytvořili v [oznamování uživatelům pomocí](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) kurzu.
2. Získat image byste chtěli upozornit uživatele a vložit ho do **img** složku v adresáři projektu.
3. Klikněte na tlačítko **zobrazit všechny soubory** v Průzkumníku řešení klikněte pravým tlačítkem na složku, do které **zahrnout do projektu**.
4. Image, vybraný, změnit její akce sestavení v okně Vlastnosti **integrovaný prostředek**.
   
    ![][IOS2]
5. V **Notifications.cs**, přidejte následující příkaz using:
   
        using System.Reflection;
6. Aktualizovat celé **oznámení** třídy následujícím kódem. Nezapomeňte nahradit zástupné symboly pomocí vašich přihlašovacích údajů centra oznámení a název souboru obrázku.
   
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
   
   > [!NOTE]
   > (volitelné) Odkazovat na [vložení a přístup k prostředkům pomocí Vizuálu C# ](https://support.microsoft.com/kb/319292) Další informace o tom, jak přidat a získání prostředků projektu.
   > 
   > 
7. V **NotificationsController.cs**, znovu definovat **NotificationsController** s následující fragmenty kódu. To odesílá id počáteční tiché bohaté oznámení do zařízení a umožní na straně klienta načtení bitové kopie:
   
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
8. Nyní jsme se znovu nasadit tuto aplikaci na web Azure aby byla přístupná ze všech zařízení. Klikněte pravým tlačítkem na projekt **AppBackend** a vyberte **Publikovat**.
9. Vyberte web Azure jako váš cíl publikování. Přihlaste se pomocí svého účtu Azure a vyberte stávající nebo novou webovou stránku, poznamenejte si **cílovou adresu URL** vlastnost **připojení** kartu. Na tuto adresu URL budeme odkazovat jako na *koncový bod back-endu* později v tomto kurzu. Klikněte na **Publikovat**.

## <a name="modify-the-ios-project"></a>Upravit projekt pro iOS
Teď, když jste upravili back-endu aplikace k odeslání jenom *id* oznámení, se změní vaše aplikace iOS bude zpracovávat toto id a načítat bohaté zprávy z back-endu.

1. Otevření projektu pro iOS a povolit vzdálená oznámení tak, že přejdete do cíle v hlavní aplikaci **cíle** oddílu.
2. Klikněte na **možnosti**, zapněte **režimy běhu na pozadí**a zkontrolujte **Vzdálená oznámení** zaškrtávací políčko.
   
    ![][IOS3]
3. Přejděte na **Main.storyboard**, a ujistěte se, že máte kontroler zobrazení (označuje jako Domů kontroler zobrazení v tomto kurzu) z [upozornit uživatele](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) kurzu.
4. Přidat **kontroler navigace** scénáře, a přetáhněte ovládací prvek na Domů Kontroleru zobrazení k němu **root zobrazení** navigace. Ujistěte se, že **je počáteční kontroler zobrazení** v atributech inspector je vybraná jenom kontroler navigace.
5. Přidat **kontroler zobrazení** scénáře a přidat **zobrazení obrázku**. Toto je stránka, kterou uživatelé uvidí, když se rozhodnete další informace získáte kliknutím na notifiication. Scénář by měl vypadat takto:
   
    ![][IOS4]
6. Klikněte na **Domů kontroler zobrazení** ve scénáři a ujistěte se, že má **homeViewController** jako jeho **vlastní třídy** a **ID scénáře**pod inspektor Identity.
7. Totéž proveďte pro kontroler zobrazení obrázku jako **imageViewController**.
8. Vytvořte novou třídu Kontroleru zobrazení s názvem **imageViewController** pro zpracování uživatelského rozhraní, které jste právě vytvořili.
9. V **imageViewController.h**, přidejte následující deklarace rozhraní kontroleru. Ujistěte se, že ovládací prvek přetažením z image zobrazení scénáře pro tyto vlastnosti pro jejich propojení:
   
        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;
10. V **imageViewController.m**, přidejte následující na konci **viewDidload**:
    
        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];
11. V **AppDelegate.m**, importovat image kontroleru jste vytvořili:
    
        #import "imageViewController.h"
12. Přidáte oddíl rozhraní s následující deklarace:
    
        @interface AppDelegate ()
    
        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;
    
        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;
    
        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;
    
        @end
13. V **AppDelegate**, ujistěte se, že vaše aplikace registruje tiché oznámení v **application: didFinishLaunchingWithOptions**:
    
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

1. Nahraďte v následující implementaci pro **aplikace: didRegisterForRemoteNotificationsWithDeviceToken** se scénáři uživatelského rozhraní se změní na účet:
   
       // Access navigation controller which is at the root of window
       UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
       // Get home view controller from stack on navigation controller
       homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
       hvc.deviceToken = deviceToken;
2. Pak přidejte následující metody, které **AppDelegate.m** načíst obrázek z vašeho koncového bodu a odesílat místního oznámení po dokončení načítání. Nezapomeňte nahradit zástupné `{backend endpoint}` s váš koncový bod back-endu:
   
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
3. Zpracování místního oznámení nad otevřením kontroler zobrazení obrázků v **AppDelegate.m** pomocí následujících metod:
   
       // Helper: redirect users to image view controller
       - (void)redirectToImageViewWithImage: (UIImage *)img {
           UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
           UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                    bundle: nil];
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

## <a name="run-the-application"></a>Spuštění aplikace
1. V XCode spusťte aplikaci na fyzickém zařízení iOS (nabízená oznámení nebude fungovat v simulátoru).
2. V uživatelském rozhraní aplikací iOS zadejte uživatelské jméno a heslo stejnou hodnotu pro ověřování a klikněte na tlačítko **přihlásit**.
3. Klikněte na tlačítko **odesílání nabízených oznámení** a zobrazí se oznámení v aplikaci. Pokud kliknete na **Další**, přejdete do bitové kopie, které jste se rozhodli zahrnout do back-endu aplikace.
4. Můžete také kliknout na **odesílání nabízených oznámení** a okamžitě stiskněte tlačítko Domů vašeho zařízení. Za malou chvíli obdržíte nabízené oznámení. Pokud klepnete na něm nebo klikněte na tlačítko Další, je přeneseno do vaší aplikace a obsahu bohaté bitové kopie.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
