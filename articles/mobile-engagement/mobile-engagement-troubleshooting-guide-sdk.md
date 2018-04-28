---
title: Řešení potíží s Průvodce - SDK Azure Mobile Engagement.
description: Řešení potíží s problémů s integrací sady SDK v Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: de265cf1-2f88-43ef-8616-156ada5be7b6
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8ec06687482c43e657e41c82fe7e2b7e758a850d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Průvodci odstraňováním potíží problémů s integrací sady SDK
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

Následují možných problémů se můžete setkat s jak Azure Mobile Engagement integruje do vaší aplikace.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>SDK problémů zjištěných selhání v jiné oblasti vaší aplikace.
### <a name="issue"></a>Problém
* Chyba kolekce dat uživatelského rozhraní (v analýzy, monitorování, segmentace nebo řídicí panely).
* Push selhání (nabízených oznámení nefungují v aplikaci a mimo aplikaci, nebo obojí).
* Pokročilé funkce selhání (sledování, informace o zeměpisné poloze nebo platformy, které nepodporují konkrétní nabízených oznámení).
* Selhání rozhraní API (rozhraní API selhání často bezobslužném režimu bez chybové zprávy).
* Selhání služby (žádná z Azure Mobile Engagement funguje pro vaši aplikaci).

### <a name="causes"></a>Způsobí, že
* Většiny problémů, které musí být rozpoznány s Azure Mobile Engagement SDK budou zjištěny chybou v aplikaci (například selhání shromažďování dat uživatelského rozhraní, selhání nabízená, chybu upřesňující funkce, selhání rozhraní API, havárie aplikací nebo výpadkem zřejmá služby) .  
* Pokud konkrétní funkce Azure Mobile Engagement ve vaší aplikaci před nikdy fungovala, musíte se k dokončení integrace. 
* Pokud konkrétní funkce Azure Mobile Engagement byla práce a byla zastavena, musíte aktualizovat na poslední verzi s Azure Mobile Engagement SDK. Mějte na paměti, že je jinou verzi sady Azure Mobile Engagement SDK pro každou platformu podporovanou nástrojem Azure Mobile Engagement (Android, iOS, Windows a Windows Phone).

#### <a name="sdk-integration"></a>Integrace sady SDK
* Azure Mobile Engagement integrované není správně v sadě SDK (Analytics).
* Dosažení integrované není správně v sadě SDK (v aplikaci a mimo nabízených oznámení aplikace).
* Certificate vs PRODUKČNÍMU vypršela platnost nebo není správný. DEV (jenom iOS).
* GCM nebo ADM integrované není správně v sadě SDK (Android jenom - Service konkrétní nabízených oznámení).
* Sledování není správně integrované v sadě SDK (sledování instalace úložiště).
* Opožděné umístění nebo umístění GPS integrované není správně v sadě SDK (cílení na podle geografického umístění).

**Viz také:**

* [Dokumentaci k sadě SDK – integrace příručky][Link 5] 
* [Průvodce odstraňováním potíží s - Push][Link 23]

#### <a name="sdk-upgrade"></a>Upgrade sady SDK
* Třeba upgradovat SDK a řešit problémy s starší verze sady SDK (často souvisejících s novější verzí operačního systému zařízení).
* Odinstalujte všechny předchozí verze aplikace ze zařízení a znovu nainstalujte nejnovější verzi aplikace znovu zaregistrovat ID zařízení z rozhraní Azure Mobile Engagement k potvrzení, že vaše zařízení používá nejnovější verze aplikace.

**Viz také:**

