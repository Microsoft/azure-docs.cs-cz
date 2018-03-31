---
title: Azure Mobile Engagement iOS SDK přehled | Microsoft Docs
description: Nejnovější aktualizace a postupy pro iOS SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 6c9d2ddeacd1d05142019f22eedf4eb3bb4f06cd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>iOS SDK pro Azure Mobile Engagement
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Chcete-li získat všechny podrobnosti o tom, jak integrovat Azure Mobile Engagement v aplikaci pro iOS, začněte zde. Pokud chcete a vyzkoušejte ho nejdřív, ujistěte se, můžete udělat naše [15 minut kurzu](mobile-engagement-ios-get-started.md).

Kliknutím zobrazíte [SDK obsahu](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>Integrace procedury
1. Začněte zde: [jak integrovat Mobile Engagement v aplikaci s iOS](mobile-engagement-ios-integrate-engagement.md)
2. Pro oznámení: [jak integrovat Reach (oznámení) v aplikaci s iOS](mobile-engagement-ios-integrate-engagement-reach.md)
3. Značka plán implementace: [jak používat rozšířené Mobile Engagement označování rozhraní API v aplikaci s iOS](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>Poznámky k verzi
### <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Odznaky s pevnou vymazat v pozadí.
* Opravené upozornění na XCode 9 o volání rozhraní API není z hlavní fronty.
* Vyřešený nevracení paměti v hlasování Reach.
* Podpora pro iOS vyřadit 6.X. Od této verze cíl nasazení vaší aplikace musí být minimálně iOS 7.

Starší verze najdete v tématu [dokončení poznámky k verzi](mobile-engagement-ios-release-notes.md)

## <a name="upgrade-procedures"></a>Postupy upgradu
Pokud již jste spojili starší verze zapojení do své aplikace, je nutné zvážit následující body při upgradu sady SDK.

Možná budete muset několik postupy použijte, pokud provedena několik verzí kompletní sady SDK najdete v tématu [postupy upgradu](mobile-engagement-ios-upgrade-procedure.md).

Pro každou novou verzi sady SDK je třeba nejprve nahradit (odebrat a znovu importujte v xcode) EngagementSDK a EngagementReach složky.

### <a name="from-300-to-400"></a>Z 3.0.0 k 4.0.0
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

#### <a name="usernotifications-framework"></a>UserNotifications framework
Je nutné přidat `UserNotifications` framework ve vašem fáze buildu.

v prohlížeči projektu otevřete podokno váš projekt a vyberte správný cíl. Potom otevřete **"Fáze sestavení"** kartě a v **"Odkaz binárních souborů a knihoven"** nabídce Přidat framework `UserNotifications.framework` -nastavit odkaz jako `Optional`

#### <a name="application-push-capability"></a>Funkce nabízené aplikace
XCode 8 může resetovat vaše aplikace push schopnosti, Překontrolujte ji prosím `capability` kartě vybraný cílový.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>Přidat nový kód registrace iOS 10 oznámení
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

#### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Řešení konfliktů UNUserNotificationCenter delegáta

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
