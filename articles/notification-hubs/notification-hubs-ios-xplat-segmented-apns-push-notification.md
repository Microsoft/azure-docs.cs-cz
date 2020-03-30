---
title: Odesílání nabízených oznámení konkrétním iOS zařízením pomocí Azure Notification Hubs | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak pomocí Centra oznámení Azure odesílat nabízená oznámení na konkrétní zařízení iOS.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: a775963f1b0fa19cd687c839f527f4a078c76864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80126993"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Kurz: Odesílání nabízených oznámení konkrétním zařízením iOS pomocí center oznámení Azure

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Přehled

Tento kurz ukazuje, jak používat Azure Notification Hubs k vysílání oznámení o nejnovějších zprávách do aplikace pro iOS. Po dokončení se můžete zaregistrovat do kategorií nejnovějších zpráv, které vás zajímají, a dostávat pouze nabízená oznámení pro tyto kategorie. Tento scénář se běžně používá v řadě aplikací, které posílají oznámení skupinám uživatelům, kteří o ně projevili zájem. Může jít třeba o čtečku RSS, aplikaci pro hudební fanoušky atd.

Scénáře vysílání povolíte tak, že při registraci v centru oznámení přidáte jednu nebo více *značek*. Když jsou oznámení odeslána na značku, zařízení, která se zaregistrovala pro značku, obdrží oznámení. Značky jsou jednoduše řetězce, které se nemusejí vytvářet předem. Další informace o značkách najdete v článku [Směrování a výrazy značek ve službě Notification Hubs](notification-hubs-tags-segment-push-message.md).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Přidání výběru kategorie do aplikace
> * Posílání označených oznámení
> * Odesílání oznámení ze zařízení
> * Spuštění aplikace a generování oznámení

## <a name="prerequisites"></a>Požadavky

Toto téma vychází z aplikace, kterou jste vytvořili v [kurzu: Nabízená oznámení aplikacím pro iOS pomocí Center oznámení Azure][get-started]. Před zahájením tohoto kurzu musíte mít již [dokončený kurz: Nabízená oznámení do aplikací pro iOS pomocí Center oznámení Azure][get-started].

## <a name="add-category-selection-to-the-app"></a>Přidání výběru kategorií do aplikace

Prvním krokem je přidání prvků uživatelského rozhraní do existujícího scénáře, které umožňují uživateli vybrat kategorie k registraci. Kategorie, které uživatel vybere, jsou uložené v zařízení. Při spuštění aplikace se v centru oznámení provede registrace zařízení s vybranými kategoriemi ve formě značek.

1. Ve **MainStoryboard_iPhone.storyboard** uložte z knihovny objektů následující součásti:

   * Štítek s textem "Breaking News",
   * Popisky s texty kategorií "Svět", "Politika", "Business", "Technologie", "Věda", "Sport",
   * Šest přepínačů, jeden pro každou kategorii, nastavit každý **stav** přepínače, aby byl ve výchozím nastavení **vypnutý.**
   * Jedno tlačítko označené "Přihlásit se k odběru"

     Váš scénář by měl vypadat takto:

     ![Tvůrce rozhraní Xcode][3]

2. V editoru asistenta vytvořte výstupy pro všechny přepínače a nazvěte je "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

3. Vytvořte akci pro `subscribe`tlačítko s názvem ; měli `ViewController.h` byste obsahovat následující kód:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Vytvořte novou **třídu Cocoa Touch** s názvem `Notifications`. Zkopírujte následující kód v části rozhraní souboru Notifications.h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Do souboru Notifications.m přidejte následující direktivu importu:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Zkopírujte následující kód v části implementace souboru Notifications.m.

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Tato třída používá místní úložiště k ukládání a načítání kategorií zpráv, které toto zařízení přijímá. Obsahuje také metodu registrace pro tyto kategorie pomocí registrace [šablony.](notification-hubs-templates-cross-platform-push-messages.md)

7. V `AppDelegate.h` souboru přidejte příkaz `Notifications.h` import u a přidejte `Notifications` vlastnost pro instanci třídy:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. V `didFinishLaunchingWithOptions` metodě `AppDelegate.m`v , přidejte kód pro inicializaci instancí oznámení na začátku metody.  
    `HUBNAME`a `HUBLISTENACCESS` (definované `hubinfo.h`v ) `<hub name>` by `<connection string with listen access>` již měly mít zástupné symboly a nahrazeny názvem centra oznámení a připojovacím řetězcem pro *DefaultListenSharedAccessSignature,* který jste získali dříve

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Obecně platí, že přihlašovací údaje distribuované klientskou aplikací nejsou příliš bezpečné, a proto byste měli s klientskou aplikací distribuovat jenom přístupový klíč pro naslouchání. Přístup pro naslouchání umožňuje aplikaci registrovat oznámení, ale nedovolí měnit stávající registrace ani odesílat oznámení. Plný přístupový klíč se používá v zabezpečené back-endové službě k posílání oznámení a změně stávajících registrací.

