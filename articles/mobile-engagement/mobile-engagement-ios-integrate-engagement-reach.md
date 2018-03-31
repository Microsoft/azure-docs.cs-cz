---
title: Azure Mobile Engagement iOS dosáhnout integraci sady SDK | Microsoft Docs
description: Nejnovější aktualizace a postupy pro iOS SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1f5f5857-867c-40c5-9d76-675a343a0296
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: 8d531f5850e8f7f352774f5894285402bd4cc53e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-reach-on-ios"></a>Jak integrovat Engagement Reach v systému iOS
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Je třeba provést postup integrace popsaný v tématu [jak integrovat Engagement iOS dokumentu](mobile-engagement-ios-integrate-engagement.md) před těchto pokynů.

Tato dokumentace vyžaduje XCode 8. Pokud jste ve skutečnosti závisí na XCode 7 pak můžete použít [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Je známého problému na tato předchozí verze při spuštění v zařízení s iOS 10: systémová oznámení nejsou reagovali. Chcete-li tomu máte k implementaci nepoužívané rozhraní API `application:didReceiveRemoteNotification:` ve vaší aplikaci delegovat následujícím způsobem:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Toto řešení nedoporučujeme** jako toto chování můžete změnit v jakéhokoli upgradu verze iOS nadcházející (i dílčí), protože tato rozhraní API pro iOS je zastaralý. Můžete přepnout do XCode 8 co nejdříve.
>
>

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Povolení přijímání bezobslužných nabízených oznámení v aplikaci
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

## <a name="integration-steps"></a>Kroky integrace
### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Vložení Engagement Reach SDK do projektu iOS
* Přidejte sady Reach sdk do projektu Xcode. V prostředí Xcode přejděte na **projektu \> přidat do projektu** a zvolte `EngagementReach` složky.

### <a name="modify-your-application-delegate"></a>Úprava delegáta aplikace
* V horní části souboru implementace importujte modul Engagement Reach:

      [...]
      #import "AEReachModule.h"
* Uvnitř metody `applicationDidFinishLaunching:` nebo `application:didFinishLaunchingWithOptions:`, vytvořte modul kampaně reach a předejte jej do existujícího inicializačního řádku Engagement:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]

        return YES;
      }
* Upravit **'icon.png'** řetězec s názvem image chcete použít jako ikona oznámení.
* Pokud budete chtít použít možnost *aktualizace oznámení "BADGE" hodnota* v kampaně Reach nebo pokud chcete použít nativního nabízení \<SaaS/Reach API nebo kampaň formátu/nativní nabízená\> kampaní, je nutné nechat modul Reach spravovat oznámení "BADGE" Ikona samotné (bude automaticky vymaže oznámení aplikace a také obnovit s hodnotou uloženou ve Engagement pokaždé, když je aplikace spuštěna nebo foregrounded). K tomu je potřeba po inicializaci modulu Reach přidáte tento řádek:

      [reach setAutoBadgeEnabled:YES];
* Pokud chcete zpracovávat Reach datová oznámení, je nutné nechat delegáta aplikace požadavkům `AEReachDataPushDelegate` protokolu. Po inicializaci modulu Reach přidejte následující řádek:

      [reach setDataPushDelegate:self];
* Pak můžete implementovat metody `onDataPushStringReceived:` a `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` v delegáta aplikace:

      -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
      {
         NSLog(@"String data push message with category <%@> received: %@", category, body);
         return YES;
      }

      -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
      {
         NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
         // Do something useful with decodedBody like updating an image view
         return YES;
      }

### <a name="category"></a>Kategorie
Kategorie parametr je volitelný při vytváření kampaň nabízených Data a umožňuje že vám data filtru nabízených oznámení. To je užitečné, pokud chcete push různých druhů z `Base64` dat a chcete určit jejich typů před analýzou je.

**Aplikace je nyní připraven přijmout a zobrazit obsah reach!**

## <a name="how-to-receive-announcements-and-polls-at-any-time"></a>Jak přijmout oznámení a hlasování kdykoli
Zapojení můžete koncovým uživatelům odeslat oznámení Reach kdykoli pomocí služby Apple Push Notification Service.

