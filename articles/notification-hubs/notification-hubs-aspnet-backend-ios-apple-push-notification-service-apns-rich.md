---
title: Azure Notification Hubs bohaté Push
description: Naučte se odesílání bohaté nabízených oznámení do aplikace pro iOS z Azure. Ukázky kódu jsou vytvořeny v Objective-C a C#.
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
ms.openlocfilehash: 21ed7dd1120958576651703283a0732e3843546d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777453"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure Notification Hubs bohaté Push
## <a name="overview"></a>Přehled
Chcete-li zapojení uživatelů rychlých bohaté obsah, aplikace chtít nabízet nad rámec prostý text. Tato oznámení zvýšit úroveň, akce uživatelů a existuje obsah, jako jsou adresy URL, zvuky, Image nebo kupóny a další. V tomto kurzu vychází [upozornění uživatelů](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) tématu a ukazuje, jak odesílat nabízená oznámení, které jsou částí (například obrázek).

V tomto kurzu je kompatibilní s iOS 7 a 8.

  ![][IOS1]

Na vysoké úrovni:

1. Back-end aplikace:
   * Ukládá bohaté datová část (v tomto případě obrázek) v úložišti databáze nebo místní back-end
   * Odešle ID tohoto bohaté oznámení do zařízení.
2. Aplikace na zařízení:
   * Kontaktuje back-end vyžaduje bohaté datovou část s ID obdrží
   * Odešle oznámení uživatelům na zařízení po načtení dat dokončení a zobrazuje datové části okamžitě, když uživatel klepnutím na další informace

## <a name="webapi-project"></a>WebAPI projektu
1. V sadě Visual Studio, otevřete **AppBackend** projekt, který jste vytvořili v [upozornění uživatelů](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) kurzu.
2. Získat image, kterou chcete upozornit uživatele a umístí jej **img** složky v adresáři projektu.
3. Klikněte na tlačítko **zobrazit všechny soubory** v Průzkumníku řešení klikněte pravým tlačítkem složku pro **zahrnout do projektu**.
4. Vybraná Image, změnit jeho akce sestavení v okně Vlastnosti a **vložený prostředek**.
   
    ![][IOS2]
5. V **Notifications.cs**, přidejte následující příkaz using:
   
        using System.Reflection;
6. Aktualizovat celek **oznámení** třídy následujícím kódem. Ujistěte se, že nahraďte zástupné symboly vaše přihlašovací údaje centra oznámení a název souboru obrázku.
   
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
   > (volitelné) Odkazovat na [postup vložení a přístup k prostředkům pomocí Visual C#](http://support.microsoft.com/kb/319292) Další informace o tom, jak přidat a získat prostředky projektu.
   > 
   > 
7. V **NotificationsController.cs**, znovu definovat **NotificationsController** s následující fragmenty kódu. To odešle počáteční tichou bohaté oznámení id zařízení a umožňuje načítání klientské bitové kopie:
   
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
8. Nyní jsme bude znovu nasaďte tuto aplikaci na web Azure aby přístupná ze všech zařízení. Klikněte pravým tlačítkem na projekt **AppBackend** a vyberte **Publikovat**.
9. Vyberte web Azure jako váš cíl publikování. Přihlaste se pomocí účtu Azure a vyberte stávajícího nebo nového webu a poznamenejte si **cílová adresa URL** vlastnost **připojení** kartě. Na tuto adresu URL budeme odkazovat jako na *koncový bod back-endu* později v tomto kurzu. Klikněte na **Publikovat**.

## <a name="modify-the-ios-project"></a>Upravit projekt pro iOS
Teď, když jste změnili váš back-end aplikace k odesílání jen na *id* oznámení, se změní aplikace pro iOS k zpracování toto id a načtení bohaté zprávy z vaší back-end.

1. Otevřete projekt iOS a povolit vzdálenou oznámení tak, že přejdete do cílových hlavní aplikace v **cíle** části.
2. Klikněte na **možnosti**, zapnout **režimy pozadí**a zkontrolujte **vzdáleného oznámení** zaškrtávací políčko.
   
    ![][IOS3]
3. Přejděte na **Main.storyboard**a zajistěte, aby byla řadič zobrazení (uvedená jako Domů řadiče zobrazení v tomto kurzu) z [upozornit uživatele](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) kurzu.
4. Přidat **navigační řadič** scénáře a přetáhněte ovládací prvek na Domů zobrazení kontroler, aby bylo **kořenový zobrazení** navigace. Zajistěte, aby **je počáteční View Controller** v atributech inspector je vybraná řadičem navigace.
5. Přidat **View Controller** scénáře a přidat **Image zobrazení**. Toto je stránka, kterou uživatelé uvidí, když se rozhodnete další informace kliknutím na notifiication. Vaše scénáře by měla vypadat takto:
   
    ![][IOS4]
6. Klikněte na **Domů View Controller** scénáře a ujistěte se, že má **homeViewController** jako jeho **vlastní třída** a **Storyboard ID**pod Identity inspector.
7. Totéž proveďte pro bitovou kopii řadiče zobrazení jako **imageViewController**.
8. Pak vytvořte novou třídu řadiče zobrazení s názvem **imageViewController** pro zpracování uživatelského rozhraní, kterou jste právě vytvořili.
9. V **imageViewController.h**, přidejte následující deklarace rozhraní kontroleru. Nezapomeňte řízení přetažení z bitové kopie zobrazení scénáře pro tyto vlastnosti k propojení dvou:
   
        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;
10. V **imageViewController.m**, přidejte následující na konci **viewDidload**:
    
        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];
11. V **AppDelegate.m**, import řadičem bitové kopie, který jste vytvořili:
    
        #import "imageViewController.h"
12. Přidáte oddíl rozhraní s následující prohlášení:
    
        @interface AppDelegate ()
    
        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;
    
        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;
    
        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;
    
        @end
13. V **AppDelegate**, ujistěte se, že vaše aplikace se zaregistruje pro tichou oznámení v **aplikace: didFinishLaunchingWithOptions**:
    
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

1. Nahraďte následující implementace pro **aplikace: didRegisterForRemoteNotificationsWithDeviceToken** provést storyboard uživatelského rozhraní se změní v úvahu:
   
       // Access navigation controller which is at the root of window
       UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
       // Get home view controller from stack on navigation controller
       homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
       hvc.deviceToken = deviceToken;
2. Pak přidejte následující metody, které **AppDelegate.m** načíst obrázek z váš koncový bod a odesílat místního oznámení po dokončení načítání. Ujistěte se, zda jste nahraďte zástupný symbol `{backend endpoint}` s back-end koncový bod:
   
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
3. Zpracování místního oznámení nad otevřením řadiče zobrazení bitové kopie v **AppDelegate.m** pomocí následujících metod:
   
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
1. V XCode spusťte aplikaci na fyzickém zařízení iOS (nabízených oznámení nebude fungovat v simulátoru).
2. V aplikaci pro iOS uživatelského rozhraní, zadejte uživatelské jméno a heslo na stejnou hodnotu pro ověřování a klikněte na tlačítko **protokolu v**.
3. Klikněte na tlačítko **odeslat nabízené** a měli byste vidět výstrahu v aplikaci. Pokud kliknete na **Další**, je přesměrován zpět na bitovou kopii jste zvolili pro zahrnutí do back-end aplikace.
4. Můžete také kliknout na **odeslat nabízené** a okamžitě stiskněte tlačítko Domů vašeho zařízení. Ve chvíli obdržíte nabízená oznámení. Pokud klepněte na něm nebo klikněte na tlačítko Další, můžete uvede do vaší aplikace a obsah bohaté image.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
