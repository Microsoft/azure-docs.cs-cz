---
title: Posílání nabízených oznámení konkrétním uživatelům pomocí Azure Notification Hubs | Microsoft Docs
description: Přečtěte si, jak odesílat nabízená oznámení konkrétním uživatelům iOS pomocí Azure Notification Hubs.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 167c666c536ee33531fd069dbd1edb530331a9f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302185"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Kurz: odeslání nabízených oznámení konkrétním uživatelům pomocí Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

V tomto kurzu se dozvíte, jak pomocí služby Azure Notification Hubs posílat nabízená oznámení konkrétním uživatelům aplikace na konkrétním zařízení. Back-end ASP.NET WebAPI se používá k ověřování klientů a generování oznámení, jak je znázorněno v tématu s pokyny k [registraci z back-endu vaší aplikace](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

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

V tomto kurzu se předpokládá, že jste vytvořili a nakonfigurovali centrum oznámení, jak je popsáno v tématu [Posílání nabízených oznámení do aplikací pro iOS pomocí Azure Notification Hubs](ios-sdk-get-started.md). Tento kurz je také předpokladem pro kurz [zabezpečeného nabízení oznámení (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) .
Pokud chcete jako back-end službu použít Mobile Apps, přečtěte si téma [Mobile Apps Začínáme s nabízenými oznámeními](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Úprava aplikace pro iOS

1. Otevřete aplikaci pro zobrazení jedné stránky, kterou jste vytvořili v části [Posílání nabízených oznámení do aplikací pro iOS pomocí Azure Notification Hubs](ios-sdk-get-started.md) kurzu.

   > [!NOTE]
   > V této části se předpokládá, že váš projekt je nakonfigurovaný s prázdným názvem organizace. Pokud ne, přiřaďte název vaší organizace všem názvům tříd.

2. V `Main.storyboard` souboru přidejte komponenty zobrazené na snímku obrazovky z knihovny objektů.

    ![Upravit scénář v Tvůrci rozhraní Xcode][1]

   * **Uživatelské jméno**: UITextField se zástupným textem, *Zadejte uživatelské jméno*, hned pod popiskem odeslat výsledky a omezením na levý a pravý okraj a pod popiskem odeslat výsledky.
   * **Heslo**: UITextField se zástupným textem, *Zadejte heslo*hned pod textové pole uživatelské jméno a omezené na levý a pravý okraj a pod textovým polem username (uživatelské jméno). V inspektoru atributů v části *návratový klíč*zaškrtněte políčko **Zabezpečená textová položka** .
   * **Přihlásit**: UIButton označený hned pod textovým polem heslo a zrušit kontrolu možnosti **Enabled** v inspektoru atributů v části *řízení-obsah*
   * **WNS**: Label a Switch povolí odeslání služby oznamování systému Windows oznámení, pokud byla nastavena v centru. Přečtěte si kurz [Windows Začínáme](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
   * **GCM**: Label a Switch povolí odesílání oznámení Google Cloud Messaging, pokud bylo nastaveno v centru. Viz kurz k [androidu Začínáme](notification-hubs-android-push-notification-google-gcm-get-started.md) .
   * **APN**: popisek a přepínač, aby bylo možné povolit odesílání oznámení do služby oznámení platformy Apple.
   * **Uživatelské jméno příjemce:** UITextField se zástupným textem, *značkou uživatelského jména příjemce*, hned pod popiskem GCM a omezeným na levý a pravý okraj a pod popiskem GCM.

     Některé součásti byly přidány do [aplikací pro iOS pomocí služby Azure Notification Hubs kurz pro posílání nabízených oznámení do aplikací pro iOS](ios-sdk-get-started.md) .

3. **CTRL** přetáhněte ze součástí zobrazení na `ViewController.h` a přidejte tyto nové možnosti:

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

4. V aplikaci `ViewController.h` přidejte následující `#define` po příkazech import. `<Your backend endpoint>`Zástupný symbol nahraďte cílovou adresou URL, kterou jste použili k nasazení back-endu aplikace v předchozí části. Například `http://your_backend.azurewebsites.net` :

    ```objc
    #define BACKEND_ENDPOINT @"<Your backend endpoint>"
    ```

5. V projektu vytvořte novou třídu pro kakaový touch s názvem `RegisterClient` k rozhraní s back-end ASP.NET, který jste vytvořili. Vytvoří třídu děděnou z `NSObject` . Pak přidejte následující kód do `RegisterClient.h` :

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. V části `RegisterClient.m` aktualizujte `@interface` část:

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

7. Nahraďte `@implementation` oddíl v RegisterClient. m následujícím kódem:

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

    Tento kód implementuje logiku vysvětlenou v článku s pokyny, který se [registruje z back-endu vaší aplikace](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) pomocí NSURLSession k provádění volání REST do back-endu aplikace a NSUserDefaults pro místní ukládání registrationId vráceného centrem oznámení.

    Aby tato třída `authorizationHeader` fungovala správně, musí být nastavená její vlastnost. Tato vlastnost je nastavena `ViewController` třídou po přihlášení.

8. V `ViewController.h` přidejte `#import` příkaz pro `RegisterClient.h` . Pak přidejte deklaraci tokenu zařízení a odkaz na `RegisterClient` instanci v `@interface` části:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. V soubor viewcontroller. m přidejte do oddílu deklaraci privátní metody `@interface` :

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Následující fragment kódu není schématem zabezpečeného ověřování. měli byste nahradit implementaci    `createAndSetAuthenticationHeaderWithUsername:AndPassword:` pomocí konkrétního ověřovacího mechanismu, který vygeneruje ověřovací token, který má být využíván registrací třídy klienta, například OAuth, Active Directory.

10. Pak v `@implementation` části `ViewController.m` přidejte následující kód, který přidá implementaci pro nastavení tokenu zařízení a záhlaví ověřování.

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

    Všimněte si, jak nastavení tokenu zařízení povolí tlačítko **Přihlásit** se. Je to proto, že jako součást akce přihlášení řadič zobrazení registruje pro nabízená oznámení pomocí back-endu aplikace. Nechcete, aby byla akce **přihlášení** k dispozici, dokud není správně nastavený token zařízení. Přihlášení můžete oddělit od registrace nabízených oznámení, dokud původní proběhne před ním.

11. V soubor viewcontroller. m použijte následující fragmenty kódu k implementaci metody Action pro tlačítko **Přihlásit** a metodu pro odeslání zprávy s oznámením pomocí back-endu ASP.NET.

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

12. Aktualizujte akci pro tlačítko **Odeslat oznámení** , abyste používali back-end ASP.NET a odesílali do všech PNS povolených přepínačem.

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

13. Ve `ViewDidLoad` funkci přidejte následující pro vytvoření instance `RegisterClient` instance a nastavte delegáta pro textová pole.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Nyní v `AppDelegate.m` nástroji odeberte veškerý obsah metody `application:didRegisterForPushNotificationWithDeviceToken:` a nahraďte ji následujícím (abyste se ujistili, že kontroler zobrazení obsahuje nejnovější token zařízení načtený ze služby APNs):

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

15. Nakonec se ujistěte `AppDelegate.m` , že máte následující metodu:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testování aplikace

1. V XCode spusťte aplikaci na fyzickém zařízení s iOS (nabízená oznámení nefungují v simulátoru).
2. V uživatelském rozhraní aplikace pro iOS zadejte pro uživatelské jméno a heslo stejnou hodnotu. Pak klikněte na **Přihlásit se**.

    ![testovací aplikace pro iOS][2]

3. Měla by se zobrazit automaticky otevírané okno informující o úspěšné registraci. Klikněte na **OK**.

    ![zobrazí se oznámení o testu iOS.][3]

4. V textovém poli **značka uživatelského jména příjemce* zadejte značku uživatelského jména, která se používá při registraci z jiného zařízení.
5. Zadejte zprávu oznámení a klikněte na **Odeslat oznámení**. Oznámení se dostanou jenom u zařízení, která mají registraci se značkou uživatelské jméno příjemce. Odesílá se pouze těmto uživatelům.

    ![oznámení s příznakem testu iOS][4]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak posílat nabízená oznámení konkrétním uživatelům, k jejichž registracím jsou přidružené značky. V dalším kurzu se dozvíte, jak posílat nabízená oznámení na základě polohy:

> [!div class="nextstepaction"]
>[Odesílání oznámení na základě polohy](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
