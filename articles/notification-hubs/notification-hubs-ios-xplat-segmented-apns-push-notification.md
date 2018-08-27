---
title: Nabízená oznámení do zařízení s Iosem konkrétní pomocí Azure Notification Hubs | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do zařízení s Iosem konkrétní.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 18caf2b1b96052d93737c8a9815e2e6643a52a67
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918058"
---
# <a name="tutorial-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Kurz: Nabízená oznámení do zařízení s Iosem konkrétní pomocí Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak používat Azure Notification Hubs k rozesílání mimořádných zpráv do aplikace pro iOS. Jakmile budete hotovi, se můžete připojit k registraci zásadní nové kategorie, které vás zajímají a přijímat pouze nabízená oznámení pro tyto kategorie. Tento scénář se běžně používá v řadě aplikací, které posílají oznámení skupinám uživatelům, kteří o ně projevili zájem. Může jít třeba o čtečku RSS, aplikaci pro hudební fanoušky atd.

Scénáře vysílání povolíte tak, že při registraci v centru oznámení přidáte jednu nebo více *značek*. Pokud jsou oznámení odesílána značku, zařízení, které jste se zaregistrovali pro značku dostat oznámení. Značky jsou jednoduše řetězce, které se nemusejí vytvářet předem. Další informace o značkách najdete v článku [Směrování a výrazy značek ve službě Notification Hubs](notification-hubs-tags-segment-push-message.md).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Přidat výběr kategorie aplikace
> * Posílání označených oznámení
> * Odesílání oznámení ze zařízení
> * Spuštění aplikace a generování oznámení

## <a name="prerequisites"></a>Požadavky

