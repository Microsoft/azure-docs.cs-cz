---
title: Azure Notification Hubs zabezpečené Push
description: Naučte se odesílání zabezpečené nabízených oznámení do aplikace pro iOS z Azure. Ukázky kódu jsou vytvořeny v Objective-C a C#.
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: d3ba967a164a35af5bf66f7e74d5f95b5dc2a37f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776198"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs zabezpečené Push
> [!div class="op_single_selector"]
> * [Univerzální pro Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Přehled
Podpora nabízená oznámení v Microsoft Azure umožňuje přístup k infrastruktuře snadno použitelnou, multiplatformní a upraveným nabízené, což výrazně zjednodušuje implementaci nabízená oznámení spotřebních a podnikových aplikací pro mobilní platformy.

Kvůli zákonným omezení zabezpečení, někdy aplikace může chtít zahrnout něco v oznámení, kterou nelze přenést prostřednictvím infrastrukturu pro standardní nabízená oznámení. Tento kurz popisuje, jak zajistit stejné prostředí posíláním důvěrných informací o prostřednictvím zabezpečeného a ověřené připojení mezi klientské zařízení a back-end aplikace.

Na vysoké úrovni tok je následující:

1. Back-end aplikace:
   * Zabezpečení datové úložiště v databázi back-end.
   * ID tohoto oznámení se odešle do zařízení (zabezpečené nebudou odeslány žádné informace).
2. Aplikace na zařízení, když obdrží oznámení:
   * Zařízení kontaktuje back-end vyžaduje zabezpečené datové části.
   * Aplikace můžete zobrazit datové části jako upozornění na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu) předpokládáme, že zařízení ukládá ověřovací token do místního úložiště, po přihlášení uživatele. Zaručí se tím uživatelům prostředí, protože zařízení můžete načíst pomocí tohoto tokenu zabezpečení datové na oznámení. Pokud vaše aplikace nejsou uložené tokeny ověřování v zařízení, nebo pokud tyto tokeny můžete vypršela platnost, by měla aplikace zařízení při přijetí oznámení zobrazit obecné oznámení uživateli zobrazuje výzvu spusťte aplikaci. Aplikace pak ověřuje uživatele a ukazuje datová část oznámení.

V tomto kurzu zabezpečení nabízené ukazuje, jak bezpečně odesílání nabízených oznámení. Tento kurz je založený na [upozornění uživatelů](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) kurzu, a proto kroky musí dokončit v tomto kurzu první.

> [!NOTE]
> V tomto kurzu se předpokládá, že jste vytvořili a nakonfigurovali vaše Centrum oznámení, jak je popsáno v [Začínáme s Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Upravit projekt pro iOS
Teď, když změnit váš back-end aplikace k odesílání jen na *ID* oznámení, budete muset změnit své aplikace pro iOS ke zpracování tohoto oznámení a zpětné volání váš back-end pro načtení zabezpečenou zprávu, který se má zobrazit.

K dosažení tohoto cíle, musíme zapisovat logiku načíst zabezpečený obsah z back-end aplikace.

1. V **AppDelegate.m**, zajistěte, aby aplikace zaregistruje pro tichou oznámení, zpracuje ID oznámení odeslaných z back-end. Přidat **UIRemoteNotificationTypeNewsstandContentAvailability** v didFinishLaunchingWithOptions možnost:
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
2. Ve vašem **AppDelegate.m** přidat oddíl implementace v horní části s následující prohlášení:
   
        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end
3. V oddílu implementace pak přidejte následující kód, nahraďte zástupný symbol `{back-end endpoint}` s koncovým bodem pro váš back-end získali dříve:

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

1. Teď musíme zpracování příchozí oznámení a získání obsahu zobrazíte pomocí této metody výše. Nejprve máme povolení spuštěný na pozadí při přijímání nabízených oznámení v aplikaci iOS. V **XCode**vyberte projektu aplikace na levém panelu a pak klikněte na cílovém hlavní aplikace v **cíle** část v centrálním podokně.
2. Pak klikněte na vaše **možnosti** v horní části podokna centrální a zkontrolujte, zda **vzdáleného oznámení** zaškrtávací políčko.
   
    ![][IOS1]
3. V **AppDelegate.m** přidejte následující metodu ke zpracování nabízených oznámení:
   
        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);
   
            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
   
                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];
   
        }
   
    Všimněte si, že je vhodnější pro zpracování v případech chybějící vlastnost hlavičky ověřování nebo odmítání back-end. Konkrétní zpracování těchto případech závisí hlavně na cílové činnost koncového uživatele. Jednou z možností je zobrazit oznámení s výzvou obecný pro ověření uživatele pro načtení skutečné oznámení.

## <a name="run-the-application"></a>Spuštění aplikace
Ke spuštění aplikace, postupujte takto:

1. V XCode spusťte aplikaci na fyzickém zařízení iOS (nabízených oznámení nebude fungovat v simulátoru).
2. V aplikaci pro iOS uživatelského rozhraní zadejte uživatelské jméno a heslo. Mohou to být libovolný řetězec, ale musí být stejnou hodnotu.
3. V aplikaci pro iOS uživatelského rozhraní, klikněte na tlačítko **přihlásit**. Pak klikněte na tlačítko **odeslat nabízené**. Měli byste vidět zabezpečené oznámení se zobrazí v vaše Centrum oznámení.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
