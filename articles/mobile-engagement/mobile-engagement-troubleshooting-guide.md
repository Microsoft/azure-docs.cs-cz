---
title: Příručky pro řešení potíží s Azure Mobile Engagement
description: Průvodce řešením potíží pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 31134a29-a513-4e5e-b626-f6cf6fe04769
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 1242b2b796821856561a82d4a585b26693aadadd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Mobile Engagement – průvodce odstraňováním potíží
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

## <a name="introduction"></a>Úvod
Následující Průvodci odstraňováním potíží vám pomůže pochopit, že základní příčiny některé běžně zaznamenané problémy a bude umožňují vyřešit sami. 

## <a name="general"></a>Obecné
Obecně platí vždy zajistěte následující:

1. Ujistěte se, že jste prošli všechny kroky potřebné k integraci, jak je popsáno v našem [kurzy Začínáme](mobile-engagement-windows-store-dotnet-get-started.md)
2. Používáte nejnovější verzi sady SDK pro platformu. 
3. Na skutečné zařízení a emulátor test, protože jsou specifické pro emulátor jenom některé problémy. 
4. Nejsou stiskne žádné omezení nebo omezení z Mobile Engagement, které jsou popsané [sem](../azure-subscription-service-limits.md)
5. Pokud si nejste možné se připojit k back-end služby Mobile Engagement nebo vidět dat. tím nepřetržitě načteny, pak zajistěte, aby žádné probíhající služby incidenty kontrolou [sem](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problémy, monitorování.
### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Nejsou zobrazeny Moje zařízení zobrazovat na kartě monitorování
Monitorování kartě se zobrazují zařízení připojená k platformě Mobile Engagement v reálném čase. Pokud ladíte na emulátoru a zařízení, měli byste vidět zde alespoň jednu relaci. Pokud aplikace byly distribuovány, uvidíte měřidla aktivních relací podle zařízení, které jsou spojeny s platformou v reálném čase. 

Pokud nevidíte zařízení na kartě monitorování je pravděpodobně problém integraci sady SDK. Některé běžné kroky pro řešení potíží se vážou:

1. Zkontrolujte, zda používáte správný připojovací řetězec v mobilní aplikaci a je z části klíče SDK a není v části klíče rozhraní API. Připojovací řetězec připojí k instanci aplikace Mobile Engagement, ve kterém se zobrazí na kartě monitorování zařízení mobilní aplikace. 
2. Pro platformu Windows - Pokud stránka přepíše `OnNavigatedTo` metoda, nezapomeňte volat `base.OnNavigatedTo(e)`.
3. Pokud jsou integraci Mobile Engagementu do stávající mobilní aplikace, pak můžete také zajistit, že nejsou chybí všechny kroky podle kroků pokročilou integraci [sem](mobile-engagement-windows-store-integrate-engagement.md)
4. Zkontrolujte přepsáním stránku s EngagementActivity v závislosti na platformě pracujete, jak je popsáno v odesílání alespoň jednu obrazovku nebo aktivity [kurzy Začínáme s](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>Na kartě monitorování zobrazující relaci se zobrazují i když I mít odpojen nebo uzavřeny mé aplikace nebo emulátor.
Pokud jsou pouze jeden připojený v tomto okamžiku pro platformu a otevřete aplikaci pomocí emulátoru pak je to pravděpodobně z důvodu adaptivní emulátor. Obecně platí je potřeba zajistit, že jste vraťte na domovskou obrazovku na emulátoru pro relaci aplikace se úspěšně odpojit. Kromě toho na platformě Windows při ladění pomocí sady Visual Studio, musíte zajistit, že v sadě Visual Studio, můžete přejít **události životního cyklu** řádku nabídek a klikněte na **pozastavit** skutečně zavřete relace. V tématu [Windows kurzu](mobile-engagement-windows-store-dotnet-get-started.md) podrobnosti. 

## <a name="analytics-issues"></a>Problémy, analýzy.
### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>I nejsou zobrazeny žádné data / aktualizovat data na kartě Analytics
Analytická data jsou přepočítána v pravidelných intervalech a může to trvat až 24 hodin pro tuto aktualizaci. Tato data nejsou v reálném čase a zobrazí se, že se že aktualizují v rámci této 24 hodin časové období.
Zkontrolujte prosím, ale odeslání alespoň jednu obrazovku nebo aktivity na back-end platformy o buď přepsání alespoň jednu stránku s `EngagementActivity` nebo volání `SendActivity` explcitly. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Nesprávně zaznamenané datum a čas pro zařízení se zobrazují na kartě Analytics
Časové období pro analýzu vychází datum z nastavení zařízení uživatelů. Proto zajistěte, aby zařízení správně nastavit datum. 

## <a name="segment-issues"></a>Problémy 'segmentu.
### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>Po vytvoření segment a je zobrazovat jako šedá nebo nezobrazují se všechna data
Vytvoření segment není v tuto chvíli v reálném čase. Vypočítá ve stejnou dobu jako agregovaná data analýzy a může to trvat až 24 hodin. Zkontrolujte znovu později, ale mezitím také se ujistěte, že vaše mobilní aplikace skutečně odesílání dat, na jehož základě se tvořící segmentů. Například Pokud událost vyslovení "foo" se neodesílají všechna mobilní zařízení, pak nebude žádná data segmentu pro segment vytvořen s položkou EventName = foo jako kritéria. Také byste měli zkontrolovat, že vaše integraci sady SDK k zajištění mobilní aplikace odesílá data správně. 

## <a name="reach-or-push-notifications-issues"></a>Problémy 'dosáhnout, nebo nabízená oznámení
### <a name="my-push-messages-are-not-being-delivered"></a>Moje nabízená oznámení nejsou doručovány.
1. Pokus o odeslání oznámení zajistěte, aby všechny součásti - mobilní aplikace, sady SDK a službu správně připojený a moct doručovat nabízená oznámení nejdřív testovací zařízení. 
2. Vždy odesílat nejprve prostřednictvím kampaň, která není naplánován nejjednodušší "se na aplikace oznámení" a ani má všechny kritéria cílové skupiny, který je zadán. Toto je znovu k prokázání, že připojení oznámení pracuje správně. 
3. Pokud došlo k potížím doručování oznámení v aplikaci taky je dobré první krok a zkuste to nejdřív odesílání upozornění se na aplikace. 
4. Zkontrolujte, že 'nativního nabízení' správně nakonfigurovaný pro mobilní aplikace. V závislosti na platformě je buď bude zahrnovat klíče (Android, Windows) nebo certifikáty (iOS). V tématu [uživatelské rozhraní – nastavení](mobile-engagement-user-interface-settings.md)
5. Mimo aplikaci, kterou oznámení může také být blokovány uživatele na základě mobilní operační systém, ujistěte se, že tomu tak není. 
6. Ujistěte se, že nejsou nastavení *ignorovat cílovou skupinu, nabízení se odešle uživatelům prostřednictvím rozhraní API* možnost **kampaň** části Reach kampaně, protože tím bude zajištěno, že může být pouze nabízená oznámení odeslána prostřednictvím rozhraní API. 
7. Ujistěte se, že testujete kampaň nabízených s obou zařízení připojit je prostřednictvím operátor sítě Wi-Fi a phone eliminovat síťové připojení jako zdroj možných problémů.
8. Zajistěte, aby systémové datum a čas na vašem zařízení nebo emulátoru správné vzhledem k tomu, že všechny synchronizované zařízení bude také narušovat nabízená oznámení služby schopnost poskytovat oznámení. 

Další platformy konkrétní řešení potíží s podle následujících pokynů:

1. **iOS** 
   
   * Zajistěte, aby certifikáty platné a neprošlé pro nabízená oznámení iOS. 
   * Ujistěte se, že správně nakonfigurujete *produkční* certifikátu v aplikaci Mobile Engagement. 
   * Ujistěte se, že testujete na *skutečné, fyzické zařízení.* Simulátoru iOS nemůže zpracovat nabízená oznámení.
   * Zkontrolujte, že identifikátor balíku správně nakonfigurovaný v mobilní aplikaci. Postupujte podle pokynů [sem](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
   * Při testování, pomocí ve svém profilu pro zřizování mobilních "Ad Hoc" distribuce. Nebudete moct dostávat oznámení, pokud vaše aplikace je zkompilovat pomocí "Debug"
2. **Android**
   
   * Ujistěte se, zda jste zadali správné číslo projektu v souboru AndroidManifest.xml mobilní aplikace, který následuje znak \n. 
     
           <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
   * Ujistěte se, nejsou chybí nebo nemá nakonfigurovaný všechna oprávnění v souboru Android Manifest 
   * Zajistěte, aby byl číslo projektu, který chcete přidat do vaší klientské aplikace stejného účtu, které jste získali klíč serveru GCM. Jakákoli Neshoda mezi těmito dvěma zabrání ven vaší nabízených oznámení. 
   * Pokud se vám systému oznámení, ale ne v aplikaci zkontrolujte [určení ikony pro oznámení oddíl](mobile-engagement-android-get-started.md) jako pravděpodobně nejsou určení správné ikony v souboru Android Manifest. 
   * Pokud odesílání oznámení BigPicture, ujistěte se, že pokud máte servery externí bitové kopie, pak potřebují být schopni podporu protokolu HTTP "GET" a "HEAD".
3. **Windows**
   
   * Ujistěte se, že byla aplikace přidružena platný aplikace pro Windows Store. V sadě Visual Studio – bude muset klikněte pravým tlačítkem na projekt a vyberte možnost "Přidružení aplikace pomocí úložiště" a vyberte aplikaci, kterou jste vytvořili ve službě Windows Store. Tato aplikace pro Windows Store musí být stejný jako ten, ze které jste získali přihlašovací údaje nativního nabízení nakonfigurovat na portálu Mobile Engagement.
   * Pokud se zobrazují na více systémů aplikaci nabízená oznámení, ale oznámení není v aplikaci s `EngagementOverlay` integrace pak zajistěte kořenového prvku mřížky na stránce. Využívá EngagementOverlay prvním elementem "Mřížky" najde v souboru xaml přidat dva webové zobrazení na stránku. Pokud chcete zjistit, kde bude nastavena webové zobrazení, můžete definovat mřížka s názvem "EngagementGrid", jako to ale budete muset zajistěte, aby byl dostatek výšky a šířky pro dva další webové zobrazení, která se zobrazí oznámení a v tomto oznámení jako oznámení v aplikaci:
     
           <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>Po vytvoření nabízená oznámení nebo oznámení/kampaně a i po jeho mi odeslat oznámení, se zobrazuje jako "Aktivní". Co to znamená?
**Kampaň** jste vytvořili v Mobile Engagementu je volána, protože je dlouhotrvající význam oznámení nabízené jako nová zařízení připojit k platformě mobile engagementu, bude se automaticky odešlou oznámení můžete Nakonfigurujte zde také splňují kritéria, které se nastavují v kampaně. Toto není jednomu nastavení snímek jednoho oznámení. Budete muset ručně klikněte na **Dokončit** tlačítko Ukončit kampaň tak, aby ji další neodešle oznámení. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>Po vytvoření kampaně nabízených a zobrazuje oznámení úspěšně ale při každém otevření aplikaci se zobrazí stejné oznámení i v případě, že došlo k reakcemi ho před?
Toto je pravděpodobně dojde během testování a pokud používáte emulátorů nebo jen některé testovací framework jako TestFlight. Co se děje, zde je v každé aplikace spustí instanci, je získání nového ID zařízení a odesílání do našich back-end, který je příčinou platformy Mobile Engagement považováno za nové zařízení a odesílání oznámení. 

## <a name="getting-support"></a>Získání podpory
Pokud se nemůžete vyřešit problém sami pak můžete můžete:

1. Vyhledejte problém v existující vláken ve fóru StackOverflow a [fórum MSDN](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) a pokud není pak položte otázku existuje. 
2. Pokud zjistíte funkce chybí pak přidejte nebo hlasování pro požadavek na našem [fórum UserVoice](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Pokud máte otevřené podporu společnosti Microsoft na základě incidentu podpory tím, že poskytuje následující podrobnosti: 
   * ID předplatného Azure
   * Platforma (například iOS, Android atd.)
   * ID aplikace
   * ID kampaně (pro nabízená oznámení problémů)
   * ID zařízení
   * Mobile Engagement SDK verze (například v2.1.0 sady SDK pro Android)
   * Podrobnosti o chybě s přesnou chybovou zprávu a scénář