* [Dokumentaci k sadě SDK – poznámky k verzi](http://go.microsoft.com/fwlink/?LinkId= 525554) 
* [Dokumentaci k sadě SDK - upgradu příručky](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>SDK jiných
* Azure Mobile Engagement nepracuje (jen Android) může způsobit chyby "AndroidManifest.xml" manifest aplikace.
* Běžné problémy s integraci sady SDK a využití rozhraní API je zaměnit klíč SDK a klíč rozhraní API.

**Viz také:**

* [Principy – Glosář][Link 6]

## <a name="advanced-coding-issues"></a>Pokročilé kódování problémy
### <a name="issue"></a>Problém
* Ne přímo související s Azure Mobile Engagement konkrétní kód platformy může způsobit problémy na iOS, Android a Windows Phone.

### <a name="causes"></a>Způsobí, že
* Několik upřesňujících kódování problémy s Azure Mobile Engagement jsou způsobeny konkrétní kód nesprávně napsaný platformy nesouvisí přímo s Azure Mobile Engagement. Musíte se k dokumentaci specifické pro platformy, které vyvíjíte pro kromě dokumentace Azure Mobile Engagement (Android, iOS, Web, systém Windows a Windows Phone).
* Konfigurace není správně "kategorie", zabraňuje propojení z oznámení do jiného umístění uvnitř nebo mimo aplikaci (jen Android). 
* Není nastavení "UIKit.framework" na "volitelné" ve vašem kódu iOS zobrazí "Symbol nebyla nalezena chyba" nebo dojde k chybě na zařízeních s iOS starší (jenom iOS).
* Platnost certifikátů nebo není správně pomocí Vývojového nebo produkčnímu verze CERT příčiny problémů nabízené (jenom iOS).
* Jsou systému platformu, která Azure Mobile Engagement nemůže řídit (jako jsou jak funguje produktu system center pro mimo aplikaci nabízených oznámení v Android a iOS) určitá omezení.
* Azure Mobile Engagement publikuje úplný seznam interní balíčky pro referenci používá pro iOS a Android pomocí Azure Mobile Engagement. Mějte na paměti, že některé funkce Azure Mobile Engagement jsou specifické pro platformu (Android, iOS, Web, systém Windows a Windows Phone).

### <a name="see-also"></a>Další informace najdete v tématech
* [Průvodce odstraňováním potíží s - Push][Link 23] 
* [Dokumentaci k sadě SDK – poznámky k verzi][Link 5]
* [Dokumentaci k sadě SDK - upgradu příručky][Link 5]

## <a name="application-crashes"></a>Selhání aplikace
### <a name="issue"></a>Problém
* Aplikace, dojde k chybě v zařízení koncových uživatelů.

### <a name="causes"></a>Způsobí, že
* Informace o havárii lze zobrazit v *uživatelského rozhraní Analytics* nebo *Analytics rozhraní API*
* Můžete zjistit ID zařízení testovací zařízení a trvat o stejnou akci, která způsobila, že aplikace havárií pro koncové uživatele k identifikaci příčinu vaší havárií.
* Známé problémy s Azure Mobile Engagement SDK, které způsobí aplikace došlo k chybě, se někdy vyřeší upgrade na nejnovější verzi sady SDK. Ujistěte se, že zkontrolujte poznámky o vaši platformu při zkoumání dojde k chybě.

### <a name="see-also"></a>Další informace najdete v tématech
* [Dokumentaci k sadě SDK – poznámky k verzi][Link 5]
* [Dokumentaci k sadě SDK - upgradu příručky][Link 5]

## <a name="app-store-upload-failures"></a>Chyb odesílání App store
### <a name="issue"></a>Problém
* Chyby související s odesílání nejnovější verzi aplikace Apple, Google nebo aplikace pro Windows store.

### <a name="causes"></a>Způsobí, že
* Aplikace ukládá někdy blokovat aplikacím s určité funkce povolit (např. Apple Storu brání použití IDFV v aplikacích v úložišti a úložišti GooglePlay brání sdílení informací o aplikaci mezi aplikacemi). 
* Zajistěte, aby zkontrolujte poznámky o platformy a aktuální verzi sady SDK, pokud máte potíže při nahrávání aplikace do úložiště.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