Toto téma staví na aplikaci, kterou jste vytvořili v [kurz: nabízená oznámení do aplikace pro iOS pomocí Azure Notification Hubs][get-started]. Před zahájením tohoto kurzu, musí už jste dokončili [kurz: nabízená oznámení do aplikace pro iOS pomocí Azure Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Přidání výběru kategorií do aplikace

Prvním krokem je přidání prvků uživatelského rozhraní na existující scénář, který uživateli umožňuje vybrat kategorie k registraci. Kategorie, které uživatel vybere, jsou uložené v zařízení. Při spuštění aplikace se v centru oznámení provede registrace zařízení s vybranými kategoriemi ve formě značek.

1. Ve vaší **MainStoryboard_iPhone.storyboard** z objektu knihovny přidejte následující součásti:

    * Popisek s textem "Novinkách."
    * Popisky se texty kategorie "World", "Politika", "Obchodní", "Technologie", "Přírodověda", "Sports"
    * Šest přepínače, jeden pro každou kategorii, nastavit každý přepínač **stavu** bude **vypnout** ve výchozím nastavení.
    * Jedno tlačítko s popiskem "Přihlásit k odběru"

    Scénář by měl vypadat takto:

    ![Tvůrce rozhraní Xcode][3]

2. V editoru Pomocníka s nastavením, vytvoříte výstupy pro všechny přepínače a volat "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"
3. Vytvoření akce pro tlačítko volá **odběru**. Vaše ViewController.h by měl obsahovat následující kód:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Vytvořte nový **třída Cocoa Touch** volá `Notifications`. V části rozhraní souboru Notifications.h zkopírujte následující kód:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Přidejte následující direktivy importu Notifications.m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. V oddílu implementace souboru Notifications.m zkopírujte následující kód.

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

    Tato třída používá místní úložiště k ukládání a načítání kategorie zpráv, který přijme toto zařízení. Kromě toho obsahuje metody pro registraci pro tyto kategorie pomocí [šablony](notification-hubs-templates-cross-platform-push-messages.md) registrace.

7. Do souboru AppDelegate.h přidejte příkaz import pro Notifications.h a přidejte vlastnost pro instanci třídy oznámení:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. V **didFinishLaunchingWithOptions** metoda ve AppDelegate.m, přidat kód pro inicializaci instance oznámení na začátku metody.  

    `HUBNAME` a `HUBLISTENACCESS` (definované v souboru hubinfo.h) byste už měli mít `<hub name>` a `<connection string with listen access>` zástupné symboly nahradí název vašeho centra oznámení a připojovacího řetězce pro *DefaultListenSharedAccessSignature* který jste získali dříve

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Obecně platí, že přihlašovací údaje distribuované klientskou aplikací nejsou příliš bezpečné, a proto byste měli s klientskou aplikací distribuovat jenom přístupový klíč pro naslouchání. Přístup pro naslouchání umožňuje aplikaci registrovat oznámení, ale nedovolí měnit stávající registrace ani odesílat oznámení. Plný přístupový klíč se používá v zabezpečené back-endové službě k posílání oznámení a změně stávajících registrací.

9. V **didRegisterForRemoteNotificationsWithDeviceToken** metoda ve AppDelegate.m, nahraďte kód v metodě s následujícím kódem předat token zařízení pro třídu oznámení. Třída oznámení provádí registrace k oznámením pomocí kategorií. Pokud uživatel změní výběr kategorie, zavolejte `subscribeWithCategories` metody v reakci na **odběru** tlačítko je aktualizovat.

    > [!NOTE]
    > Vzhledem k tomu, že v každém okamžiku může šance token zařízení přiřazené podle Apple Push Notification Service (APNS), byste měli zaregistrovat pro oznámení často, aby se zabránilo chybám oznámení. V tomto příkladu se oznámení registrují při každém spuštění aplikace. Pokud se aplikace spouštějí často, třeba častěji než jednou denně, pravděpodobně můžete registraci přeskočit kvůli úspoře šířky pásma, protože od předchozí registrace neuplynul ani den.

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

    V tomto okamžiku by měla existovat žádný kód v **didRegisterForRemoteNotificationsWithDeviceToken** metody.

10. Následující metody by měly být již k dispozici v AppDelegate.m Dokončit [Začínáme s Notification Hubs] [ get-started] kurzu. Pokud ne, je přidat.

    ```objc
    -(void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    * (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Tato metoda obsluhuje oznámení, když je aplikace spuštěna, zobrazením jednoduchého **UIAlert**.

11. V ViewController.m, AppDelegate.h přidejte příkaz import a zkopírujte následující kód do XCode vygenerovat **odběru** metody. Tento kód aktualizuje registrace oznámení používat nové značky kategorií, které uživatel se rozhodl v uživatelském rozhraní.

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
            [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Tato metoda vytvoří **NSMutableArray** kategorií a používá **oznámení** třídy pro uložení seznamu v místním úložišti a registry, odpovídající značky ve vašem Centru oznámení. Při změně kategorií se vytvoří registrace s novými kategoriemi.

3. V ViewController.m, přidejte následující kód **viewDidLoad** metody nastavte uživatelské rozhraní založené na dříve uložený kategoriích.

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

Aplikace teď můžete ukládat sadu kategorií v místním úložišti zařízení použije k registraci v centru oznámení při každém spuštění aplikace. Uživatel může změnit výběr kategorií v modulu runtime a klikněte na tlačítko **odběru** metoda se aktualizovat registraci zařízení. Dále aktualizujete aplikaci k odesílání mimořádných zpráv přímo v samotné aplikaci.

## <a name="optional-send-tagged-notifications"></a>(volitelné) Odesílání oznámení příznakem

Pokud nemáte přístup ke službě Visual Studio, můžete přeskočit k další části a odesílání oznámení z aplikace. Můžete také odeslat oznámení správné šablony z [Azure Portal] pomocí karty ladění pro vaše Centrum oznámení. 

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(volitelné) Odesílání oznámení ze zařízení

Obvykle bude odesláno oznámení pomocí back-end službu, ale můžete odesílat oznámení o aktuálních zprávách přímo z aplikace. Uděláte to tak, aktualizujte `SendNotificationRESTAPI` metodu, která jste definovali v [Začínáme s Notification Hubs] [ get-started] kurzu.

1. V `ViewController.m`, aktualizujte `SendNotificationRESTAPI` metody jako následuje tak, aby přijímá parametr kategorie značky a odešle správné [šablony](notification-hubs-templates-cross-platform-push-messages.md) oznámení.

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

2. V `ViewController.m`, aktualizujte **odeslat oznámení** akce, jak je znázorněno v následujícím kódu. Tak, aby odešle oznámení pomocí jednotlivé značky jednotlivě a odešle na více platforem.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, GCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Znovu sestavte projekt a ujistěte se, že máte žádné chyby buildu.

## <a name="run-the-app-and-generate-notifications"></a>Spuštění aplikace a generování oznámení

1. Stisknutím tlačítka Spustit se projekt sestavil a aplikace se spustila. Vyberte některé možnosti zprávy rozbíjející přihlásit k odběru a potom stiskněte klávesu **přihlásit k odběru** tlačítko. Zobrazí se dialogové okno oznamující, že oznámení jste se přihlásili k odběru.

    ![Příklad oznámení v iOS][1]

    Pokud zvolíte **přihlásit k odběru**, aplikace vybraných kategorií převádí značky a vyžaduje nové registrace zařízení u vybraných značek v centru oznámení.

2. Zadejte zprávu, která odeslat, protože stiskněte mimořádných zpráv **odeslat oznámení** tlačítko. Alternativně Spusťte konzolovou aplikaci .NET pro generování oznámení.

    ![Změnit předvolby oznámení v iOS][2]

3. Každé předplatné služby mimořádné zprávy obdržením mimořádných zpráv, které jste právě odeslali.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zaslali oznámení vysílání specifické pro zařízení s iOS, které jste se zaregistrovali do kategorií. Informace o k odesílání lokalizovaných oznámení, přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
>[Odesílání lokalizovaných oznámení](notification-hubs-ios-xplat-localized-apns-push-notification.md)


<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Azure Portal]: https://portal.azure.com
