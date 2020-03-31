---
title: Odesílání nabízených oznámení konkrétním uživatelům pomocí center oznámení Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak pomocí služby Azure Notification Hubs posílat nabízená oznámení konkrétním uživatelům.
documentationcenter: ios
author: sethm
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 48135ea614bbab4ca6649a83895ae5f632918c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72387477"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Kurz: Odesílání nabízených oznámení konkrétním uživatelům pomocí center oznámení Azure

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

V tomto kurzu se dozvíte, jak pomocí služby Azure Notification Hubs posílat nabízená oznámení konkrétním uživatelům aplikace na konkrétním zařízení. Back-end ASP.NET WebAPI se používá k ověřování klientů a generování oznámení, jak je znázorněno v tématu pokyny [Registrace z back-endu aplikace](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření projektu WebAPI
> * Ověřování klientů v back-endu WebAPI
> * Registrace k oznámením pomocí back-endu WebAPI
> * Odesílání oznámení z back-endu WebAPI
> * Publikování nového back-endu WebAPI
> * Úprava aplikace pro iOS
> * Testování aplikace

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že jste vytvořili a nakonfigurovali centrum oznámení, jak je popsáno v [začínáme s centry oznámení (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Tento kurz je také předpokladem kurzu [secure push (iOS).](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
Pokud chcete používat mobilní aplikace jako back-endovou službu, přečtěte si [část Mobilní aplikace Začínáme pomocí funkce Push](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Úprava aplikace pro iOS

1. Otevřete aplikaci zobrazení jedné stránky, kterou jste vytvořili, v kurzu [Začínáme s centry oznámení (iOS).](notification-hubs-ios-apple-push-notification-apns-get-started.md)

   > [!NOTE]
   > Tato část předpokládá, že projekt je nakonfigurován s prázdným názvem organizace. Pokud ne, je třeba předřadit název organizace na všechny názvy tříd.

2. Do `Main.storyboard` souboru přidejte komponenty zobrazené na snímku obrazovky z knihovny objektů.

    ![Úprava scénáře v nástroji tvůrce rozhraní Xcode][1]

   * **Uživatelské jméno**: UITextField se zástupným textem, *Zadejte uživatelské jméno*, bezprostředně pod popiskem výsledků odeslání a omezen na levý a pravý okraj a pod popisek výsledků odeslání.
   * **Heslo**: UITextField se zástupným *textem, Zadejte heslo*, bezprostředně pod textovým polem uživatelského jména a je omezen na levý a pravý okraj a pod textovým polem uživatelského jména. Zaškrtněte možnost **Zabezpečený text** v inspektoru atributů v části *Návratový klíč*.
   * **Přihlásit se**: Tlačítko UIButton označené bezprostředně pod textovým polem hesla a zrušit zaškrtnutí **políčka Povoleno** v Inspektoru atributů v části *Obsah ovládacího prvku*
   * **WNS**: Popisek a přepínač pro povolení odesílání oznámení Služby oznámení Systému windows oznámení, pokud byla nastavena v centru. Podívejte se na kurz [Začínáme s Windows.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
   * **GCM**: Označení a přepnutí umožňuje odesílání oznámení do služby Google Cloud Messaging, pokud bylo nastaveno v centru. Viz Kurz [Začínáme s Androidem.](notification-hubs-android-push-notification-google-gcm-get-started.md)
   * **APNS**: Označení a přepnutí umožňuje odeslání oznámení službě oznámení platformy Apple.
   * **Uživatelské jméno příjemce:UITextField** se zástupným textem, *Značka uživatelského jména příjemce*, bezprostředně pod popiskem GCM a omezen na levý a pravý okraj a pod popiskem GCM.

     Některé součásti byly přidány v [kurzu Začínáme s centry oznámení (iOS).](notification-hubs-ios-apple-push-notification-apns-get-started.md)

3. **Ctrl** přetáhněte ze součástí v `ViewController.h` zobrazení a přidejte tyto nové výstupy.

    ```objc
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

4. V `ViewController.h`aplikaci `#define` přidejte za příkazy importu následující. Nahraďte `<Enter Your Backend Endpoint>` zástupný symbol cílovou adresou URL, kterou jste použili k nasazení back-endu aplikace v předchozí části. Například, `http://your_backend.azurewebsites.net`.

    ```objc
    #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```

5. V projektu vytvořte novou třídu Cocoa Touch s názvem `RegisterClient` rozhraní s ASP.NET back-endem, který jste vytvořili. Vytvořte třídu `NSObject`dědící z . Potom přidejte následující `RegisterClient.h`kód do .

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. V `RegisterClient.m`části `@interface` aktualizujte:

    ```objc
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

7. Nahraďte `@implementation` oddíl v souboru RegisterClient.m následujícím kódem:

    ```objc
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

    Tento kód implementuje logiku vysvětlenou v článku [Pokyny Registrace z back-endu aplikace](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) pomocí NSURLSession k provedení volání REST do back-endu aplikace a NSUserDefaults místně ukládat id registrace vrácené centrem oznámení.

    Tato třída vyžaduje, aby její vlastnost `authorizationHeader` byla nastavena, aby fungovala správně. Tato vlastnost je `ViewController` nastavena třídou po přihlášení.

8. V `ViewController.h`souboru `#import` přidejte příkaz pro . `RegisterClient.h` Pak přidejte deklaraci tokenu zařízení `RegisterClient` a `@interface` odkaz na instanci v části:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. V ViewController.m přidejte deklaraci `@interface` soukromé metody v části:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Následující fragment není zabezpečené schéma ověřování, měli byste nahradit implementaci `createAndSetAuthenticationHeaderWithUsername:AndPassword:` mechanismu specifického ověřování, který generuje ověřovací token, který má být spotřebován třídou klienta registru, například OAuth, Active Directory.

10. Pak v `@implementation` části `ViewController.m`, přidejte následující kód, který přidá implementaci pro nastavení tokenu zařízení a ověřovací hlavičky.

    ```objc
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

    Všimněte si, jak nastavení tokenu zařízení umožňuje přihlašovací tlačítko. Je to proto, že jako součást akce přihlášení, kontrolor zobrazení registruje pro nabízená oznámení s back-endem aplikace. Proto nechcete, aby akce Přihlášení byla přístupná, dokud nebude správně nastaven token zařízení. Přihlášení můžete oddělit od push registrace tak dlouho, dokud se stane první před druhou.

11. V ViewController.m použijte následující úryvky k implementaci metody akce pro tlačítko **Přihlásit** se a metodu pro odeslání oznámení pomocí ASP.NET back-endu.

    ```objc
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

12. Aktualizujte akci tlačítka **Odeslat oznámení** tak, aby používala ASP.NET back-end a odesílala do libovolného pns povoleného přepínačem.

    ```objc
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

13. Do `ViewDidLoad` funkce přidejte následující k vytvoření `RegisterClient` instance a nastavte delegáta pro textová pole.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Nyní `AppDelegate.m`v , odeberte `application:didRegisterForPushNotificationWithDeviceToken:` veškerý obsah metody a nahraďte ji následujícím (chcete-li se ujistit, že řadič zobrazení obsahuje nejnovější token zařízení načtený z APNs):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Nakonec `AppDelegate.m`v , ujistěte se, že máte následující metodu:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testování aplikace

1. V XCode spusťte aplikaci na fyzickém zařízení se systémem iOS (nabízená oznámení nefungují v simulátoru).
2. V uživatelském rozhraní aplikace pro iOS zadejte stejnou hodnotu pro uživatelské jméno i heslo. Potom klepněte na tlačítko **Přihlásit se**.

    ![testovací aplikace pro iOS][2]

3. Měli byste vidět pop-up informující o úspěchu registrace. Klikněte na tlačítko **OK**.

    ![Zobrazeno oznámení o testu iOS][3]

4. Do textového pole ** Příjemce uživatelského jména zadejte* značku uživatelského jména použitou při registraci z jiného zařízení.
5. Zadejte oznámení a klepněte na tlačítko **Odeslat oznámení**. Oznámení obdrží pouze zařízení, která mají registraci u značky uživatelského jména příjemce. Je odeslána pouze těmto uživatelům.

    ![Oznámení označené testem iOS][4]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak posílat nabízená oznámení konkrétním uživatelům, k jejichž registracím jsou přidružené značky. V dalším kurzu se dozvíte, jak posílat nabízená oznámení na základě polohy: 

> [!div class="nextstepaction"]
>[Odesílání oznámení na základě polohy](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
