---
title: Zařízení s iOS pomocí Azure Notification Hubs odesílat nabízená oznámení lokalizované | Dokumentace Microsoftu
description: Další informace o použití lokalizovaných nabízených oznámení do zařízení se systémem iOS pomocí Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: eef3f153844be00d0338aa98b8aba21c5b749e46
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094219"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Kurz: Odesílání lokalizovaných nabízených oznámení do zařízení s iOS pomocí Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

V tomto kurzu se dozvíte, jak používat [šablony](notification-hubs-templates-cross-platform-push-messages.md) funkce služby Azure Notification Hubs k rozesílání mimořádných zpráv, které byl lokalizován podle jazyka a zařízení. V tomto kurzu začnete s aplikací pro iOS vytvořena v [Používání centra oznámení k odesílání novinek]. Jakmile budete hotovi, zaregistrujte kategorií, které vás zajímají, určit jazyk, ve kterém chcete přijímat oznámení a přijímat pouze nabízená oznámení u vybraných kategorií v daném jazyce.

Existují dvě části pro tento scénář:

* aplikace pro iOS umožňuje klientských zařízení můžete určit jazyk a přihlaste se k jiné zásadní nové kategorie; odběru
* Vysílá oznámení, pomocí back endu **značky** a **šablony** funkce služby Azure Notification Hubs.

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Aktualizace uživatelského rozhraní aplikace
> * Vytvoření aplikace pro iOS
> * Odesílání oznámení lokalizovanou šablonu z konzolové aplikace .NET
> * Odesílání oznámení lokalizovanou šablonu ze zařízení

## <a name="overview"></a>Přehled

V [Používání centra oznámení k odesílání novinek], jste sestavili aplikaci, která používá **značky** přihlásit k odběru oznámení pro různé nové kategorie. Velký počet aplikací, ale cílit na různé trhy a lokalizovat. To znamená, že obsah oznámení sami musí být lokalizovány a doručí do sady správné zařízení. V tomto kurzu se dozvíte, jak používat **šablony** služby Notification hubs pro snadné poskytování lokalizované mimořádných zpráv.

> [!NOTE]
> Jedním ze způsobů na odesílání lokalizovaných oznámení je vytvořit několik verzí jednotlivé značky. Například pro podporu angličtina, francouzština a Mandarínština, je třeba tří různých klíčových slov pro zprávami ze světa seskupenými: "world_en", "world_fr" a "world_ch". Pak nutné odeslat lokalizovanou verzi zprávami ze světa seskupenými ke každé z těchto značek. V tomto tématu použijete šablony vyhnout tím, jak narůstá značek a požadavky na odeslání více zpráv.

Na vysoké úrovni šablony jsou způsob, jak určit, jak konkrétní zařízení přijímat oznámení. Šablona přesně určuje formát datové části tím, že odkazuje na vlastnosti, které jsou součástí zprávy odeslané back-endovou aplikací. V případě odešlete zprávu bez národního prostředí obsahující všechny podporované jazyky:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Zajistěte, že zařízení zaregistrovat šablonou, která odkazuje na správné vlastnosti. Aplikace pro iOS, která chce zaregistrovat francouzské zpráv pro instanci zaregistruje pomocí následující syntaxe:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Další informace o šablonách najdete v tématu [šablony](notification-hubs-templates-cross-platform-push-messages.md) článku.

## <a name="prerequisites"></a>Požadavky

* Dokončení [nabízená oznámení do zařízení s Iosem konkrétní](notification-hubs-ios-xplat-segmented-apns-push-notification.md) kurz a máte kód, který je k dispozici, protože v tomto kurzu staví přímo na tento kód.
* Visual Studio 2017 je volitelné.

## <a name="update-the-app-user-interface"></a>Aktualizace uživatelského rozhraní aplikace

V této části upravíte novinkách aplikaci, kterou jste vytvořili v tématu [Používání centra oznámení k odesílání novinek] k odesílání lokalizovaných mimořádné zprávy pomocí šablon.

Ve vaší `MainStoryboard_iPhone.storyboard`, přidat segmentované ovládací prvek se třemi jazyky: Angličtina, francouzština a Mandarínština.

![Vytvoření uživatelského rozhraní scénáři iOS][13]

Pak nezapomeňte přidat IBOutlet ve vašich ViewController.h, jak je znázorněno na následujícím obrázku:

![Vytvoření výstupy přepínače][14]

## <a name="build-the-ios-app"></a>Vytvoření aplikace pro iOS

1. Ve vaší `Notification.h` přidat `retrieveLocale` metody a úprava úložiště a odběru metod, jak je znázorněno v následujícím kódu:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    Ve vaší `Notification.m`, upravit `storeCategoriesAndSubscribe` metodu tak, že přidáte `locale` parametr a jeho uložení ve výchozím nastavení uživatele:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Potom upravte *odběru* tak, aby zahrnoval národní prostředí:

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

    Použijte metodu `registerTemplateWithDeviceToken`, namísto `registerNativeWithDeviceToken`. Při registraci k šabloně, musíte zadat šablonu json a také název pro šablonu (jak aplikace může být vhodné zaregistrovat různé šablony služby). Ujistěte se, že k registraci kategorie jako značky, jak chcete Ujistěte se, že chcete dostávat oznámení těchto zpráv.

    Přidejte metodu pro načtení z uživatelská nastavení pro výchozí národní prostředí:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Teď, když jste změnili `Notifications` třídy, je nutné Ujistěte se, že `ViewController` využívá nové `UISegmentControl`. Přidejte následující řádek v `viewDidLoad` metoda Ujistěte se, že zobrazíte národní prostředí, která je aktuálně vybrána:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Pak na vaše `subscribe` metodu, změňte volání `storeCategoriesAndSubscribe` v následujícím kódu:

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

3. Nakonec budete muset aktualizovat `didRegisterForRemoteNotificationsWithDeviceToken` metoda ve vaší AppDelegate.m tak, aby správně můžete aktualizovat svoji registraci při spuštění vaší aplikace. Změňte volání `subscribe` metoda oznámení s následujícím kódem:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(volitelné) Odesílání oznámení lokalizovanou šablonu z konzolové aplikace .NET

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(volitelné) Odesílání oznámení lokalizovanou šablonu ze zařízení

Pokud nemusíte mít přístup ke službě Visual Studio, nebo chcete testování odesílání lokalizovanou šablonu oznámení přímo z aplikace na zařízení. Můžete přidat parametry lokalizovanou šablonu tak, aby `SendNotificationRESTAPI` metoda, kterou jste definovali v předchozím kurzu.

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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste odeslali lokalizovaných oznámení do zařízení s Iosem. Pokud se chcete naučit nabízená oznámení odesílaná konkrétním uživatelům aplikace pro iOS, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Zasílání nabízených oznámení určitým uživatelům](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Používání centra oznámení k odesílání novinek]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
