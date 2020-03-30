---
title: Azure Notification Hubs Secure Push pro iOS
description: Přečtěte si, jak odesílat zabezpečená nabízená oznámení do aplikace pro iOS z Azure. Ukázky kódu napsané v objective-C a C#.
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 96d1dd514f6fb9c11d7194714337583d6b4387cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530744"
---
# <a name="azure-notification-hubs-secure-push"></a>Zabezpečené nabízení center oznámení Azure

> [!div class="op_single_selector"]
> * [Univerzální pro Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Přehled

Podpora nabízených oznámení v Microsoft Azure umožňuje přístup ke snadno použitelné víceplatformové a škálované nabízené infrastruktuře, což výrazně zjednodušuje implementaci nabízených oznámení pro spotřebitelské i podnikové aplikace pro mobilní zařízení. Platformy.

Z důvodu regulačních nebo bezpečnostních omezení může aplikace někdy chtít zahrnout něco do oznámení, které nelze přenášet prostřednictvím standardní infrastruktury nabízených oznámení. Tento kurz popisuje, jak dosáhnout stejného prostředí odesláním citlivých informací prostřednictvím zabezpečeného, ověřeného připojení mezi klientským zařízením a back-endem aplikace.

Na vysoké úrovni je tok následující:

1. Back-end aplikace:
   * Ukládá zabezpečené datové části v back-end databázi.
   * Odešle ID tohoto oznámení do zařízení (nejsou odesílány žádné zabezpečené informace).
2. Aplikace na zařízení při příjmu oznámení:
   * Zařízení kontaktuje back-end požadující zabezpečenou datovou část.
   * Aplikace může zobrazit datovou část jako oznámení na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu) předpokládáme, že zařízení ukládá ověřovací token v místním úložišti po přihlášení uživatele. To zaručuje bezproblémové prostředí, protože zařízení může načíst zabezpečenou datovou část oznámení pomocí tohoto tokenu. Pokud vaše aplikace neukládá ověřovací tokeny na zařízení nebo pokud tyto tokeny mohou vypršet, aplikace zařízení po obdržení oznámení by měla zobrazit obecné oznámení s výzvou uživateli ke spuštění aplikace. Aplikace pak ověří uživatele a zobrazí datovou část oznámení.

Tento kurz zabezpečeného nabízení ukazuje, jak bezpečně odeslat nabízené oznámení. Kurz vychází z kurzu [Upozornit uživatele,](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) takže byste měli nejprve provést kroky v tomto kurzu.

> [!NOTE]
> Tento kurz předpokládá, že jste vytvořili a nakonfigurovali centrum oznámení, jak je popsáno v [začínáme s centry oznámení (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Úprava projektu iOS

Teď, když jste upravili back-end aplikace tak, aby odesílala pouze *ID* oznámení, musíte změnit aplikaci pro iOS, aby toto oznámení zpracovali, a zavolat zpět back-end, abyste načetli zabezpečenou zprávu, která se má zobrazit.

K dosažení tohoto cíle, musíme napsat logiku k načtení zabezpečeného obsahu z back-endu aplikace.

1. V `AppDelegate.m`aplikaci se ujistěte, že se aplikace registruje pro tichá oznámení, aby zpracuje ID oznámení odeslané z back-endu. Přidejte `UIRemoteNotificationTypeNewsstandContentAvailability` možnost v didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. V `AppDelegate.m` části implementace v horní části s následujícím prohlášením:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Pak přidejte do sekce implementace následující kód, který napodobuje zástupný symbol `{back-end endpoint}` koncovým bodem pro váš back-end získaný dříve:

    ```objc
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

    Tato metoda volá back-end aplikace k načtení obsahu oznámení pomocí přihlašovacích údajů uložených ve sdílených předvolbách.

4. Nyní musíme zpracovat příchozí oznámení a použít výše uvedenou metodu k načtení obsahu k zobrazení. Nejprve musíme povolit, aby vaše aplikace pro iOS běžela na pozadí při přijímání nabízených oznámení. V **XCode**vyberte projekt aplikace na levém panelu a klikněte na hlavní cíl aplikace v části **Cíle** v centrálním podokně.
5. Potom klikněte na kartu **Možnosti** v horní části centrálního podokna a zaškrtněte políčko **Vzdálená oznámení.**

    ![][IOS1]

6. V `AppDelegate.m` přidejte následující metodu pro zpracování nabízených oznámení:

    ```objc
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
    ```

    Všimněte si, že je vhodnější zpracovat případy chybějící vlastnosti záhlaví ověřování nebo odmítnutí back-endem. Konkrétní zpracování těchto případů závisí především na cílové uživatelské zkušenosti. Jednou z možností je zobrazení oznámení s obecnou výzvou pro uživatele k ověření načíst skutečné oznámení.

## <a name="run-the-application"></a>Spuštění aplikace

Chcete-li aplikaci spustit, postupujte takto:

1. V XCode spusťte aplikaci na fyzickém zařízení se systémem iOS (nabízená oznámení nebudou v simulátoru fungovat).
2. V uživatelském rozhraní aplikace pro iOS zadejte uživatelské jméno a heslo. Může se jednat o libovolný řetězec, ale musí mít stejnou hodnotu.
3. V uj. **Log in** Potom klepněte na **tlačítko Odeslat nabízenou položku**. V oznamovacím centru by se mělo zobrazit zabezpečené oznámení.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
