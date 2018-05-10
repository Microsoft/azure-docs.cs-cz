---
title: Nabízená oznámení pro konkrétní uživatele pomocí Azure Notification Hubs | Microsoft Docs
description: Zjistěte, jak odesílat nabízená oznámení pro konkrétní uživatele pomocí Azure Notification Hubs.
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/13/2018
ms.author: dimazaid
ms.openlocfilehash: 36d70c40e3de7bd38cdfc566da37060cdcea9060
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Kurz: Nabízená oznámení pro konkrétní uživatele pomocí Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

V tomto kurzu se dozvíte, jak se dají pomocí Azure Notification Hubs posílat nabízená oznámení specifickým uživatelům aplikace na specifickém zařízení. Backendu ASP.NET WebAPI slouží k ověřování klientů a ke generování oznámení, jak je znázorněno v tématu pokyny [registrace z back-end aplikace](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

V tomto kurzu proveďte následující kroky:

> [!div class="checklist"]
> * Vytvoření projektu WebAPI
> * Ověřování klientů na back-end WebAPI
> * Registrace pro oznámení pomocí back-end WebAPI
> * Odesílání oznámení z back-end WebAPI
> * Publikování nový back-end WebAPI
> * Upravit aplikaci s iOS
> * Testování aplikace

## <a name="prerequisites"></a>Požadavky
V tomto kurzu se předpokládá, že jste vytvořili a nakonfigurovali vaše Centrum oznámení, jak je popsáno v [Začínáme s Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). V tomto kurzu je také předpokladem pro [zabezpečení Push (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) kurzu.
Pokud chcete použít jako back-end služby Mobile Apps, najdete v článku [mobilní aplikace začít pracovat s nabízené](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Upravit aplikaci s iOS
1. Otevřete aplikaci zobrazení jednu stránku, kterou jste vytvořili v [Začínáme s Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) kurzu.
   
   > [!NOTE]
   > Této části se předpokládá, že váš projekt je nakonfigurovaný s názvem prázdné organizace. Pokud ne, budete muset předřazení název vaší organizace na všechny názvy tříd.
   > 
   > 
2. V `Main.storyboard`, přidejte součásti ukazuje snímek obrazovky z objektu knihovny.
   
    ![][1]
   
   * **Uživatelské jméno**: UITextField A s zástupný text *zadejte uživatelské jméno*bezprostředně pod odesílání výsledky popisku a omezená na levý a pravý okraj a pod návěští odesílání výsledky.
   * **Heslo**: UITextField A s zástupný text *zadejte heslo*, bezprostředně pod uživatelské jméno textové pole a omezené levý a pravý okraj a pod textové pole uživatelské jméno. Zkontrolujte **zabezpečení zadávání textu** možnost v atributu Inspector, v části *vrátit klíč*.
   * **Přihlaste se**: A UIButton s názvem bez přípony bezprostředně pod textové pole heslo a zrušte zaškrtnutí políčka **povoleno** možnost Inspector atributy, v části *obsahu ovládacího prvku*
   * **WNS**: popisek a přepínače, které umožňují odesílání oznámení služby oznámení Windows, pokud ji byla nastavena na rozbočovači. Najdete v článku [Windows Začínáme](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) kurzu.
   * **GCM**: popisek a přepínače, které umožňují odesílání oznámení pro Google Cloud Messaging, pokud ji byla nastavena na rozbočovači. V tématu [Začínáme Android](notification-hubs-android-push-notification-google-gcm-get-started.md) kurzu.
   * **APNS**: štítek a přepínače povolíte odesílání oznámení do služby Apple platformy oznámení.
   * **Příjemce Username:A** UITextField s zástupný text *značky uživatelské jméno příjemce*, bezprostředně pod GCM popisku a omezené levý a pravý okraj a pod popisek GCM.

    Některé součásti byly přidány v [Začínáme s Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) kurzu.

1. **CTRL** přetáhněte z komponenty v zobrazení ViewController.h a přidejte tyto nové výstupy.
   
    ```obj-c
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
        @property (weak, nonatomic) IBOutlet UITextField *NotificationField;
   
        // Used to enable the buttons on the UI
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;
   
        // Used to enabled sending notifications across platforms
        @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;
   
        - (IBAction)LogInAction:(id)sender;
    ```
2. V ViewController.h, přidejte následující `#define` po příkazy pro import. Nahraďte *< Zadejte koncový bod vašeho back-end\>*  zástupný symbol cílová adresa URL můžete použít k nasazení vašeho back-end aplikace v předchozí části. Například *http://you_backend.azurewebsites.net*.
   
    ```obj-c
        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```
3. V projektu, vytvořte novou **Touch kakao třída** s názvem **RegisterClient** rozhraní s ASP.NET back-end jste vytvořili. Vytvořte třídu, která dědí z `NSObject`. V RegisterClient.h přidejte následující kód.

    ```obj-c   
        @interface RegisterClient : NSObject
   
        @property (strong, nonatomic) NSString* authenticationHeader;
   
        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;
   
        -(instancetype) initWithEndpoint:(NSString*)Endpoint;
   
        @end
    ```
4. V `RegisterClient.m`, aktualizovat `@interface` části:

    ```obj-c   
        @interface RegisterClient ()
   
        @property (strong, nonatomic) NSURLSession* session;
        @property (strong, nonatomic) NSURLSession* endpoint;
   
        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion;
        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;
   
        @end
    ```
5. Nahraďte `@implementation` část v RegisterClient.m následujícím kódem:

    ```obj-c
        @implementation RegisterClient

        // Globals used by RegisterClient
        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

        -(instancetype) initWithEndpoint:(NSString*)Endpoint
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
                _endpoint = Endpoint;
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                    andCompletion:(void(^)(NSError*))completion
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description]
                stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                    uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
                completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                    tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                    if (error) {
                        completion(error);
                        return;
                    }

                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if (httpResponse.statusCode == 200) {
                        completion(nil);
                    } else if (httpResponse.statusCode == 410 && retry) {
                        [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                    } else {
                        NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                    @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                                options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                                encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                    registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                if (!error)
                {
                    completion(response, error);
                }
                else
                {
                    NSLog(@"Error request: %@", error);
                    completion(nil, error);
                }
            }];
            [dataTask resume];
        }

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                        objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                    _endpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data
                        encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                        [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId
                        forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        @end
    ```

    Tento kód implementuje logiku popsané v článku pokyny [registrace z back-end aplikace](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) pomocí NSURLSession k provedení REST zavolá na váš back-end aplikace a NSUserDefaults ukládat místně registrationId vrácené Centrum oznámení.

    Tato třída vyžaduje, aby jeho vlastnost **authorizationHeader** nastavit správné fungování. Tato vlastnost je nastavena **ViewController** třída po přihlášení.

1. V ViewController.h, přidejte `#import` příkaz pro RegisterClient.h. Pak přidejte deklaraci pro token zařízení a odkaz na `RegisterClient` instance v `@interface` části:

    ```obj-c   
        #import "RegisterClient.h"
   
        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;
    ```
2. V ViewController.m, přidejte deklaraci soukromá metoda v `@interface` části:
   
    ```obj-c
        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
   
        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
   
        @end
    ```

    > [!NOTE]
    > Následující fragment kódu není schéma zabezpečeného ověřování, doporučujeme nahradit implementace **createAndSetAuthenticationHeaderWithUsername:AndPassword:** s vaší konkrétní ověřovací mechanismus, který generuje ověřovací token pro registraci klienta třídu, například OAuth, služby Active Directory.
1. Potom v `@implementation` části `ViewController.m`, přidejte následující kód, který přidá implementaci nastavení hlavičku tokenu a ověření zařízení.
   
    ```obj-c
        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }
   
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];
   
            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];
   
            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                        encoding:NSUTF8StringEncoding];
        }
   
        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }
    ```
   
    Všimněte si, jak nastavení token zařízení povolí tlačítko přihlásit. Je to proto, že v rámci akce přihlášení se zaregistruje řadiče zobrazení pro nabízená oznámení s back-end aplikace. Proto nechcete, aby akce přihlášení být přístupné, dokud token zařízení správně nastavit. Přihlášení z registraci nabízených můžete oddělit tak dlouho, dokud první se stane před jeho.
2. V ViewController.m, použijte následující fragmenty implementovat metodu akce pro vaše **protokolu v** tlačítko a metody k odeslání zprávy oznámení pomocí ASP.NET back-end.
   
    ```obj-c
       - (IBAction)LogInAction:(id)sender {
           // create authentication header and set it in register client
           NSString* username = self.UsernameField.text;
           NSString* password = self.PasswordField.text;
   
           [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];
   
           __weak ViewController* selfie = self;
           [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
               andCompletion:^(NSError* error) {
               if (!error) {
                   dispatch_async(dispatch_get_main_queue(),
                   ^{
                       selfie.SendNotificationButton.enabled = YES;
                       [self MessageBox:@"Success" message:@"Registered successfully!"];
                   });
               }
           }];
       }

        - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                    Message:(NSString*)message
        {
            NSURLSession* session = [NSURLSession
                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
                delegateQueue:nil];

            // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
            NSURL* requestURL = [NSURL URLWithString:[NSString
                stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
                usernameTag]];

            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];

            // Get the mock authenticationheader from the register client
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                self.registerClient.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

            // Execute the send notification REST API on the ASP.NET Backend
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                        pns, httpResponse.statusCode, error];
                    dispatch_async(dispatch_get_main_queue(),
                    ^{
                        // Append text because all 3 PNS calls may also have information to view
                        [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                    });
                    NSLog(status);
                }

                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }
    ```

1. Akce pro aktualizace **odeslat oznámení** tlačítko pomocí ASP.NET back-end a poslat jakékoli PNS povoleno přepínač.

    ```obj-c
        - (IBAction)SendNotificationMessage:(id)sender
        {
            //[self SendNotificationRESTAPI];
            [self SendToEnabledPlatforms];
        }

        -(void)SendToEnabledPlatforms
        {
            NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

            [self.sendResults setText:@""];

            if ([self.WNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

            if ([self.GCMSwitch isOn])
                [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

            if ([self.APNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
        }
    ```

1. Ve funkci **ViewDidLoad**, přidejte následující instance RegisterClient instance a nastavit delegáta u textových polí.
   
    ```obj-c
       self.UsernameField.delegate = self;
       self.PasswordField.delegate = self;
       self.RecipientField.delegate = self;
       self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```
2. Nyní ve **AppDelegate.m**, odeberte všechny obsah metody `application:didRegisterForPushNotificationWithDeviceToken:` a nahraďte ji následujícím a ujistěte se, že řadiče zobrazení obsahuje nejnovější token zařízení načíst ze služby APN:
   
    ```obj-c
       // Add import to the top of the file
       #import "ViewController.h"
   
       - (void)application:(UIApplication *)application
                   didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
       {
           ViewController* rvc = (ViewController*) self.window.rootViewController;
           rvc.deviceToken = deviceToken;
       }
    ```
3. Nakonec v **AppDelegate.m**, zajistěte, aby byla následující metodu:

    ```obj-c   
       - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
           NSLog(@"%@", userInfo);
           [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
       }
    ```

## <a name="test-the-application"></a>Testování aplikace
1. V XCode spusťte aplikaci na fyzickém zařízení iOS (nabízených oznámení v simulátoru nefungují).
2. V aplikaci pro iOS uživatelského rozhraní zadejte stejnou hodnotu pro uživatelské jméno a heslo. Pak klikněte na tlačítko **protokolu v**.
   
    ![][2]
3. Měli byste vidět vyskakovací okno s informacemi o registraci úspěch. Klikněte na **OK**.
   
    ![][3]
4. V **značky uživatelské jméno příjemce* textové pole, zadejte název značky uživatele použít s registrací z jiného zařízení.
5. Zadejte zprávu oznámení a klikněte na tlačítko **odeslat oznámení**. Jenom zařízení, která mají registrace pomocí značky jméno příjemce uživatele přijímat oznámení. Pouze odesláním pro tyto uživatele.
   
    ![][4]

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak pro nabízená oznámení pro konkrétní uživatele, kteří mají značkám přidruženým jejich registrace. Informace o tom pro nabízená oznámení na základě polohy, přechodu na následující kurzu: 

> [!div class="nextstepaction"]
>[Nabízená oznámení na základě polohy](notification-hubs-push-bing-spartial-data-geofencing-notification.md)


[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
