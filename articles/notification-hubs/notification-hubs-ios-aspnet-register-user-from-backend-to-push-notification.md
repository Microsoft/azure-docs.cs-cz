---
title: Registrace aktuálního uživatele pro nabízená oznámení pomocí webového rozhraní API | Microsoft Docs
description: Naučte se, jak vyžádat registraci nabízených oznámení v aplikaci pro iOS pomocí Azure Notification Hubs, když ASP.NET webové rozhraní API provádí registraci.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 3fec04a1a45f8b154e27a1e5303e44111f4cb421
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211868"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registrace aktuálního uživatele pro nabízená oznámení pomocí ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Přehled

V tomto tématu se dozvíte, jak vyžádat registraci nabízených oznámení v Azure Notification Hubs, když ASP.NET webové rozhraní API provádí registraci. Toto téma se týká kurzu [Informování uživatelů pomocí Notification Hubs]. Abyste mohli vytvořit ověřenou mobilní službu, musíte už v tomto kurzu provést požadované kroky. Další informace o scénáři informování uživatelů najdete v tématu informování [Informování uživatelů pomocí Notification Hubs].

## <a name="update-your-app"></a>Aktualizace aplikace

1. V MainStoryboard_iPhone. scénáři přidejte následující komponenty z knihovny objektů:

   * **Popisek**: "Vložení do uživatele s Notification Hubs"
   * **Popisek**: "InstallationId"
   * **Popisek**: Uživatelský
   * **Textové pole**: Uživatelský
   * **Popisek**: Zadáno
   * **Textové pole**: Zadáno
   * **Tlačítko**: Hlas

     V tomto okamžiku váš scénář vypadá následovně:

     ![][0]

2. V editoru pomocníka vytvořte pro všechny přepínací ovládací prvky možnost vzdálení a zavolejte je, připojte textová pole pomocí kontroleru zobrazení (delegát) a vytvořte **akci** pro tlačítko pro **přihlášení** .

    ![][1]

    Váš soubor BreakingNewsViewController. h by teď měl obsahovat následující kód:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Vytvořte třídu s názvem `DeviceInfo`a zkopírujte následující kód do části rozhraní souboru DeviceInfo. h:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Zkopírujte následující kód do části implementace v souboru DeviceInfo. m:

    ```objc
    @synthesize installationId = _installationId;

    - (id)init {
        if (!(self = [super init]))
            return nil;

        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
        if(!_installationId) {
            CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
            _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
            CFRelease(newUUID);

            //store the install ID so we don't generate a new one next time
            [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
            [defaults synchronize];
        }

        return self;
    }

    - (NSString*)getDeviceTokenInHex {
        const unsigned *tokenBytes = [[self deviceToken] bytes];
        NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
        return hexToken;
    }
    ```

5. V PushToUserAppDelegate. h přidejte následující vlastnost singleton:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. `didFinishLaunchingWithOptions` Do metody v PushToUserAppDelegate. m přidejte následující kód:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    První řádek inicializuje `DeviceInfo` typ singleton. Druhý řádek spustí registraci pro nabízená oznámení, která už existuje, pokud jste už dokončili kurz Začínáme [Začínáme s Notification Hubs] .
7. V PushToUserAppDelegate. m Implementujte metodu `didRegisterForRemoteNotificationsWithDeviceToken` ve své AppDelegate a přidejte následující kód:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Tím se nastaví token zařízení pro požadavek.

   > [!NOTE]
   > V tomto okamžiku by neměl být v této metodě žádný jiný kód. Pokud již máte volání `registerNativeWithDeviceToken` metody, která byla přidána po dokončení kurzu [Začínáme s Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md) , je nutné přidat nebo odebrat toto volání.

8. `PushToUserAppDelegate.m` Do souboru přidejte následující metodu obslužné rutiny:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Tato metoda zobrazí v uživatelském rozhraní výstrahu, když vaše aplikace obdrží oznámení, když je spuštěná.

9. `PushToUserViewController.m` Otevřete soubor a vraťte klávesnici v následující implementaci:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. V metodě v souboru inicializujte `installationId` popisek následujícím způsobem: `PushToUserViewController.m` `viewDidLoad`

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. V rozhraní `PushToUserViewController.m`přidejte následující vlastnosti:

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Pak přidejte následující implementaci:

    ```objc
    - (NSOperationQueue *)downloadQueue {
        if (!_downloadQueue) {
            _downloadQueue = [[NSOperationQueue alloc] init];
            _downloadQueue.name = @"Download Queue";
            _downloadQueue.maxConcurrentOperationCount = 1;
        }
        return _downloadQueue;
    }

    // base64 encoding
    - (NSString*)base64forData:(NSData*)theData
    {
        const uint8_t* input = (const uint8_t*)[theData bytes];
        NSInteger length = [theData length];

        static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

        NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
        uint8_t* output = (uint8_t*)data.mutableBytes;

        NSInteger i;
        for (i=0; i < length; i += 3) {
            NSInteger value = 0;
            NSInteger j;
            for (j = i; j < (i + 3); j++) {
                value <<= 8;

                if (j < length) {
                    value |= (0xFF & input[j]);
                }
            }

            NSInteger theIndex = (i / 3) * 4;
            output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
            output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
            output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
            output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
        }

        return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
    }
    ```

13. Zkopírujte následující kód do `login` metody obslužné rutiny vytvořené pomocí Xcode:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

    // build JSON
    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

    // build auth string
    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
    [request setHTTPMethod:@"POST"];
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

    // connect with POST
    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
        // add UIAlert depending on response.
        if (error != nil) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
            if ([httpResponse statusCode] == 200) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
            }
        } else {
            NSLog(@"error: %@", error);
        }
    }];
    ```

    Tato metoda získá ID instalace i kanál pro nabízená oznámení a pošle je spolu s typem zařízení na ověřenou metodu webového rozhraní API, která vytvoří registraci v Notification Hubs. Toto webové rozhraní API bylo definováno v programu informování [Informování uživatelů pomocí Notification Hubs].

Teď, když se klientská aplikace aktualizovala, se vraťte k informování [Informování uživatelů pomocí Notification Hubs] a aktualizujte mobilní službu, aby odesílala oznámení pomocí Notification Hubs.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Informování uživatelů pomocí Notification Hubs]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Začínáme s Notification Hubs]: notification-hubs-ios-apple-push-notification-apns-get-started.md
