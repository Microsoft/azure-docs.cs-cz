---
title: Azure Mobile Engagement iOS SDK postup upgradu | Microsoft Docs
description: Nejnovější aktualizace a postupy pro iOS SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 72a9e493-3f14-4e52-b6e2-0490fd04b184
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: ac03593cf0c983297e583985a3e68ea6c17e39de
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="upgrade-procedures"></a>Postupy upgradu
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Pokud již jste spojili starší verze zapojení do své aplikace, je nutné zvážit následující body při upgradu sady SDK.

Pro každou novou verzi sady SDK je třeba nejprve nahradit (odebrat a znovu importujte v xcode) EngagementSDK a EngagementReach složky.

## <a name="from-300-to-400"></a>Z 3.0.0 k 4.0.0
### <a name="xcode-8"></a>XCode 8
XCode 8 je povinný, od verze 4.0.0 sady SDK.

> [!NOTE]
> Pokud jste ve skutečnosti závisí na XCode 7 pak můžete použít [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Je známého problému na modul reach této předchozí verze při spuštění v zařízení s iOS 10: systémová oznámení nejsou reagovali. Chcete-li tomu máte k implementaci nepoužívané rozhraní API `application:didReceiveRemoteNotification:` ve vaší aplikaci delegovat následujícím způsobem:
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Toto řešení nedoporučujeme** jako toto chování můžete změnit v jakéhokoli upgradu verze iOS nadcházející (i dílčí), protože tato rozhraní API pro iOS je zastaralý. Můžete přepnout do XCode 8 co nejdříve.
> 
> 

### <a name="usernotifications-framework"></a>UserNotifications framework
Je nutné přidat `UserNotifications` framework ve vašem fáze buildu.

v prohlížeči projektu otevřete podokno váš projekt a vyberte správný cíl. Potom otevřete **"Fáze sestavení"** kartě a v **"Odkaz binárních souborů a knihoven"** nabídce Přidat framework `UserNotifications.framework` -nastavit odkaz jako `Optional`

### <a name="application-push-capability"></a>Funkce nabízené aplikace
XCode 8 může resetovat vaše aplikace push schopnosti, Překontrolujte ji prosím `capability` kartě vybraný cílový.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Přidat nový kód registrace iOS 10 oznámení
Starší fragment kódu pro registraci aplikace pro oznámení stále funguje, ale používá zastaralá rozhraní API při spuštění v iOS 10.

Import `User Notification` framework:

        #import <UserNotifications/UserNotifications.h> 

V delegáta aplikace `application:didFinishLaunchingWithOptions` nahradit metoda:

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

podle:

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

## <a name="from-200-to-300"></a>Z 2.0.0 k 3.0.0
Podpora pro iOS vyřadit 4.X. Od této verze cíl nasazení vaší aplikace musí být minimálně iOS 6.

Pokud používáte Reach ve vaší aplikaci, musíte přidat `remote-notification` hodnotu `UIBackgroundModes` pole v souboru Info.plist příjem vzdáleného oznámení vyžaduje, aby.

Metoda `application:didReceiveRemoteNotification:` musí být nahrazen `application:didReceiveRemoteNotification:fetchCompletionHandler:` v delegáta aplikace.

"AEPushDelegate.h" je zastaralá rozhraní a je nutné odebrat všechny odkazy. To zahrnuje odstranění `[[EngagementAgent shared] setPushDelegate:self]` a metody delegáta z delegáta aplikace:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

## <a name="from-1160-to-200"></a>Z 1.16.0 k 2.0.0
Následující část popisuje postup migrace integraci sady SDK z Capptain služby, které do aplikace používá technologii Azure Mobile Engagement nabízí Capptain SAS.
Pokud provádíte migraci ze starší verze, najdete na webu společnosti Capptain nejdřív přenést 1.16 pak použije následující postup.

> [!IMPORTANT]
> Capptain a Mobile Engagement nejsou stejné služby a postup níže uvedené jenom dozvíte, jak migrovat klientské aplikace. Migrace sady SDK v aplikaci není migrovat data ze serverů Capptain na servery Mobile Engagement
> 
> 

### <a name="agent"></a>Agent
Metoda `registerApp:` nahradila nová metoda `init:`. Delegáta aplikace musí být příslušným způsobem aktualizuje a použít připojovací řetězec:

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

Sledování SmartAd byla odebrána ze sady SDK, musíte se odebrat všechny instance `AETrackModule` – třída

### <a name="class-name-changes"></a>Změny názvu – třída
Jako součást rebranding existuje několik názvů souboru nebo třídy, které se musí změnit.

Všechny třídy předponu "CP" přejmenování s předponou "AE".

Příklad:

* `CPModule.h` je přejmenován na `AEModule.h`.

Všechny třídy předponu "Capptain" přejmenování s předponou "Engagement".

Příklady:

* Třída `CapptainAgent` je přejmenován na `EngagementAgent`.
* Třída `CapptainTableViewController` je přejmenován na `EngagementTableViewController`.
* Třída `CapptainUtils` je přejmenován na `EngagementUtils`.
* Třída `CapptainViewController` je přejmenován na `EngagementViewController`.

