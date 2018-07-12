---
title: Azure Notification Hubs zabezpečené nabízená oznámení
description: Zjistěte, jak k odesílání zabezpečených nabízených oznámení do aplikací pro iOS z Azure. Ukázky kódu napsané v jazyce Objective-C a C#.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38308567"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs zabezpečené nabízená oznámení
> [!div class="op_single_selector"]
> * [Univerzální pro Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Přehled
Podpora nabízená oznámení v Microsoft Azure umožňuje získat přístup k snadným ovládáním, multiplatformní a horizontálním navýšením kapacity škálovanou infrastrukturu, což výrazně zjednodušuje provádění nabízená oznámení pro zákaznické a podnikové aplikace pro mobilní zařízení platformy.

Z důvodu dodržování legislativních nebo omezení zabezpečení, někdy aplikace může být vhodné zahrnout něco oznámení, který nemůže být přenesen prostřednictvím infrastrukturu standardní nabízených oznámení. Tento kurz popisuje, jak dosáhnout stejné prostředí tím, že odesílání citlivé informace přes zabezpečené ověřené připojení mezi klientským zařízením a back-endu aplikace.

Na vysoké úrovni tok je následujícím způsobem:

1. Back-end aplikace:
   * Úložiště zabezpečené datové části v back-end databáze.
   * ID tohoto oznámení se odešle do zařízení (se neodesílají žádné informace o zabezpečení).
2. Aplikace na zařízení, když obdrží oznámení:
   * Zařízení kontaktuje back endu, zabezpečené datové části požadavku.
   * Aplikace můžete zobrazit datovou část jako oznámení na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu) předpokládáme, že zařízení ukládá ověřovací token v místním úložišti, po přihlášení uživatele. Zaručí se tak bezproblémové prostředí, protože zařízení můžete načíst zabezpečené pomocí tohoto tokenu datovou část v oznámení. Pokud vaše aplikace neukládá ověřovacích tokenů na zařízení nebo pokud můžete platnost těchto tokenů, aplikace pro zařízení, při přijetí oznámení by měl zobrazit obecné oznámení výzvou, aby uživatel ke spuštění aplikace. Aplikace pak ověří uživatele a zobrazuje datová část oznámení.

V tomto kurzu zabezpečení nabízené ukazuje, jak bezpečně pošle nabízené oznámení. Tento kurz vychází [oznamování uživatelům pomocí](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) kurz, abyste měli dokončíte kroky uvedené v tomto kurzu nejprve.

> [!NOTE]
> Tento kurz předpokládá, že jste vytvořili a konfiguraci centra oznámení, jak je popsáno v [Začínáme se službou Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Upravit projekt pro iOS
Teď, když jste změnili back endu aplikace k odeslání jenom *ID* oznámení, budete muset změnit vaše aplikace iOS bude zpracovávat toto oznámení a zpětné volání back endu k načtení zabezpečené zprávy, který se má zobrazit.

K dosažení tohoto cíle, musíme napsat logiku k načtení zabezpečený obsah z back-end aplikace.

1. V **AppDelegate.m**, ujistěte se, že aplikace registrů pro tichou oznámení, takže zpracuje ID oznámení odeslané z back-endu. Přidat **UIRemoteNotificationTypeNewsstandContentAvailability** v didFinishLaunchingWithOptions možnost:
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
2. Ve vaší **AppDelegate.m** přidat oddíl implementace v horní části stránky s následující deklarace:
   
        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end
3. V oddílu implementace pak přidejte následující kód a nahraďte zástupný symbol `{back-end endpoint}` s koncovým bodem pro váš back-end získané dříve:

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

1. Teď máme zpracuje příchozí oznámení a použijte výše uvedené metody k načtení zobrazování obsahu. Nejprve musíme povolit vaše aplikace iOS bude běžet na pozadí při přijímání nabízených oznámení. V **XCode**, vyberte projektu aplikace na levém panelu a potom klikněte na tlačítko v cílové hlavní aplikace **cíle** středovém podokně v části.
2. Klikněte na vaše **možnosti** kartě v horní části centrální podokně a podívejte se **Vzdálená oznámení** zaškrtávací políčko.
   
    ![][IOS1]
3. V **AppDelegate.m** přidejte následující metodu pro zpracování nabízených oznámení:
   
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
   
    Všimněte si, že je vhodnější zpracovat případy chybí vlastnost záhlaví ověřování nebo zamítnutí back-end. Konkrétní zpracování těchto případech většinou závisí cílové činnost koncového uživatele. Jednou z možností je zobrazení oznámení s výzvou obecný kvůli ověření uživatele k načtení skutečné oznámení.

## <a name="run-the-application"></a>Spuštění aplikace
Pokud chcete spustit aplikaci, postupujte takto:

1. V XCode spusťte aplikaci na fyzickém zařízení iOS (nabízená oznámení nebude fungovat v simulátoru).
2. V aplikaci pro iOS uživatelského rozhraní zadejte uživatelské jméno a heslo. Mohou to být libovolný řetězec, ale musí být stejnou hodnotu.
3. V aplikaci pro iOS uživatelského rozhraní, klikněte na tlačítko **přihlášení**. Pak klikněte na tlačítko **odesílání nabízených oznámení**. Měli byste vidět zabezpečené oznámení se zobrazí v centru oznámení.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
