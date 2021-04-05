---
title: Posílání nabízených oznámení na konkrétní zařízení s iOS pomocí Azure Notification Hubs | Microsoft Docs
description: V tomto kurzu se naučíte používat službu Azure Notification Hubs k posílání nabízených oznámení na konkrétní zařízení s iOS.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 2cb979491e247a4d44b9ae9ae27c433fb3f436d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579220"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Kurz: odeslání nabízených oznámení na konkrétní zařízení s iOS pomocí Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak pomocí Azure Notification Hubs vysílat oznámení o novinkách do aplikace pro iOS. Po dokončení se můžete zaregistrovat k nezúčastněným kategoriím zpráv, které vás zajímají, a pro tyto kategorie přijímat jenom nabízená oznámení. Tento scénář se běžně používá v řadě aplikací, které posílají oznámení skupinám uživatelům, kteří o ně projevili zájem. Může jít třeba o čtečku RSS, aplikaci pro hudební fanoušky atd.

Scénáře vysílání povolíte tak, že při registraci v centru oznámení přidáte jednu nebo více *značek*. Po odeslání oznámení do značky obdrží oznámení zařízení, která jsou zaregistrovaná pro značku. Značky jsou jednoduše řetězce, které se nemusejí vytvářet předem. Další informace o značkách najdete v článku [Směrování a výrazy značek ve službě Notification Hubs](notification-hubs-tags-segment-push-message.md).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Přidání výběru kategorie do aplikace
> * Posílání označených oznámení
> * Odeslat oznámení ze zařízení
> * Spuštění aplikace a generování oznámení

## <a name="prerequisites"></a>Požadavky

