---
title: Nabízená oznámení lokalizované do zařízení s iOS pomocí Azure Notification Hubs | Microsoft Docs
description: Naučte se používat lokalizované nabízených oznámení do zařízení s iOS pomocí Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: b3d74086ee233da50138aff00d8da78aa0243a75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776494"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Kurz: Lokalizované nabízených oznámení do zařízení s iOS pomocí Azure Notification Hubs 
> [!div class="op_single_selector"]
> * [Windows Store jazyka C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 

V tomto kurzu se dozvíte, jak používat [šablony](notification-hubs-templates-cross-platform-push-messages.md) funkce Azure Notification Hubs k vysílání oznámení o aktuálních zprávách, lokalizované podle jazyka a zařízení. V tomto kurzu začnete k aplikaci iOS, které jsou vytvořené v [použití centra oznámení k odesílání novinek]. Po dokončení registrace pro kategorií, které vás zajímají, zadat jazyk, ve které chcete dostávat oznámení a přijímat pouze nabízená oznámení pro vybrané kategorie v daném jazyce.

Existují tento scénář se skládá ze dvou částí:

* aplikace pro iOS umožňuje klienta zařízení můžete určit jazyk a k odběru kategorií různých nejnovější zprávy;
* Back-end vysílá oznámení, pomocí **značka** a **šablony** funkce Azure Notification Hubs.

V tomto kurzu proveďte následující kroky:

> [!div class="checklist"]
> * Aktualizace uživatelského rozhraní aplikace
> * Vytvoření aplikace pro iOS
> * Odesílání oznámení lokalizovanou šablonu z konzolové aplikace .NET
> * Odeslat oznámení lokalizovanou šablonu ze zařízení


## <a name="overview"></a>Přehled
V [použití centra oznámení k odesílání novinek], jste vytvořili aplikaci, která používá **značky** přihlášení k odběru oznámení pro různé zprávy kategorie. Velký počet aplikací, ale cíli více trhů a vyžadují lokalizace. Znamená to, že obsah oznámení sami musí být lokalizovaný a doručí na správnou sadu zařízení. V tomto kurzu se dozvíte, jak používat **šablony** funkce centra oznámení snadno dodávat služby vhodné oznámení o lokalizované aktuálních zprávách.

> [!NOTE]
> Jeden způsob, jak odeslat lokalizované oznámení je vytvoření více verzí jednotlivé značky. Například pro podporu angličtinu, francouzštinu a Mandarínština, potřebovali byste tří různých značek pro world zprávy: "world_en", "world_fr" a "world_ch". Máte by pak odeslat lokalizované verzi world zprávy pro každé z těchto značek. V tomto tématu Použití šablon předejdete tím, jak narůstá značek a požadavek odeslat více zpráv.

Na vysoké úrovni šablony jsou způsob, jak určit, jak by měla určité zařízení zasláno oznámení. Šablona specifikuje formát datové části přesně tím, že odkazuje na vlastnosti, které jsou součástí zprávy odeslané ve vašem back-end aplikace. V případě můžete odeslat zprávu bez ohledu na národním prostředí obsahující všechny podporované jazyky:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Pak je zajistit, že zařízení zaregistrovat pomocí šablony, která odkazuje na správný vlastnost. Například aplikaci iOS, která chce k registraci pro francouzštině zprávy zaregistruje pomocí následující syntaxe:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Další informace o šablonách najdete v tématu [šablony](notification-hubs-templates-cross-platform-push-messages.md) článku.

## <a name="prerequisites"></a>Požadavky

- Dokončení [nabízená oznámení do zařízení s iOS konkrétní](notification-hubs-ios-xplat-segmented-apns-push-notification.md) kurz a mít kód k dispozici, protože v tomto kurzu staví přímo na tento kód.
- Visual Studio 2012 nebo novější je volitelný.

## <a name="update-the-app-user-interface"></a>Aktualizace uživatelského rozhraní aplikace
V této části upravíte novinkách aplikaci, kterou jste vytvořili v tématu [použití centra oznámení k odesílání novinek] odeslat lokalizované novinky pomocí šablon.

Vaše MainStoryboard_iPhone.storyboard přidat Segmentovaným ovládací prvek se třemi jazyky: angličtina, francouzština a Mandarínština.

![][13]

Potom nezapomeňte přidat IBOutlet ve vaší ViewController.h, jak je znázorněno na následujícím obrázku:

![][14]

## <a name="build-the-ios-app"></a>Vytvoření aplikace pro iOS
1. Ve vašem Notification.h přidat *retrieveLocale* metoda a upravte úložišti a přihlášení k odběru metod, jak je znázorněno v následujícím kódu:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    ```
    V Notification.m, upravte *storeCategoriesAndSubscribe* metoda přidáním parametr národního prostředí a ukládání do výchozí nastavení uživatele:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
    ````
    Potom upravte *přihlášení k odběru* tak, aby zahrnoval národního prostředí:
   
    ```obj-c
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
    You use the method *registerTemplateWithDeviceToken*, instead of *registerNativeWithDeviceToken*. When you register for a template, you have to provide the json template and also a name for the template (as the app might want to register different templates). Make sure to register your categories as tags, as you want to make sure to receive the notifciations for those news.
   
    Add a method to retrieve the locale from the user default settings:
   
    ```obj-c
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
    ```
2. Teď, když jste upravili třídě oznámení, budete muset Ujistěte se, že ViewController využívá nové UISegmentControl. Přidejte následující řádek v *viewDidLoad* metoda zobrazíte národního prostředí, který je aktuálně vybraný zajistit:
   
    ```obj-c
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
     ```  
    Pak na vaše *přihlášení k odběru* metoda, změnit nastavení volání *storeCategoriesAndSubscribe* pro následující kód:
   
    ```obj-c
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
3. Nakonec budete muset aktualizovat *didRegisterForRemoteNotificationsWithDeviceToken* metoda v AppDelegate.m, tak, aby správně můžete aktualizovat registrace při spuštění aplikace. Změňte nastavení volání *přihlášení k odběru* metoda oznámení s následujícím kódem:
   
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

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(volitelné) Odeslat oznámení lokalizovanou šablonu ze zařízení
Pokud máte přístup k sadě Visual Studio, nebo jenom chcete otestujte, zasílání oznámení lokalizovanou šablonu přímo z aplikace na zařízení. Můžete přidat parametry lokalizovanou šablonu `SendNotificationRESTAPI` metoda definované v předchozích kurzu.

    ```obj-c
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
V tomto kurzu jste poslali lokalizované oznámení do zařízení s iOS. Informace o tom pro nabízená oznámení konkrétním uživatelům aplikace pro iOS, přechodu na následující kurzu: 

> [!div class="nextstepaction"]
>[Nabízená oznámení pro konkrétní uživatele](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[použití centra oznámení k odesílání novinek]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
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
