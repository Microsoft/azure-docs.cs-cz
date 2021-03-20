---
title: Odesílání lokalizovaných nabízených oznámení do systému iOS pomocí Azure Notification Hubs | Microsoft Docs
description: Naučte se používat pro zařízení s iOS lokalizovaná oznámení pomocí služby Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a78d3a76e2b13a120e9e744e181c95bfcb330f27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92313918"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Kurz: odeslání lokalizovaných nabízených oznámení do systému iOS pomocí Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C #](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

V tomto kurzu se dozvíte, jak pomocí funkce [šablony](notification-hubs-templates-cross-platform-push-messages.md) v Azure Notification Hubs vysílat oznámení o novinkách, která jsou lokalizovaná podle jazyka a zařízení. V tomto kurzu začnete s aplikací pro iOS vytvořenou [pomocí Notification Hubs k odesílání novinek]. Po dokončení se můžete zaregistrovat do kategorií, které vás zajímají, zadat jazyk, ve kterém se mají oznámení přijímat, a pro vybrané kategorie v daném jazyce přijmout jenom nabízená oznámení.

Tento scénář obsahuje dvě části:

* aplikace pro iOS umožňuje klientským zařízením určit jazyk a přihlásit se k odběru různých kategorií s novinkami.
* Back-endové všesměrově vysílá oznámení pomocí **značek** a funkcí **šablon** služby Azure Notification Hubs.

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Aktualizace uživatelského rozhraní aplikace
> * Sestavení aplikace pro iOS
> * Odeslat lokalizovaná oznámení šablon z konzolové aplikace .NET
> * Odeslat lokalizovaná oznámení šablon ze zařízení

## <a name="overview"></a>Přehled

Při [použití Notification Hubs k odesílání novinek]jste vytvořili aplikaci, která používá **značky** k přihlášení k odběru oznámení pro různé kategorie zpráv. Mnoho aplikací však cílí na více trhů a vyžaduje lokalizaci. To znamená, že obsah oznámení musí být lokalizovaný a dodaný do správné sady zařízení. V tomto kurzu se dozvíte, jak pomocí funkce **šablony** Notification Hubs snadno doručovat lokalizovaná oznámení o novinkách.

> [!NOTE]
> Jedním ze způsobů, jak odeslat lokalizovaná oznámení, je vytvořit několik verzí jednotlivých značek. Například pro podporu pro angličtinu, francouzštinu a mandarinku budete potřebovat tři různé značky pro světové zprávy: "world_en", "world_fr" a "world_ch". Pak byste museli odeslat lokalizovanou verzi celosvětového příspěvku ke každé z těchto značek. V tomto tématu použijete šablony k tomu, abyste se vyhnuli šíření značek a požadavek na odeslání více zpráv.

Šablony představují způsob, jak určit, jak má konkrétní zařízení obdržet oznámení. Šablona přesně určuje formát datové části tím, že odkazuje na vlastnosti, které jsou součástí zprávy odeslané back-endovou aplikací. V takovém případě odešlete nezávisláou zprávu národního prostředí obsahující všechny podporované jazyky:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Pak zajistěte, aby se zařízení registrovala se šablonou, která odkazuje na správnou vlastnost. Například aplikace pro iOS, která se chce zaregistrovat pro francouzské zprávy ve francouzštině, pomocí následující syntaxe:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Další informace o šablonách najdete v článku [šablony](notification-hubs-templates-cross-platform-push-messages.md) .

## <a name="prerequisites"></a>Předpoklady

* Dokončete kurz [nabízených oznámení na konkrétní zařízení se systémem iOS](notification-hubs-ios-xplat-segmented-apns-push-notification.md) a zpřístupníte kód, protože tento kurz se sestaví přímo na tomto kódu.
* Visual Studio 2019 je volitelné.

## <a name="update-the-app-user-interface"></a>Aktualizace uživatelského rozhraní aplikace

V této části upravíte aplikaci s novinkami, kterou jste vytvořili v tématu [použití Notification Hubs k odesílání novinek] k odesílání lokalizovaných zpráv pomocí šablon.

Přidejte do `MainStoryboard_iPhone.storyboard` něj segmentované řízení se třemi jazyky: angličtina, francouzština a mandarinek.

![Vytvoření scénáře uživatelského rozhraní iOS][13]

