---
title: Nabízená oznámení odesílaná konkrétním uživatelům pomocí Azure Notification Hubs | Dokumentace Microsoftu
description: Zjistěte, jak pomocí služby Azure Notification Hubs posílat nabízená oznámení konkrétním uživatelům.
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 4ecac47de08b458eac375f8f5e774c396aeb2f5d
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448104"
---
# <a name="tutorial-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Kurz: Nabízená oznámení odesílaná konkrétním uživatelům pomocí Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

V tomto kurzu se dozvíte, jak pomocí služby Azure Notification Hubs posílat nabízená oznámení konkrétním uživatelům aplikace na konkrétním zařízení. Back-endu ASP.NET WebAPI se používá k ověřování klientů a generování oznámení, jak je znázorněno v tomto tématu pokyny [registrace z back-endu aplikace](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření projektu WebAPI
> * Ověřování klientů v back-endu WebAPI
> * Registrace oznámení pomocí back-endu WebAPI
> * Odesílání oznámení z back-endu WebAPI
> * Publikování nového back-endu WebAPI
> * Upravit aplikaci pro iOS
> * Testování aplikace

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že jste vytvořili a konfiguraci centra oznámení, jak je popsáno v [Začínáme se službou Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Tento kurz je také předpokladem pro [zabezpečení Push (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) kurzu.
Pokud chcete použít jako back-end službu Mobile Apps, najdete v článku [Mobile Apps Začínáme s nabízenými oznámeními](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Upravit aplikaci pro iOS

1. Otevřete zobrazení jednostránkové aplikace vytvořené v [Začínáme se službou Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) kurzu.

   > [!NOTE]
   > V této části se předpokládá, že váš projekt je nakonfigurovaný s názvem prázdné organizace. V opačném případě budete muset název vaší organizace na všechny názvy tříd.

2. V `Main.storyboard` přidejte zobrazené na snímku obrazovky z objektu knihovny součásti.

    ![Upravit storybard v Tvůrci rozhraní Xcode][1]

   * **Uživatelské jméno**: A UITextField zástupným textem *zadejte uživatelské jméno*bezprostředně pod odeslat výsledky označovat popisky a omezené na levý a pravý okraj a pod popisek výsledky odeslat.
   * **Heslo**: A UITextField zástupným textem *zadat heslo*, bezprostředně pod uživatelské jméno textové pole a omezené na levý a pravý okraj a pod textové pole uživatelského jména. Zkontrolujte **zabezpečený vstupní Text** možnost v inspektoru atributu v části *vrátit klíč*.
   * **Přihlaste se**: Tlačítka uživatelského rozhraní s popiskem bezprostředně pod textové pole hesla a zrušte zaškrtnutí políčka **povoleno** možnost v inspektoru atributy v části *obsah ovládacího prvku*
   * **WNS**: Popisek a přepnout na umožnit odesílání oznámení služby oznámení Windows, pokud je nastavena na rozbočovači. Zobrazit [Začínáme se službou Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) kurzu.
   * **GCM**: Popisek a přepnout na povolení odesílání oznámení do Google Cloud Messaging, pokud je nastavena v centru. Zobrazit [Začínáme s Androidem](notification-hubs-android-push-notification-google-gcm-get-started.md) kurzu.
   * **APNS**: Popisek a přepnete se do umožnit odesílání oznámení do služby Apple Notification platformy.
   * **Příjemce Username:A** UITextField zástupným textem *značky uživatelského jména příjemců*, bezprostředně pod služby GCM označovat popisky a omezené na levý a pravý okraj a pod popisek služby GCM.

    Byly přidány některé součásti [Začínáme se službou Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) kurzu.

3. **CTRL** přetáhnout z komponenty v zobrazení `ViewController.h` a přidejte tyto nové výstupy.

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

4. V `ViewController.h`, přidejte následující `#define` po příkazech pro import. Náhradní `<Enter Your Backend Endpoint>` zástupný symbol s cílovou adresu URL jste použili k nasazení back-endu aplikace v předchozí části. Příklad: *http://your_backend.azurewebsites.net*.

    ```objc
    #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```

5. V projektu, vytvořte novou třídu Cocoa Touch s názvem `RegisterClient` rozhraní s ASP.NET back endu jste vytvořili. Vytvořte třídu, která dědí z `NSObject`. Pak přidejte následující kód `RegisterClient.h`.

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. V `RegisterClient.m`, aktualizujte `@interface` části:

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

7. Nahradit `@implementation` kapitoly RegisterClient.m následujícím kódem:

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

    Tento kód implementuje logiku popsaných v článku pokyny [registrace z back-endu aplikace](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) provést REST pomocí NSURLSession volání do back-endu aplikace a vrácené NSUserDefaults místně uložit registrationId Centrum oznámení.

    Tato třída vyžaduje vlastnost `authorizationHeader` nastavit, aby vše správně fungovalo. Tato vlastnost nastavena `ViewController` třídy po přihlášení.

8. V `ViewController.h`, přidejte `#import` příkaz pro `RegisterClient.h`. Pak přidejte deklaraci pro token zařízení a odkazy na `RegisterClient` instance v `@interface` části:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. V ViewController.m, přidejte deklaraci privátní metody v `@interface` části:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Následující fragment kódu není schéma zabezpečeného ověřování, musíte nahradit provádění `createAndSetAuthenticationHeaderWithUsername:AndPassword:` s vaší konkrétní ověřovací mechanismus, který vygeneruje ověřovací token využívat třída registru klienta, například OAuth, služby Active Directory.

10. Pak v `@implementation` část `ViewController.m`, přidejte následující kód, který se přidá implementace pro nastavení hlavičce tokenu a ověření zařízení.

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

    Všimněte si, jak nastavení token zařízení povolí tlačítko přihlásit. Je to proto, že jako součást přihlášení akci, kontroler zobrazení zaregistruje pro nabízená oznámení s back-endu aplikace. Proto nechcete, aby akce přihlášení být přístupné, dokud token zařízení je správně nastavený. Přihlášení z registrace nabízených oznámení můžete oddělit jako první se provede před ten.

11. V ViewController.m, použijte následující fragmenty kódu pro implementaci metody akce pro vaše **přihlásit** tlačítko a metody k odeslání zprávy oznámení pomocí back-endu ASP.NET.

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

12. Akce pro aktualizace **odeslat oznámení** tlačítko použít back-endu ASP.NET a odesílat na jakékoli systému oznámení platformy, zajišťuje přepínač.

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

13. V `ViewDidLoad` funkci, přidejte následující příkaz pro vytvoření instance `RegisterClient` instance a nastavit delegáta pro textová pole.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Teď v `AppDelegate.m`, odeberte všechny obsah metody `application:didRegisterForPushNotificationWithDeviceToken:` a nahraďte ho následujícím (aby se zajistilo, že kontroler zobrazení obsahuje nejnovější token zařízení získaných APNs):

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

15. Nakonec v `AppDelegate.m`, ujistěte se, že máte následující metodu:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testování aplikace

1. V XCode spusťte aplikaci na fyzickém zařízení iOS (oznámení nebude fungovat v simulátoru push).
2. V uživatelském rozhraní aplikací iOS zadejte stejnou hodnotu pro uživatelské jméno a heslo. Pak klikněte na tlačítko **přihlásit**.

    ![testování aplikace s iOS][2]

3. Měli byste vidět automaticky otevírané okno vás informuje o úspěšné registraci. Klikněte na **OK**.

    ![iOS testovací oznámení zobrazí][3]

4. V **značky uživatelského jména příjemců* textové pole, zadejte název značky uživatele použít s registrací z jiného zařízení.
5. Zadejte zprávu oznámení a klikněte na tlačítko **odeslat oznámení**. Jenom zařízení, které mají registrace se značkou jméno příjemce uživatele zpráva oznámení. To je odeslán, pouze na tyto uživatele.

    ![iOS testovací příznakem oznámení][4]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak posílat nabízená oznámení konkrétním uživatelům, k jejichž registracím jsou přidružené značky. V dalším kurzu se dozvíte, jak posílat nabízená oznámení na základě polohy: 

> [!div class="nextstepaction"]
>[Odesílání oznámení na základě polohy](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