Toto téma se sestavuje v aplikaci, kterou jste vytvořili v [kurzu: nabízená oznámení do aplikací pro iOS pomocí Azure Notification Hubs][get-started]. Před zahájením tohoto kurzu musíte mít již dokončený [kurz: nabízená oznámení do aplikací pro iOS pomocí Azure Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Přidání výběru kategorií do aplikace

Prvním krokem je přidání prvků uživatelského rozhraní do stávajícího scénáře, který umožňuje uživateli vybrat kategorie k registraci. Kategorie, které uživatel vybere, jsou uložené v zařízení. Při spuštění aplikace se v centru oznámení provede registrace zařízení s vybranými kategoriemi ve formě značek.

1. V **MainStoryboard_iPhone. ve scénáři** přidejte do knihovny objektů následující komponenty:

   * Popisek s textem "průlom News",
   * Popisky s texty kategorií "World", "politika", "Business", "Technology", "věda", "Sport",
   * Šest přepínačů, jeden pro každou kategorii, **nastavte ve výchozím** nastavení všechny **stavy** přepínačů.
   * Jedno tlačítko s označením "předplatné"

     Scénář by měl vypadat takto:

     ![Tvůrce rozhraní Xcode][3]

2. V editoru pomocníka vytvořte pro všechny přepínače možnost vzdálení a zavolejte je "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch".

3. Vytvořte akci pro tlačítko s názvem `subscribe` . váš `ViewController.h` obsah by měl obsahovat následující kód:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Vytvořte novou **třídu pro kakao Touch** s názvem `Notifications` . Zkopírujte následující kód do oddílu rozhraní oznámení souboru. h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Přidejte následující direktivu import do oznámení. m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Zkopírujte následující kód do části implementace v souboru Notifications. m.

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
        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Tato třída používá místní úložiště k ukládání a načítání kategorií zpráv, které toto zařízení obdrží. Obsahuje také metodu pro registraci těchto kategorií pomocí registrace [šablony](notification-hubs-templates-cross-platform-push-messages.md) .

7. V `AppDelegate.h` souboru přidejte příkaz Import pro `Notifications.h` a přidejte vlastnost pro instanci `Notifications` třídy:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. V `didFinishLaunchingWithOptions` metodě v `AppDelegate.m` přidejte kód pro inicializaci instance oznámení na začátku metody.  
    `HUBNAME` a `HUBLISTENACCESS` (definované `hubinfo.h` ) by již měly mít `<hub name>` `<connection string with listen access>` zástupné symboly a nahrazeny názvem vašeho centra oznámení a připojovacím řetězcem pro *DefaultListenSharedAccessSignature* , které jste získali dříve.

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Obecně platí, že přihlašovací údaje distribuované klientskou aplikací nejsou příliš bezpečné, a proto byste měli s klientskou aplikací distribuovat jenom přístupový klíč pro naslouchání. Přístup pro naslouchání umožňuje aplikaci registrovat oznámení, ale nedovolí měnit stávající registrace ani odesílat oznámení. Plný přístupový klíč se používá v zabezpečené back-endové službě k posílání oznámení a změně stávajících registrací.

9. V `didRegisterForRemoteNotificationsWithDeviceToken` metodě v `AppDelegate.m` nahraďte kód v metodě následujícím kódem pro předání tokenu zařízení do `notifications` třídy. `notifications`Třída provádí registraci pro oznámení s kategoriemi. Pokud uživatel změní výběr kategorie, zavolejte `subscribeWithCategories` metodu v reakci na tlačítko přihlásit k **odběru** a aktualizujte je.

    > [!NOTE]
    > Vzhledem k tomu, že token zařízení přiřazený Apple Push Notification Service (APNS) se může kdykoli změnit, měli byste se často zaregistrovat k oznámením, aby nedocházelo k chybám oznámení. V tomto příkladu se oznámení registrují při každém spuštění aplikace. Pokud se aplikace spouštějí často, třeba častěji než jednou denně, pravděpodobně můžete registraci přeskočit kvůli úspoře šířky pásma, protože od předchozí registrace neuplynul ani den.

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

    V tomto okamžiku by neměl existovat žádný jiný kód v `didRegisterForRemoteNotificationsWithDeviceToken` metodě.

10. Následující metody by již měly být přítomny v `AppDelegate.m` tématu dokončení kurzu [začínáme s Notification Hubs][get-started] . Pokud ne, přidejte je.

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

    Tato metoda zpracovává oznámení přijatá při spuštění aplikace zobrazením jednoduchého **UIAlertu**.

11. V `ViewController.m` přidejte `import` příkaz pro `AppDelegate.h` a zkopírujte následující kód do metody vygenerované `subscribe` metodou Xcode. Tento kód aktualizuje registraci oznámení, aby používal nové značky kategorií, které uživatel zvolil v uživatelském rozhraní.

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

    Tato metoda vytváří `NSMutableArray` kategorie a používá `Notifications` třídu k uložení seznamu v místním úložišti a registruje odpovídající značky v centru oznámení. Při změně kategorií se vytvoří registrace s novými kategoriemi.

12. V `ViewController.m` přidejte následující kód do `viewDidLoad` metody pro nastavení uživatelského rozhraní na základě dříve uložených kategorií.

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

Aplikace teď může ukládat sadu kategorií v místním úložišti zařízení, které se používá k registraci v centru oznámení při každém spuštění aplikace. Uživatel může změnit výběr kategorií za běhu a kliknout na `subscribe` metodu pro aktualizaci registrace zařízení. V dalším kroku aktualizujete aplikaci tak, aby odesílala oznámení o novinkách přímo do samotné aplikace.

## <a name="optional-send-tagged-notifications"></a>volitelné Odesílat označená oznámení

Pokud nemáte přístup k aplikaci Visual Studio, můžete přejít k další části a odesílat oznámení z aplikace samotné. Můžete také odeslat správné oznámení šablony z [Azure Portal] pomocí karty ladění v centru oznámení.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>volitelné Odeslat oznámení ze zařízení

Obvykle se oznámení odesílají pomocí back-end služby, ale můžete posílat oznámení o novinkách přímo z aplikace. Provedete to tak, že aktualizujete `SendNotificationRESTAPI` metodu, kterou jste definovali v kurzu [začínáme s Notification Hubs][get-started] .

1. V nástroji `ViewController.m` aktualizujte `SendNotificationRESTAPI` metodu následujícím způsobem tak, aby přijímala parametr pro značku kategorie a odesílala správné oznámení [šablon](notification-hubs-templates-cross-platform-push-messages.md) .

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

2. V nástroji `ViewController.m` aktualizujte `Send Notification` akci, jak je znázorněno v následujícím kódu. Takže pošle oznámení s použitím jednotlivých značek jednotlivě a pošle je na několik platforem.

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

3. Znovu sestavte projekt a ujistěte se, že nedošlo k chybám sestavení.

## <a name="run-the-app-and-generate-notifications"></a>Spuštění aplikace a generování oznámení

1. Stiskněte tlačítko Spustit a sestavte projekt a spusťte aplikaci. Vyberte některé možnosti pro průlomové zprávy, které se mají přihlásit k odběru, a pak stiskněte tlačítko **přihlásit k odběru** . Mělo by se zobrazit dialogové okno, které indikuje, že se oznámení přihlásila.

    ![Příklad oznámení v iOS][1]

    Když zvolíte **přihlášení k odběru**, aplikace převede vybrané kategorie na značky a požádá o nové registrace zařízení pro vybrané značky z centra oznámení.

2. Zadejte zprávu, která se pošle jako podzprávy s novinkami, a pak stiskněte tlačítko **Odeslat oznámení** . Případně spusťte konzolovou aplikaci .NET pro generování oznámení.

    ![Změna předvoleb oznámení v iOS][2]

3. Každé zařízení, které se přihlásí k odběru nových zpráv, obdrží oznámení o novinkách, která jste právě odeslali.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste odeslali oznámení všesměrového vysílání na konkrétní zařízení s iOS, která jsou zaregistrovaná pro tyto kategorie. Pokud se chcete dozvědět, jak doručovat lokalizované oznámení, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Odesílání lokalizovaných oznámení](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/dn530749(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[get-started]: ios-sdk-get-started.md
[Azure Portal]: https://portal.azure.com