Chcete-li povolit tuto funkci, budete muset připravit aplikace pro Apple nabízená oznámení a úprava delegáta aplikace.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Příprava aplikace pro nabízená oznámení Apple
Postupujte podle průvodce: [postup přípravy aplikace pro nabízená oznámení Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Přidejte kód klienta
*Aplikace v tomto okamžiku měli registrované nabízeného certifikátu Apple front-endu zapojení.*

Pokud už není potřeba, budete muset registraci aplikace na příjem nabízených oznámení.

* Import `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>
* Přidejte následující řádek při spuštění aplikace (obvykle ve `application:didFinishLaunchingWithOptions:`):

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

Pak musíte poskytnout Engagement token zařízení vrácený Apple servery. To se provádí v metodu s názvem `application:didRegisterForRemoteNotificationsWithDeviceToken:` v delegáta aplikace:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Nakonec budete muset informovat sady Engagement SDK, až aplikace obdrží upozornění na vzdálené. K tomu, volejte metodu `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` v delegáta aplikace:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [!IMPORTANT]
> Ve výchozím nastavení ovládací prvky completionHandler Engagement Reach. Pokud chcete ručně reagovat na `handler` blokovat ve vašem kódu, můžete předat hodnotu nil `handler` argument a řízení dokončení blokovat sami. Najdete v článku `UIBackgroundFetchResult` typ seznam možných hodnot.
>
>

### <a name="full-example"></a>Úplný příklad
Zde je úplná ukázka integrace:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Řešení konfliktů UNUserNotificationCenter delegáta

*Pokud se implementuje ani aplikace, nebo jeden z vašich knihovnách třetích stran `UNUserNotificationCenterDelegate` pak můžete tuto část přeskočit.*

A `UNUserNotificationCenter` delegáta se sadou SDK používá k monitorování životní cyklus Engagement oznámení na zařízení se systémem iOS, 10 nebo vyšší. Sada SDK obsahuje vlastní implementaci `UNUserNotificationCenterDelegate` protokolu, ale může být jen jedna `UNUserNotificationCenter` delegovat na aplikaci. Všechny ostatní delegáta přidán do `UNUserNotificationCenter` budou v konfliktu s zapojení jeden objekt. Pokud sada SDK zjistí nebo jakékoli jiné třetí strany delegáta jej nebude získáte možnost vyřešte konflikty používat vlastní implementaci. Budete muset přidat logiku Engagement vlastní delegáta za účelem vyřešení konfliktů.

Existují dva způsoby, jak dosáhnout.

Návrh 1, jednoduše tak, že předávání delegát volání sady SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Návrh 2, která dědí z nebo `AEUserNotificationHandler` – třída

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> Můžete určit, zda oznámení pochází z Engagement nebo není předáním jeho `userInfo` slovníku agentovi `isEngagementPushPayload:` třídy metoda.

Ujistěte se, že `UNUserNotificationCenter` objektu delegáta je nastaven na vaše delegáta v rámci buď `application:willFinishLaunchingWithOptions:` nebo `application:didFinishLaunchingWithOptions:` metoda delegáta aplikace.
Například pokud jste implementovali výše uvedený návrh 1:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code

        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

## <a name="how-to-customize-campaigns"></a>Postup přizpůsobení kampaně
### <a name="notifications"></a>Oznámení
Existují dva typy oznámení: oznámení systému a v aplikaci.

Systémová oznámení jsou zpracovávány iOS a nelze upravovat.

Oznámení v aplikaci probíhají zobrazení, která se dynamicky přidá do okna aktuální aplikace. Tomu se říká překrytí oznámení. Překryvy oznámení jsou skvělý pro rychlé integraci, protože se nevyžaduje upravit všechna zobrazení v aplikaci.

#### <a name="layout"></a>Rozložení
Pokud chcete upravit vzhled oznámení v aplikaci, můžete jednoduše upravit soubor `AENotificationView.xib` vašim potřebám, pokud ponechat hodnoty značky a typy dílčích existující zobrazení.

Ve výchozím nastavení jsou oznámení v aplikaci zobrazí v dolní části obrazovky. Pokud chcete zobrazit v horní části obrazovky, upravit poskytnutého `AENotificationView.xib` a změňte `AutoSizing` vlastnosti hlavní zobrazení, může být uchováván v horní části jeho superview.

#### <a name="categories"></a>Kategorie
Při úpravě zadané rozložení upravíte vzhledu všechna oznámení. Kategorie umožňují definovat různé cílové vypadá (pravděpodobně chování) pro oznámení. Kategorie lze při vytváření kampaně Reach. Mějte na paměti, že kategorie vám také umožní přizpůsobit oznámení a hlasování, který je popsán dále v tomto dokumentu.

Chcete-li zaregistrovat kategorie obslužnou rutinu pro oznámení, přidejte volání, jakmile se inicializovat modul reach.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier` musí být instancí objektu, který vyhovuje protokolu `AENotifier`.

Můžete implementovat metodu protokolu pomocí sami nebo můžete přeimplementovat existující třída `AEDefaultNotifier` kterém již provádí většinu činností.

Například pokud chcete znovu definovat zobrazení oznámení pro konkrétní kategorii, proveďte tento příklad:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Tento jednoduchý příklad kategorie Předpokládejme, že máte soubor s názvem `MyNotificationView.xib` ve vaší sady hlavní aplikace. Pokud není schopen najít odpovídající metodu `.xib`, se nezobrazí oznámení a Engagement na výstup zprávu, v konzole.

Soubor poskytnutý nib by měly dodržovat následující pravidla:

* Měl by obsahovat pouze jedno zobrazení.
* Dílčích zobrazení by měl být typů stejné jako ty, které v zadané nib souboru s názvem `AENotificationView.xib`
* Dílčích zobrazení musí mít stejné značky jako ty, které uvnitř poskytnutého nib soubor s názvem `AENotificationView.xib`

> [!TIP]
> Jednoduše zkopírujete soubor zadaný nib, s názvem `AENotificationView.xib`a mohli začít pracovat z ní. Ale buďte opatrní, zobrazení uvnitř tohoto souboru nib je přidružena k třídě `AENotificationView`. Tato třída předefinovat metodu `layoutSubViews` přesouvat a měnit velikost jeho dílčích zobrazení podle kontextu. Můžete chtít nahraďte ho `UIView` nebo vlastní zobrazení třída.
>
>

Pokud potřebujete podrobnější přizpůsobení oznámení (Pokud například chcete načíst zobrazení přímo z kódu), doporučuje se podívejte se na v zadaný zdroj kódu a třída dokumentaci `Protocol ReferencesDefaultNotifier` a `AENotifier`.

Všimněte si, že můžete použít stejné oznamovatel pro více kategorií.

Můžete také předefinovat oznamovatel výchozí takto:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Zpracování oznámení
Při použití výchozí kategorie, se nazývají některé metody životní cyklus na `AEReachContent` do sestavy statistik objektu a aktualizovat stav kampaně:

* Když se zobrazí oznámení v aplikaci `displayNotification` metoda je volána (který sestavy statistik) podle `AEReachModule` Pokud `handleNotification:` vrátí `YES`.
* Pokud se zavře oznámení, `exitNotification` metoda je volána, statistiky se použije v hlášení a další kampaně lze nyní zpracovat.
* Po kliknutí na oznámení `actionNotification` je volána, statistiky se použije v hlášení a přidružené akce se provádí.

Pokud vaši implementaci `AENotifier` obchází výchozí chování, budete muset pro volání těchto metod životní cyklus sami. Následující příklady ilustrují některých případech, kde přeskočí výchozí chování:

* Nerozšíříte `AEDefaultNotifier`, například implementována kategorie zpracování od začátku.
* Můžete overrode `prepareNotificationView:forContent:`, je nutné namapovat minimálně `onNotificationActioned` nebo `onNotificationExited` na jednu z vaší U.I ovládací prvky.

> [!WARNING]
> Pokud `handleNotification:` vyvolá výjimku, obsah se odstraní a `drop` je volána, to je uvedený v statistiky a další kampaně lze nyní zpracovat.
>
>

#### <a name="include-notification-as-part-of-an-existing-view"></a>Zahrnout oznámení jako součást stávající zobrazení
Překryvy se výborně hodí pro rychlé integrace, ale může být někdy není vhodné nebo může mít nežádoucí vedlejší účinky.

Pokud nejste s systému překrytí v některé z vaší zobrazení, můžete jej přizpůsobit pro tato zobrazení.

Můžete rozhodnout pro zahrnutí naše rozložení oznámení do vaší stávající zobrazení. To pokud chcete udělat, je k dispozici dva styly implementace:

1. Přidání zobrazení oznámení pomocí rozhraní tvůrce

   * Otevřete *rozhraní tvůrce*
   * Umístěte 320 x 60 (nebo 768 x 60, pokud jste na zařízení iPad) `UIView` místo, kam chcete zobrazit oznámení
   * Nastavit hodnotu značky pro toto zobrazení: **36822491**
2. Přidání zobrazení oznámení prostřednictvím kódu programu. Pokud byl inicializován zobrazení právě přidejte následující kód:

       UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
       notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
       [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG` Makro lze nalézt v `AEDefaultNotifier.h`.

> [!NOTE]
> Výchozí oznamovatel automaticky zjistí, že rozložení oznámení je zahrnuta v tomto zobrazení a nebude pro něj přidat překrytí.
>
>

### <a name="announcements-and-polls"></a>Oznámení a hlasování
#### <a name="layouts"></a>Rozložení
Můžete upravit soubory `AEDefaultAnnouncementView.xib` a `AEDefaultPollView.xib` tak dlouho, dokud ponechat hodnoty značky a typy dílčích existující zobrazení.

#### <a name="categories"></a>Kategorie
##### <a name="alternate-layouts"></a>Alternativní rozložení
Jako oznámení kategorie kampaně umožňuje mít alternativní rozložení pro oznámení a hlasování.

Pokud chcete vytvořit kategorii pro oznámení, musíte rozšířit **AEAnnouncementViewController** a zaregistrovat ji, jakmile byla inicializována modul reach:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [!NOTE]
> Pokaždé, když se uživatel kliknutím na oznámení pro oznámení v kategorii "Moje\_kategorie", řadiči registrované zobrazení (v takovém případě `MyCustomAnnouncementViewController`) budou inicializována pomocí volání metody `initWithAnnouncement:` a zobrazení se zařadí do aktuální okno aplikace.
>
>

V implementaci `AEAnnouncementViewController` třída budete muset čtení vlastnosti `announcement` k chybě při inicializaci vaší dílčích zobrazení. Podívejte se na příklad níže, kde dva popisků jsou inicializována pomocí `title` a `body` vlastnosti `AEReachAnnouncement` třídy:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Pokud nechcete, aby se načíst zobrazení samotnými, ale chcete znovu použít výchozí rozložení zobrazení oznámení, můžete jednoduše provést řadiči vlastní zobrazení rozšiřuje třídu zadaný `AEDefaultAnnouncementViewController`. V takovém případě duplicitní soubor nib `AEDefaultAnnouncementView.xib` a přejmenujte ji, mohou být načteny ve vašem řadiči vlastní zobrazení (pro řadič s názvem `CustomAnnouncementViewController`, by měly volat souboru nib `CustomAnnouncementView.xib`).

Pokud chcete nahradit výchozí kategorii oznámení, jednoduše zaregistrovat řadiči vlastní zobrazení pro kategorii definované v `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Hlasování lze upravit stejným způsobem jako:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Tentokrát, poskytnutého `MyCustomPollViewController` musíte rozšířit `AEPollViewController`. Nebo můžete rozšířit z výchozí řadiče: `AEDefaultPollViewController`.

> [!IMPORTANT]
> Nezapomeňte volat buď `action` (`submitAnswers:` pro vlastní dotazování řadiče zobrazení) nebo `exit` metoda před řadiče zobrazení se zavře. Jinak statistiky nebude odeslána (tj. žádné analýzy kampaň) a další je nejdůležitější další kampaně nebudete upozorněni, až po restartování procesu aplikace.
>
>

##### <a name="implementation-example"></a>Příklad implementace
V této implementaci vlastní oznámení zobrazení je načten z externí xib souboru.

Jako pro přizpůsobení pokročilé oznámení, doporučujeme prohlédnout si zdrojový kód standardní implementace.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