9. V `didRegisterForRemoteNotificationsWithDeviceToken` metodě `AppDelegate.m`v , nahradit kód v metodě s následujícím `notifications` kódem předat token zařízení do třídy. Třída `notifications` provádí registraci pro oznámení s kategoriemi. Pokud uživatel změní výběr kategorií, `subscribeWithCategories` zavolejte metodu jako odpověď na tlačítko **odběru** a aktualizujte je.

    > [!NOTE]
    > Vzhledem k tomu, že token zařízení přiřazený službou Nabízených oznámení Apple (APNS) se může kdykoli změnit, měli byste se často registrovat pro oznámení, abyste se vyhnuli selhání oznámení. V tomto příkladu se oznámení registrují při každém spuštění aplikace. Pokud se aplikace spouštějí často, třeba častěji než jednou denně, pravděpodobně můžete registraci přeskočit kvůli úspoře šířky pásma, protože od předchozí registrace neuplynul ani den.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    V tomto okamžiku by měl být `didRegisterForRemoteNotificationsWithDeviceToken` žádný jiný kód v metodě.

10. Následující metody by již `AppDelegate.m` měly být k dispozici od dokončení [kurzu Začínáme s oznámeními.][get-started] Pokud ne, přidejte je.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Tato metoda zpracovává oznámení přijatá při spuštění aplikace zobrazením jednoduchého **uialert**.

11. V `ViewController.m`aplikaci `import` přidejte příkaz pro `AppDelegate.h` a zkopírujte následující `subscribe` kód do metody generované XCode. Tento kód aktualizuje registraci oznámení tak, aby používala nové značky kategorií, které uživatel zvolil v uživatelském rozhraní.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Tato metoda `NSMutableArray` vytvoří kategorie a `Notifications` používá třídu k uložení seznamu v místním úložišti a registruje odpovídající značky s centrem oznámení. Při změně kategorií se vytvoří registrace s novými kategoriemi.

12. V `ViewController.m`nabídce `viewDidLoad` přidejte do metody následující kód pro nastavení uživatelského rozhraní na základě dříve uložených kategorií.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

Aplikace teď může uložit sadu kategorií do místního úložiště zařízení, které se používá k registraci v centru oznámení při každém spuštění aplikace. Uživatel může změnit výběr kategorií za běhu `subscribe` a klepnutím na metodu aktualizovat registraci zařízení. Dále aktualizujete aplikaci a odešlete oznámení o nejnovějších zprávách přímo v samotné aplikaci.

## <a name="optional-send-tagged-notifications"></a>(nepovinné) Odesílání označených oznámení

Pokud nemáte přístup k Visual Studiu, můžete přeskočit na další část a odesílat oznámení ze samotné aplikace. Můžete také odeslat správné oznámení šablony z [portálu Azure] pomocí karty ladění pro centrum oznámení.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(nepovinné) Odesílání oznámení ze zařízení

Normálně oznámení by být odeslány back-endslužby, ale můžete posílat aktuality oznámení přímo z aplikace. Chcete-li tak učinit, aktualizujte metodu, `SendNotificationRESTAPI` kterou jste definovali v kurzu [Začínáme s centry oznámení.][get-started]

1. V `ViewController.m`aplikaci `SendNotificationRESTAPI` aktualizujte metodu takto, aby přijala parametr pro značku kategorie a odešle správné oznámení [šablony.](notification-hubs-templates-cross-platform-push-messages.md)

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
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

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

2. V `ViewController.m`aplikaci `Send Notification` aktualizujte akci, jak je znázorněno v následujícím kódu. Tak, aby odesílá oznámení pomocí každé značky jednotlivě a odešle na více platforem.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Znovu sestavte projekt a ujistěte se, že nemáte žádné chyby sestavení.

## <a name="run-the-app-and-generate-notifications"></a>Spuštění aplikace a generování oznámení

1. Stiskněte tlačítko Spustit a sestavte projekt a spusťte aplikaci. Vyberte některé možnosti nejnovějších zpráv, abyste se přihlásili k odběru, a stiskněte tlačítko **Přihlásit se k odběru.** Měli byste vidět dialogové okno označující, že oznámení byla přihlášena k odběru.

    ![Ukázkové oznámení v systému iOS][1]

    Když zvolíte **Přihlásit se k odběru**, aplikace převede vybrané kategorie na značky a požádá o registraci nového zařízení pro vybrané značky z centra oznámení.

2. Zadejte zprávu, která má být odeslána jako aktuality, a stiskněte tlačítko **Odeslat oznámení.** Případně spusťte aplikaci konzoly .NET a vygenerujte oznámení.

    ![Změna předvoleb oznámení v iOS][2]

3. Každé zařízení, které se přihlásilo k odběru nejnovějších zpráv, obdrží aktuální zprávy, které jste právě odeslali.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste odeslali oznámení vysílání na konkrétní zařízení se systémem iOS, které se zaregistrovaly pro kategorie. Chcete-li se dozvědět, jak nabízená lokalizovaná oznámení, přejdete k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Odesílání lokalizovaných oznámení](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Portál Azure]: https://portal.azure.com
