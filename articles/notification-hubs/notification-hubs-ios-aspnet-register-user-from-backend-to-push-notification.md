---
title: Registrace aktuálního uživatele pro nabízená oznámení pomocí webového rozhraní API | Microsoft Docs
description: Zjistěte, jak požádat o registraci nabízených oznámení v aplikaci pro iOS pomocí Azure Notification Hubs, když registrace se provádí pomocí rozhraní ASP.NET Web API.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c43c15131afb5fbf346b0137dac566f5331c65a2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776368"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registrace aktuálního uživatele pro nabízená oznámení pomocí technologie ASP.NET
> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
> 
> 

## <a name="overview"></a>Přehled
Toto téma ukazuje, jak požádat o registraci nabízených oznámení pomocí Azure Notification Hubs při registraci se provádí pomocí rozhraní ASP.NET Web API. Toto téma rozšiřuje kurzu [upozorněte uživatele s Notification Hubs]. Musí již dokončení požadovaných kroků v tomto kurzu k vytvoření ověřené mobilní služby. Další informace o scénář uživatelé oznámení najdete v tématu [upozorněte uživatele s Notification Hubs].

## <a name="update-your-app"></a>Aktualizace aplikace
1. V MainStoryboard_iPhone.storyboard přidejte následující součásti z objektu knihovny:
   
   * **Popisek**: "Push pro uživatele s centry oznámení"
   * **Popisek**: "InstallationId"
   * **Popisek**: "User"
   * **Textové pole**: "User"
   * **Popisek**: "Password"
   * **Textové pole**: "Password"
   * **Tlačítko**: "Přihlášení"
     
     V tomto okamžiku by váš scénáře vypadá takto:
     
      ![][0]
2. V editoru pomocníka vytvořit výstupy vypnuté ovládacích prvků a volat, připojit textových polí s řadiče zobrazení (delegát) a vytvořte **akce** pro **přihlášení** tlačítko.
   
       ![][1]
   
       Your BreakingNewsViewController.h file should now contain the following code:
   
        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;
   
        - (IBAction)login:(id)sender;
3. Vytvoření třídy s názvem **DeviceInfo**a zkopírujte následující kód do části rozhraní souboru DeviceInfo.h:
   
        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;
4. Zkopírujte následující kód v oddílu implementace DeviceInfo.m souboru:
   
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
5. V PushToUserAppDelegate.h přidejte následující singleton vlastnost:
   
        @property (strong, nonatomic) DeviceInfo* deviceInfo;
6. V **didFinishLaunchingWithOptions** metoda v PushToUserAppDelegate.m, přidejte následující kód:
   
        self.deviceInfo = [[DeviceInfo alloc] init];
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
   
    Inicializuje první řádek **DeviceInfo** typu singleton. Druhý řádek spustí registrace pro nabízená oznámení, který je již přítomen, pokud jste již dokončili [Začínáme s Notification Hubs] kurzu.
7. V PushToUserAppDelegate.m, implementovat metodu **didRegisterForRemoteNotificationsWithDeviceToken** ve vaší AppDelegate a přidejte následující kód:
   
        self.deviceInfo.deviceToken = deviceToken;
   
    Toto nastaví token zařízení pro daný požadavek.
   
   > [!NOTE]
   > V tomto okamžiku by neměly existovat jiný kód v této metodě. Pokud již máte volání **registerNativeWithDeviceToken** metoda, která byla přidána po dokončení [Začínáme s Notification Hubs](/manage/services/notification-hubs/get-started-notification-hubs-ios/) kurz, je nutné okomentujte nebo odebrat toto volání.
   > 
   > 
8. V souboru PushToUserAppDelegate.m přidejte následující metodu obslužné rutiny:
   
   * (void) aplikace:(UIApplication *) aplikace didReceiveRemoteNotification:(NSDictionary *) informací o uživateli {NSLog (@"% @", informací o uživateli);   UIAlertView * výstraha = [initWithTitle [UIAlertView alokační]:@"Notification" zpráva: [objectForKey informací o uživateli:@"inAppMessage"] delegáta: nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];   [Zobrazit výstrahy]; }
   
   Tato metoda zobrazí výstrahu v uživatelském rozhraní, když vaše aplikace obdrží oznámení, když je spuštěná.
9. Otevřete soubor PushToUserViewController.m a vrátí klávesnice v následujících implementace:
   
        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }
10. V **viewDidLoad** metoda v souboru PushToUserViewController.m inicializovat popisek installationId následujícím způsobem:
    
         DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
         Self.installationId.text = deviceInfo.installationId;
11. V rozhraní v PushToUserViewController.m přidejte následující vlastnosti:
    
        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;
12. Pak přidejte následující implementace:
    
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
13. Zkopírujte následující kód do **přihlášení** vytvořené XCode metoda obslužné rutiny:
    
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
    
    Tato metoda získá ID instalace a kanál pro nabízená oznámení a odešle ji, společně s typu zařízení, do ověřené metody webového rozhraní API, která vytvoří registraci v Notification Hubs. Toto webové rozhraní API byla definovaná v [upozorněte uživatele s Notification Hubs].

Teď, když klientské aplikace se aktualizovalo, vraťte se do [upozorněte uživatele s Notification Hubs] a aktualizovat mobilní službu pro odeslání oznámení pomocí centra oznámení.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[upozorněte uživatele s Notification Hubs]: /manage/services/notification-hubs/notify-users-aspnet

[Začínáme s Notification Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-ios
