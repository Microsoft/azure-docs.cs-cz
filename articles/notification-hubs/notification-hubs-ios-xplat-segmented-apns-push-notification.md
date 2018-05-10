---
title: Nabízená oznámení na konkrétní iOS zařízení pomocí Azure Notification Hubs | Microsoft Docs
description: V tomto kurzu zjistěte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do zařízení s iOS konkrétní.
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
ms.openlocfilehash: f6096238deb2186edfac2eb9d1c9a9e76db07553
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Kurz: Nabízená oznámení na konkrétní iOS zařízení pomocí Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak používat Azure Notification Hubs k vysílání oznámení o aktuálních zprávách aplikace pro iOS. Po dokončení je možné zaregistrovat pro nejnovější novinky kategorií, které vás zajímají a přijímat pouze nabízená oznámení pro tyto kategorie. Tento scénář je běžný vzor velký počet aplikací, kde mají oznámení k odeslání do skupiny uživatelů, kteří mají předtím nebyl deklarovaný zájem, například, čtečku RSS, aplikace pro Hudba ventilátory, atd.

Všesměrového vysílání scénáře jsou povolené zahrnutím jeden nebo více *značky* při vytváření registrace v centru oznámení. Když oznámení se odesílají do značku, zobrazí se zařízení, která byla zaregistrovaná pro značku oznámení. Protože značky jsou jednoduše řetězce, nemají být předem zřízená. Další informace o značkách najdete v tématu [směrování centra oznámení a značky výrazy](notification-hubs-tags-segment-push-message.md).

V tomto kurzu proveďte následující kroky:

> [!div class="checklist"]
> * Přidat výběru kategorie do aplikace
> * Odesílání oznámení s příznakem
> * Odeslat oznámení ze zařízení
> * Spusťte aplikaci a generovat upozornění

