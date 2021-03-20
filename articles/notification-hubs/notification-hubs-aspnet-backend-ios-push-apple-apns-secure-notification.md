---
title: Azure Notification Hubs Secure push pro iOS
description: Naučte se odesílat zabezpečená nabízená oznámení do aplikace pro iOS z Azure. Ukázky kódu napsané v cíli – C a C#.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a6c85ba017656bd312ddfe3d5f6d98014a3dc89a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90090342"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs Secure push

> [!div class="op_single_selector"]
> * [Univerzální pro Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Přehled

Podpora nabízených oznámení v Microsoft Azure umožňuje přístup ke špičkové infrastruktuře nabízených oznámení s více platformami, která výrazně zjednodušuje implementaci nabízených oznámení pro příjemce i podnikové aplikace pro mobilní platformy.

V důsledku regulativních nebo bezpečnostních omezení někdy může aplikace chtít v oznámení zahrnout něco, co nelze přenést pomocí standardní infrastruktury nabízených oznámení. V tomto kurzu se dozvíte, jak dosáhnout stejného prostředí odesláním citlivých informací prostřednictvím zabezpečeného a ověřeného připojení mezi klientským zařízením a back-endu aplikace.

Tok je na vysoké úrovni následující:

1. Back-end aplikace:
   * Ukládá zabezpečenou datovou část do back-endové databáze.
   * Odešle ID tohoto oznámení do zařízení (nejsou odesílány žádné zabezpečené informace).
2. Aplikace v zařízení při příjmu oznámení:
   * Zařízení kontaktuje back-end požadující zabezpečenou datovou část.
   * Aplikace může datovou část zobrazit jako oznámení na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu) předpokládáme, že zařízení po přihlášení uživatele uloží ověřovací token do místního úložiště. Tím zajistíte bezproblémové prostředí, protože zařízení může načíst zabezpečenou datovou část oznámení pomocí tohoto tokenu. Pokud vaše aplikace neukládá ověřovací tokeny na zařízení nebo pokud tyto tokeny můžou být prošlé, aplikace zařízení po přijetí oznámení by měla zobrazit obecné oznámení s výzvou, aby uživatel spustil aplikaci. Aplikace pak uživatele ověří a zobrazí datovou část oznámení.

Tento kurz zabezpečeného nabízeného oznámení ukazuje, jak bezpečně odeslat nabízené oznámení. Kurz se sestaví v kurzu [informování uživatelů](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , takže byste nejdřív měli provést kroky v tomto kurzu.

> [!NOTE]
> V tomto kurzu se předpokládá, že jste vytvořili a nakonfigurovali centrum oznámení, jak je popsáno v tématu [Posílání nabízených oznámení do aplikací pro iOS pomocí Azure Notification Hubs](ios-sdk-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Úprava projektu iOS

Teď, když jste změnili back-end aplikace, abyste poslali jenom *ID* oznámení, musíte změnit aplikaci pro iOS, aby zpracovala toto oznámení, a volat back-end pro načtení zabezpečené zprávy, která se má zobrazit.

Abychom dosáhli tohoto cíle, musíme napsat logiku, která načte zabezpečený obsah z back-endu aplikace.

1. V nástroji se ujistěte, `AppDelegate.m` že aplikace registruje pro tichá oznámení, aby zpracovala ID oznámení odeslané z back-endu. Přidejte `UIRemoteNotificationTypeNewsstandContentAvailability` možnost do didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. V `AppDelegate.m` horní části Přidat implementaci s následující deklarací:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Pak v části implementace přidejte následující kód a nahraďte zástupný symbol `{back-end endpoint}` koncovým bodem pro back-end získaný dříve:

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

    Tato metoda volá back-end aplikace pro načtení obsahu oznámení pomocí přihlašovacích údajů uložených ve sdílených preferencích.

4. Nyní zpracujte příchozí oznámení a pomocí výše uvedené metody načtěte obsah, který se má zobrazit. Nejdřív při přijímání nabízeného oznámení Povolte aplikaci pro iOS běžet na pozadí. V **Xcode** vyberte projekt aplikace na levém panelu a pak klikněte na cíl vaší hlavní aplikace v části **cíle** v centrálním podokně.
5. Pak v horní části centrálního podokna klikněte na kartu **Možnosti** a zaškrtněte políčko **Vzdálená oznámení** .

    ![Snímek obrazovky s XCode s vybraným projektem aplikace a otevřenou kartou možnosti. Zaškrtávací políčko pro vzdálené oznámení je zaškrtnuté.][IOS1]

6. V `AppDelegate.m` části přidejte následující metodu pro zpracování nabízených oznámení:

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

    Všimněte si, že je vhodnější zpracovat případy chybějící vlastnosti záhlaví ověřování nebo zamítnutí back-endu. Konkrétní zpracování těchto případů závisí hlavně na svém cílovém uživatelském prostředí. Jednou z možností je zobrazit oznámení s obecnou výzvou pro uživatele, aby se ověřilo, že se má načíst vlastní oznámení.

## <a name="run-the-application"></a>Spuštění aplikace

Chcete-li spustit aplikaci, postupujte následovně:

1. V XCode spusťte aplikaci na fyzickém zařízení s iOS (nabízená oznámení nebudou v simulátoru fungovat).
2. V uživatelském rozhraní aplikace pro iOS zadejte uživatelské jméno a heslo. Může se jednat o libovolný řetězec, ale musí se jednat o stejnou hodnotu.
3. V uživatelském rozhraní aplikace pro iOS klikněte na **Přihlásit** se. Pak klikněte na **Odeslat nabízení**. V centru oznámení by se mělo zobrazit zabezpečené oznámení.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