Pak nezapomeňte přidat IBOutlet do soubor viewcontroller. h, jak je znázorněno na následujícím obrázku:

![Vytvořit pro přepínače další možnosti][14]

## <a name="build-the-ios-app"></a>Sestavení aplikace pro iOS

1. V `Notification.h` , přidejte `retrieveLocale` metodu a upravte úložiště a odběr metod, jak je znázorněno v následujícím kódu:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    V `Notification.m` , upravte `storeCategoriesAndSubscribe` metodu tak, že přidáte `locale` parametr a uložíte ho do výchozích hodnot uživatele:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Pak upravte metodu *přihlášení k odběru* tak, aby zahrnovala národní prostředí:

    ```objc
    - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

        NSString* localeString;
        switch (locale) {
            case 0:
                localeString = @"English";
                break;
            case 1:
                localeString = @"French";
                break;
            case 2:
                localeString = @"Mandarin";
                break;
        }

        NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Místo použijte metodu `registerTemplateWithDeviceToken` `registerNativeWithDeviceToken` . Když se zaregistrujete na šablonu, musíte zadat šablonu JSON a také název šablony (protože aplikace může chtít registrovat jiné šablony). Ujistěte se, že jste své kategorie zaregistrovali jako značky, abyste je mohli dostávat pro tyto zprávy.

    Přidejte metodu pro načtení národního prostředí z výchozího nastavení uživatele:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Teď, když jste změnili `Notifications` třídu, musíte se ujistit, že využívá `ViewController` nové `UISegmentControl` . Do metody přidejte následující řádek `viewDidLoad` , abyste se ujistili, že chcete zobrazit aktuálně vybrané národní prostředí:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Potom v `subscribe` metodě změňte vaše volání na `storeCategoriesAndSubscribe` následující kód:

    ```objc
    [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                    @"Subscribed!" delegate:nil cancelButtonTitle:
                                    @"OK" otherButtonTitles:nil, nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

3. Nakonec musíte aktualizovat `didRegisterForRemoteNotificationsWithDeviceToken` metodu v AppDelegate. m, abyste mohli správně aktualizovat svou registraci při spuštění aplikace. Změňte volání `subscribe` metody oznámení pomocí následujícího kódu:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>volitelné Odeslat lokalizovaná oznámení šablon z konzolové aplikace .NET

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>volitelné Odeslat lokalizovaná oznámení šablon ze zařízení

Pokud nemáte přístup k aplikaci Visual Studio nebo chcete pouze testovat odesílání lokalizovaných oznámení šablon přímo z aplikace na zařízení. Můžete přidat lokalizované parametry šablony do `SendNotificationRESTAPI` metody, kterou jste definovali v předchozím kurzu.

```objc
- (void)SendNotificationRESTAPI:(NSString*)categoryTag
{
    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                defaultSessionConfiguration] delegate:nil delegateQueue:nil];

    NSString *json;

    // Construct the messages REST endpoint
    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                        HUBNAME, API_VERSION]];

    // Generated the token to be used in the authorization header.
    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

    //Create the request to add the template notification message to the hub
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    [request setHTTPMethod:@"POST"];

    // Add the category as a tag
    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

    // Template notification
    json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
            \"News_English\":\"Breaking %@ News in English : %@\","
            \"News_French\":\"Breaking %@ News in French : %@\","
            \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
            categoryTag, self.notificationMessage.text,
            categoryTag, self.notificationMessage.text,  // insert English localized news here
            categoryTag, self.notificationMessage.text,  // insert French localized news here
            categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

    // Signify template notification format
    [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

    // JSON Content-Type
    [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

    //Authenticate the notification message POST request with the SaS token
    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

    //Add the notification message body
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

    // Send the REST request
    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
            }
            if (data != NULL)
            {
                //xmlParser = [[NSXMLParser alloc] initWithData:data];
                //[xmlParser setDelegate:self];
                //[xmlParser parse];
            }
        }];

    [dataTask resume];
}
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste odeslali lokalizovaná oznámení do zařízení s iOS. Pokud se chcete dozvědět, jak nabízet oznámení konkrétním uživatelům aplikací pro iOS, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Nabízená oznámení odesílaná konkrétním uživatelům](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: /previous-versions/azure/reference/dn223264(v=azure.100)
[Používání centra oznámení k odesílání novinek]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/reference/dn223264(v=azure.100)