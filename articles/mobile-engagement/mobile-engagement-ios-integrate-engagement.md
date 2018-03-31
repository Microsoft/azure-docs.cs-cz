---
title: Azure Mobile Engagement iOS SDK integrace | Microsoft Docs
description: Nejnovější aktualizace a postupy pro iOS SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 947ea44b-00c1-450f-9a3b-74437954dc56
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: f0d650205ca86205ad857003f892c1a60b3831bf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-on-ios"></a>Jak integrovat Engagement v systému iOS
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!div class="op_single_selector"]
> * [Univerzální pro Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
>
>

Tento postup popisuje nejjednodušší způsob, jak aktivovat Engagement analýzy a monitorování funkcí v aplikace iOS.

Sady Engagement SDK vyžaduje systému IOS 7 + a Xcode 8 +: cíl nasazení vaší aplikace musí být alespoň iOS 7.

> [!NOTE]
> Pokud jste ve skutečnosti závisí na XCode 7 pak můžete použít [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Je známého problému při spuštění v iOS 10 zařízení najdete na modul Reach této předchozí verze [integrační modul reach](mobile-engagement-ios-integrate-engagement-reach.md) další podrobnosti. Pokud se rozhodnete použít SDK v3.2.4 a z nich přeskočit, `UserNotifications.framework` importovat v dalším kroku.
>
>

Následující kroky jsou dost aktivovat sestavy protokolů, které jsou potřebné k výpočtu všechny statistické údaje týkající se uživatelů, relací, aktivity, dojde k chybě a Technicals. Sestava protokolů, které jsou potřebné k výpočtu další statistiky, jako jsou události a chyby úlohy je třeba provést ručně pomocí rozhraní API Engagement (najdete v části [jak používat rozšířené Mobile Engagement označování rozhraní API v aplikaci s iOS](mobile-engagement-ios-use-engagement-api.md) vzhledem k tomu, že se tyto statistiky závislé aplikace.

## <a name="embed-the-engagement-sdk-into-your-ios-project"></a>Vložení sady Engagement SDK do projektu iOS
* Sada SDK iOS stáhnout z [zde](http://aka.ms/qk2rnj).
* Přidejte sady Engagement SDK do projektu iOS: v Xcode, klikněte pravým tlačítkem na projekt a vyberte **"Přidat soubory do..."** a zvolte `EngagementSDK` složky.
* Engagement vyžaduje další rozhraní pro práci: v prohlížeči projektu otevřete podokno váš projekt a vyberte správný cíl. Potom otevřete **"Fáze sestavení"** kartě a v **"Odkaz binárních souborů a knihoven"** nabídky, přidejte tyto architektury:

  * `UserNotifications.framework` – nastavte odkaz jako `Optional`
  * `AdSupport.framework` – nastavte odkaz jako `Optional`
  * `SystemConfiguration.framework`
  * `CoreTelephony.framework`
  * `CFNetwork.framework`
  * `CoreLocation.framework`
  * `libxml2.dylib`

> [!NOTE]
> Rozhraní framework AdSupport lze odebrat. Zapojení musí toto rozhraní ke shromažďování IDFA. Nicméně je možné zakázat shromažďování IDFA \<ios sdk engagement idfa\> pro dosažení souladu s novou zásadu Apple týkající se mu ID této.
>
>

## <a name="initialize-the-engagement-sdk"></a>Inicializace Engagement SDK
Je třeba úprava delegáta aplikace:

* V horní části souboru implementace importujte agenta Engagement:

      [...]
      #import "EngagementAgent.h"
* Inicializace Engagement uvnitř metody '**applicationDidFinishLaunching:**"nebo"**aplikace: didFinishLaunchingWithOptions:**':

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
      {
        [...]
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
        [...]
      }

## <a name="basic-reporting"></a>Vytváření základních sestav
### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Doporučená metoda: přetížení vaší `UIViewController` třídy
Chcete-li aktivovat sestavu všechny protokoly, které vyžadují zapojení k výpočtu uživatelů, relací, aktivity, dojde k chybě a technické statistiky, můžete jednoduše provést všechny vaše `UIViewController` dílčí třídy dědí `EngagementViewController` třídy (stejného pravidla pro `UITableViewController`  -\> `EngagementTableViewController`).

**Bez Engagement:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**S Engagement:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Alternativní metoda: volání `startActivity()` ručně
Pokud nemůžete nebo nechcete přetížení vaše `UIViewController` třídy, můžete místo toho spustit vaše aktivity voláním `EngagementAgent`na metody přímo.

> [!IMPORTANT]
> IOS SDK automaticky zavolá `endActivity()` metoda při ukončení aplikace. Z toho důvodu je *vysoce* doporučuje volat `startActivity` metoda vždy, když aktivita uživatele změnit a *nikdy* volání `endActivity` metoda, protože voláním této metody vynutí aktuální relace ukončit.
>
>

## <a name="location-reporting"></a>Rozšířené ohlašování polohy
Podmínky služby Apple neumožňují aplikacím používat zjišťování za účelem statistiky pouze umístění. Proto se doporučuje povolit umístění sestavy pouze v případě, že vaše aplikace používat také umístění sledování z jiného důvodu.

Od verze iOS 8, je nutné zadat popis jak vaše aplikace používá polohy nastavením řetězec pro klíč [NSLocationWhenInUseUsageDescription] nebo [NSLocationAlwaysUsageDescription]v souboru Info.plist vaší aplikace. Pokud chcete umístění sestavy na pozadí se zapojení, přidejte klíč NSLocationAlwaysUsageDescription. Ve všech ostatních případech přidáte klíč NSLocationWhenInUseUsageDescription. Všimněte si, že je potřeba NSLocationAlwaysAndWhenInUseUsageDescription a NSLocationWhenInUseUsageDescription umístění pozadí sestavy v systému iOS 11.

### <a name="lazy-area-location-reporting"></a>Opožděné hlášení umístění oblasti
Opožděné hlášení umístění oblasti umožňuje podat zprávu zemi, oblast a polohu přidružené k zařízení. Tento typ hlášení umístění používá jenom síťová umístění (na základě ID buňky nebo Wi-Fi). Oblasti zařízení se hlásí nejvíce jednou za relace. GPS se nikdy nepoužívá, a proto tento typ umístění sestavy má jen v několika (není k vyslovení ne) dopad na baterii.

Hlášené oblasti se používají k výpočtu geografické statistické údaje o uživatelích, relacích, události a chyby. Může být také používány jako kritérium v kampaně Reach. Oblasti poslední známé ohlásil pro zařízení se dá načíst Poděkování [rozhraní API pro zařízení].

Pokud chcete povolit opožděné hlášení umístění oblasti, přidejte následující řádek po inicializaci agenta Engagement:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Hlášení polohy reálném čase.
Hlášení polohy reálném čase umožňuje podat zprávu pro zeměpisnou šířku a zeměpisnou délku přidružené k zařízení. Ve výchozím nastavení tento typ hlášení umístění používá jenom síťová umístění (na základě ID buňky nebo Wi-Fi) a generování sestav je aktivní pouze když je aplikace spuštěná v popředí (tj. během relace).

Umístění v reálném čase, jsou *není* slouží k výpočtu statistiky. Jejich jediným účelem je umožnit použití geografického vymezení reálném čase \<monitorování geografických Reach-cílové skupiny zón\> kritérium v kampaně Reach.

Pokud chcete povolit generování sestav umístění reálném čase, přidejte následující řádek po inicializaci agenta Engagement:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>Na základě GPS při vytváření sestav
Ve výchozím nastavení hlášení polohy reálném čase pouze používá síťové umístění. Chcete-li povolit použití GPS na základě umístění (což je mnohem přesnější), přidejte:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Vytváření sestav pozadí
Ve výchozím nastavení hlášení polohy reálném čase je aktivní pouze když je aplikace spuštěná v popředí (tj. během relace). Chcete-li povolit generování sestav také v pozadí, přidejte:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [!NOTE]
> Při spuštění aplikace v pozadí, pouze sítě na základě umístění nahlásí, i když je povolená GPS.
>
>

Implementace této funkce bude volat [startMonitoringSignificantLocationChanges] když vaše aplikace přejde do na pozadí. Uvědomte si ji budou spustit aplikaci do na pozadí, automaticky Pokud dorazí novou událost umístění.

## <a name="advanced-reporting"></a>Rozšířená tvorba sestav
Případně, pokud chcete k hlášení konkrétních událostí aplikace, chyb a úlohy, budete muset použít rozhraní API Engagement prostřednictvím metody `EngagementAgent` třídy. Objekt této třídy může načíst volání `[EngagementAgent shared]` statickou metodu.

Rozhraní API Engagement umožňuje používat všechny rozšířené možnosti Engagement a podrobně pokynů používat rozhraní API Engagement v systému iOS (i jako v technické dokumentaci `EngagementAgent` třídy).

## <a name="disable-idfa-collection"></a>Zakázání shromažďování IDFA
Ve výchozím nastavení, bude používat Engagement [IDFA] k jednoznačné identifikaci uživatele. Ale pokud nepoužíváte inzerování jinde v aplikaci, vám mohou být odmítnuty zkontrolujte procesem obchodu s aplikacemi. Shromažďování IDFA lze zakázat přidáním makro preprocesoru `ENGAGEMENT_DISABLE_IDFA` v souboru pch (nebo `Build Settings` aplikace). To zajistí, že neexistuje žádné odkazy na `ASIdentifierManager`, `advertisingIdentifier` nebo `isAdvertisingTrackingEnabled` v sestavení aplikace.

Integrace ve **prefix.pch** souboru:

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Můžete ověřit, že shromažďování IDFA je správně zakázán v aplikaci kontrolou protokolů testování zapojení. V tématu integrační testovací\<ios-sdk-engagement-test-idfa\> Další informace naleznete v dokumentaci.

## <a name="disable-log-reporting"></a>Zakázání zasílání zpráv protokolu
### <a name="using-a-method-call"></a>Pomocí volání metody
Pokud chcete Engagement zastavit odesílání protokolů, můžete volat:

    [[EngagementAgent shared] setEnabled:NO];

Toto volání je trvalé: používá `NSUserDefaults` uložit informace o.

Můžete povolit protokol reporting znovu voláním stejnou funkci s `YES`.

### <a name="integration-in-your-settings-bundle"></a>Integrace ve vaší sady nastavení
Namísto volání této funkce, můžete také integrovat toto nastavení přímo do existující `Settings.bundle` souboru. Řetězec `engagement_agent_enabled` jako musí být použita identifikátor předvoleb a musí být přidružený k přepínači přepnutí (`PSToggleSwitchSpecifier`).

Následující příklad `Settings.bundle` ukazuje, jak implementovat ho:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[rozhraní API pro zařízení]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