## <a name="prerequisites"></a>Požadavky
Toto téma je založený na aplikaci, kterou jste vytvořili v [kurz: nabízená oznámení do aplikace pro iOS pomocí Azure Notification Hubs][get-started]. Před zahájením tohoto kurzu, musí jste již dokončili [kurz: nabízená oznámení do aplikace pro iOS pomocí Azure Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Přidat výběru kategorie do aplikace
Prvním krokem je přidání prvky uživatelského rozhraní do vaší stávající scénáře, který uživateli umožňuje výběr kategorií k registraci. Kategorie, které uživatel jsou uloženy v zařízení. Při spuštění aplikace registrace zařízení se vytvoří v centru oznámení s vybrané kategorie jako značky.

1. Ve vašem MainStoryboard_iPhone.storyboard přidejte následující součásti z objektu knihovny:
   
   * Popisek s textem "Novinkách."
   * Popisky se texty kategorie "World", "Politika", "Firemní", "Technologie", "Vědecké účely", "Sports",
   * Šest přepínače, jeden pro každou kategorii nastavit každý přepínač **stavu** být **vypnout** ve výchozím nastavení.
   * Jedno tlačítko s označením "Přihlásit k odběru"
     
     Vaše scénáře by měla vypadat takto:
     
     ![][3]
2. V editoru pomocníka vytvořit výstupy u všech přepínačů a volat je "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"
3. Vytvoří akci pro tlačítko názvem **přihlášení k odběru**. Vaše ViewController.h by měl obsahovat následující kód:
   
    ```obj-c
        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;
   
        - (IBAction)subscribe:(id)sender;
    ```
4. Vytvořte novou **kakao Touch třída** názvem `Notifications`. V části rozhraní souboru Notifications.h zkopírujte následující kód:
   
    ```obj-c
        @property NSData* deviceToken;
   
        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;
   
        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;
   
        - (NSSet*)retrieveCategories;
   
        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```
5. Přidejte následující direktivy importu Notifications.m:
   
    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```
6. Zkopírujte následující kód v oddílu implementace souboru Notifications.m.
   
    ```obj-c
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

    Tato třída se používá místní úložiště pro ukládání a načítání kategorií příspěvků, který obdrží toto zařízení. Také obsahuje metody pro registraci pro tyto kategorie pomocí [šablony](notification-hubs-templates-cross-platform-push-messages.md) registrace.

1. V souboru AppDelegate.h přidejte příkaz import pro Notifications.h a přidejte vlastnost instance třídy oznámení:
   
    ```obj-c
        #import "Notifications.h"
   
        @property (nonatomic) Notifications* notifications;
    ```
2. V **didFinishLaunchingWithOptions** metoda v AppDelegate.m, přidat kód pro inicializaci instance oznámení na začátku metody.  
   
    `HUBNAME` a `HUBLISTENACCESS` (definovanou v souboru hubinfo.h) byste již měli mít `<hub name>` a `<connection string with listen access>` nahradit zástupné symboly pomocí názvu centra oznámení a připojovacího řetězce pro *DefaultListenSharedAccessSignature* který jste získali dříve
   
    ```obj-c
        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```
   
   > [!NOTE]
   > Protože přihlašovací údaje, které jsou distribuované s klientskou aplikaci není obvykle zabezpečení, by měl distribuovat klíč pro naslouchání přístup pouze s vaší klientské aplikace. Poslechněte umožní přístup k aplikaci zaregistrovat pro oznámení, ale existující registrace nemůže být upravena a nelze odeslat oznámení. Úplný přístup klíč se používá ve službě Zabezpečené back-end pro zasílání oznámení a změna existující registrace.
   > 
   > 
3. V **didRegisterForRemoteNotificationsWithDeviceToken** metoda v AppDelegate.m, nahraďte kód v metodě následujícím kódem předat token zařízení k třídě oznámení. Třída oznámení provádí registrace pro oznámení s kategorií. Pokud uživatel změní výběru kategorií, zavolejte `subscribeWithCategories` metoda v reakci na **přihlášení k odběru** tlačítko je aktualizovat.
   
   > [!NOTE]
   > Protože token zařízení, které jsou přiřazené podle APNS Apple Push Notification Service () můžete kdykoli šance, byste měli zaregistrovat pro oznámení často, aby se zabránilo selhání oznámení. V tomto příkladu se zaregistruje pro oznámení při každém spuštění aplikace. Pro aplikace, které jsou často spouštíte více než jednou denně, můžete pravděpodobně přeskočit registraci byla zachována šířka pásma, pokud od předchozí registrace uplynul méně než jeden den.
   > 
   > 
   
    ```obj-c
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

    V tomto okamžiku by měla existovat žádný kód v **didRegisterForRemoteNotificationsWithDeviceToken** metoda.

1. Následující metody již mají být dostupné v AppDelegate.m dokončení [Začínáme s Notification Hubs] [ get-started] kurzu. Pokud ne, přidejte je.
   
    ```obj-c    
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
   
   Tato metoda zpracovává oznámení obdržená při spuštěné aplikaci tím, že zobrazuje jednoduchou **UIAlert**.
2. V ViewController.m, přidejte příkaz import pro AppDelegate.h a zkopírujte následující kód do generované XCode **přihlášení k odběru** metoda. Tento kód aktualizuje registrace oznámení používat nové značky kategorie, které uživatelem vybrané v uživatelském rozhraní.
   
    ```obj-c
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

   Tato metoda vytvoří **NSMutableArray** kategorií a používá **oznámení** třídy pro uložení seznamu místní úložiště a registrů, odpovídající značky pomocí centra oznámení. Při změně kategorií, registrace se znovu vytvoří se nové kategorie.
3. V ViewController.m, přidejte následující kód do **viewDidLoad** metodu a nastavit uživatelské rozhraní založené na dříve uloženou kategoriích.

    ```obj-c    
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


Aplikaci teď můžete uložit sadu kategorií v místním úložišti zařízení používá k registraci do centra oznámení při každém spuštění aplikace. Uživatel může změnit výběr kategorie v modulu runtime a kliknutím **přihlášení k odběru** metoda aktualizace registrace pro zařízení. Potom aktualizujte aplikaci odesílat oznámení o aktuálních zprávách přímo do aplikace.

## <a name="optional-send-tagged-notifications"></a>(volitelné) Odesílání oznámení s příznakem
Pokud nemáte přístup k sadě Visual Studio, můžete přeskočit k další části a odesílání oznámení z aplikace. Můžete také odeslat oznámení správné šablony z [portál Azure] pomocí karty ladění pro vaše Centrum oznámení. 

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(volitelné) Odeslat oznámení ze zařízení
Obvykle bude odesláno oznámení přes službu back-end, ale můžete odesílat oznámení o aktuálních zprávách přímo z aplikace. Uděláte to tak, aktualizujte `SendNotificationRESTAPI` metoda, která je definována v [Začínáme s Notification Hubs] [ get-started] kurzu.

1. V `ViewController.m`, aktualizovat `SendNotificationRESTAPI` jako metodu následuje tak, aby přijímá parametr pro značku kategorie a odešle správné [šablony](notification-hubs-templates-cross-platform-push-messages.md) oznámení.
   
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
2. V `ViewController.m`, aktualizovat **odeslat oznámení** akce, jak je znázorněno v následujícím kódu. Tak, aby k odesílání upozornění pomocí jednotlivé značky jednotlivě a odešle na více platforem.

    ```obj-c
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


1. Znovu sestavte projekt a ujistěte se, že máte žádné chyby sestavení.

## <a name="run-the-app-and-generate-notifications"></a>Spusťte aplikaci a generovat upozornění
1. Klikněte na tlačítko spustit pro sestavení projektu a spusťte aplikaci. Vyberte některé zprávy možnosti ukončování řádků pro přihlášení k odběru a potom stiskněte klávesu **přihlásit k odběru** tlačítko. Měli byste vidět, zobrazí se dialogové okno oznamující, že se odebírá oznámení.
   
    ![][1]
   
    Pokud vyberete **přihlásit k odběru**, převede vybraných kategorií značky a požaduje novou registraci zařízení pro vybranou značky z centra oznámení aplikace.
2. Zadejte zprávu k odeslání jako novinek stiskněte **odeslat oznámení** tlačítko. Alternativně spusťte konzolové aplikace .NET pro generování oznámení.
   
    ![][2]
3. Každé zařízení předplatné nejnovější zprávy přes obdrží oznámení o aktuálních zprávách, které jste poslali.

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zaslali oznámení vysílání specifické pro zařízení s iOS které zapsány kategorie. Informace o tom tak, aby nabízel lokalizované oznámení, přechodu na následující kurzu: 

> [!div class="nextstepaction"]
>[Nabízená oznámení lokalizované](notification-hubs-ios-xplat-localized-apns-push-notification.md)


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
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[portál Azure]: https://portal.azure.com
