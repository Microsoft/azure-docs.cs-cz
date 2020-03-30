---
title: Odesílání lokalizovaných nabízených oznámení do iOS pomocí Center oznámení Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak používat nabízená lokalizovaná oznámení pro zařízení s iOS pomocí Azure Notification Hubs.
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
ms.openlocfilehash: a8614156be5d516d16aff698b604cf0e661d7311
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72385659"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Kurz: Odesílání lokalizovaných nabízených oznámení do iOS pomocí Center oznámení Azure

> [!div class="op_single_selector"]
> * [Windows Store C #](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

Tento kurz ukazuje, jak používat funkci [šablony](notification-hubs-templates-cross-platform-push-messages.md) centra Oznámení Azure k vysílání oznámení o nejnovějších zprávách, které byly lokalizovány podle jazyka a zařízení. V tomto kurzu začnete s aplikací pro iOS vytvořenou v [centru use notification hub y k odesílání nejnovějších zpráv]. Po dokončení se můžete zaregistrovat do kategorií, které vás zajímají, zadat jazyk, ve kterém chcete dostávat oznámení, a přijímat pouze nabízená oznámení pro vybrané kategorie v tomto jazyce.

Tento scénář má dvě části:

* Aplikace pro iOS umožňuje klientským zařízením určit jazyk a přihlásit se k odběru různých kategorií nejnovějších zpráv;
* Back-end vysílá oznámení pomocí funkce **značky** a **šablony** Centra oznámení Azure.

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Aktualizace uživatelského rozhraní aplikace
> * Vytvoření aplikace pro iOS
> * Odesílání oznámení lokalizovaných šablon z aplikace konzoly ROZHRANÍ .NET
> * Odeslání lokalizovaných oznámení šablony ze zařízení

## <a name="overview"></a>Přehled

V [části Use Notification Hubs k odesílání nejnovějších zpráv]jste vytvořili aplikaci, která **používala značky** k odběru oznámení pro různé kategorie zpráv. Mnoho aplikací však cílí na více trhů a vyžaduje lokalizaci. To znamená, že samotný obsah oznámení musí být lokalizován a doručen do správné sady zařízení. Tento kurz ukazuje, jak používat funkci **šablony** centra oznámení snadno doručit lokalizované oznámení aktuality.

> [!NOTE]
> Jedním ze způsobů odesílání lokalizovaných oznámení je vytvoření více verzí každé značky. Například pro podporu angličtiny, francouzštiny a mandarínštiny byste potřebovali tři různé značky pro zprávy ze světa: "world_en", "world_fr" a "world_ch". Pak byste museli odeslat lokalizovanou verzi světových zpráv do každé z těchto značek. V tomto tématu použijete šablony, abyste se vyhnuli šíření značek a požadavku na odesílání více zpráv.

Šablony představují způsob, jak určit, jak by konkrétní zařízení mělo dostávat oznámení. Šablona přesně určuje formát datové části tím, že odkazuje na vlastnosti, které jsou součástí zprávy odeslané back-endovou aplikací. V případě, že odešlete zprávu agnostik národního prostředí obsahující všechny podporované jazyky:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Pak zajistíte, že zařízení zaregistrovat se šablonou, která odkazuje na správnou vlastnost. Například aplikace pro iOS, která se chce zaregistrovat pro francouzské zpravodajské registry pomocí následující syntaxe:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Další informace o šablonách najdete v článku [Šablony.](notification-hubs-templates-cross-platform-push-messages.md)

## <a name="prerequisites"></a>Požadavky

* Dokončete nabízená oznámení na konkrétní kurz [zařízení iOS](notification-hubs-ios-xplat-segmented-apns-push-notification.md) a mít kód k dispozici, protože tento kurz staví přímo na tomto kódu.
* Visual Studio 2019 je volitelné.

## <a name="update-the-app-user-interface"></a>Aktualizace uživatelského rozhraní aplikace

V této části upravíte aplikaci Nejnovější zprávy, kterou jste vytvořili v tématu [Pomocí center oznámení odesílejte nejnovější zprávy] k odesílání lokalizovaných nejnovějších zpráv pomocí šablon.

Ve `MainStoryboard_iPhone.storyboard`vašem , přidejte segmentované ovládací prvek se třemi jazyky: angličtina, francouzština a mandarínština.

![Vytvoření scénáře ui iOS][13]

Pak nezapomeňte přidat IBOutlet v ViewController.h, jak je znázorněno na následujícím obrázku:

![Vytvoření zásuvek pro přepínače][14]

## <a name="build-the-ios-app"></a>Vytvoření aplikace pro iOS

1. Ve `Notification.h`vašem , `retrieveLocale` přidejte metodu a upravte store a subscribe metody, jak je znázorněno v následujícím kódu:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    Ve `Notification.m`vašem , `storeCategoriesAndSubscribe` upravte metodu přidáním parametru `locale` a uložením do výchozích hodnot uživatele:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Potom upravte metodu *subscribe* tak, aby zahrnovala národní prostředí:

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

    Použijete metodu `registerTemplateWithDeviceToken`, `registerNativeWithDeviceToken`namísto . Když se zaregistrujete k šabloně, musíte zadat šablonu json a také název šablony (protože aplikace může chtít zaregistrovat různé šablony). Nezapomeňte zaregistrovat kategorie jako značky, protože chcete, abyste obdrželi oznámení pro tyto novinky.

    Přidejte metodu pro načtení národního prostředí z výchozího nastavení uživatele:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Nyní, když `Notifications` jste změnili třídu, `ViewController` musíte se ujistit, že využívá nové `UISegmentControl`. Přidejte do metody `viewDidLoad` následující řádek, abyste se ujistili, že je aktuálně vybrané národní prostředí:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Potom ve `subscribe` své metodě změňte `storeCategoriesAndSubscribe` volání na následující kód:

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

3. Nakonec budete muset aktualizovat `didRegisterForRemoteNotificationsWithDeviceToken` metodu ve vašem AppDelegate.m, takže můžete správně aktualizovat registraci při spuštění aplikace. Změňte volání `subscribe` na způsob oznámení pomocí následujícího kódu:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(nepovinné) Odesílání oznámení lokalizovaných šablon z aplikace konzoly ROZHRANÍ .NET

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(nepovinné) Odeslání lokalizovaných oznámení šablony ze zařízení

Pokud nemáte přístup k Visual Studiu nebo chcete jen otestovat odesílání oznámení lokalizované šablony přímo z aplikace na zařízení. Parametry lokalizované šablony můžete `SendNotificationRESTAPI` přidat k metodě, kterou jste definovali v předchozím kurzu.

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

V tomto kurzu jste poslali lokalizovaná oznámení do zařízení se systémem iOS. Chcete-li se dozvědět, jak odesílat oznámení konkrétním uživatelům aplikací pro iOS, přejdete k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Nabízená oznámení odesílaná konkrétním uživatelům](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
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
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
