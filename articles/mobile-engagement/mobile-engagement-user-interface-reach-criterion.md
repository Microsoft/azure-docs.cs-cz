---
title: Azure Mobile Engagement uživatelské rozhraní - Reach kritérium
description: Další informace o použití cílení kritéria odeslat nabízené kampaně vyberte podmnožině uživatelů pomocí Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a4ed03a0-55b1-4dd8-b0bd-c475005afb66
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 2adf473c6acea0f128eb14e2616748ff29d5d762
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Jak používat cílení kritéria k odesílat nabízené kampaně vyberte podmnožinu uživatelů
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

Cílení na cílovou skupinu podle konkrétních kritérií pomocí tlačítka "Nová kritéria" je jedním z nejúčinnějších koncepty v Azure Mobile Engagement, pomáhá odešlete relevantní nabízená oznámení zákazníků bude odpovídat místo everyone spamu. Můžete omezit na základě kritérií standardní cílovou skupinu a simulovat nabízených oznámení k určení, kolik lidí obdrží oznámení.

**Viz také:**

* [Kampaně nabízených novou dokumentaci - Reach - uživatelského rozhraní][Link 27]

## <a name="audience-criteria-can-include"></a>Může zahrnovat kritérií pro cílové skupiny:
* ** Technicals: **, můžete vybrat podle stejné technické informace, které se zobrazí v částech analýzy a monitorování. **Viz také:** [uživatelského rozhraní dokumentace - Analytics][Link 15], [dokumentace k uživatelského rozhraní – monitorování][Link 16]
* **Umístění:** aplikace, které používají "hlášení polohy reálném čase" s geografického vymezení můžete použít geografického umístění jako kritérium pro cílovou skupinu z GPS umístění. "Opožděné hlášení umístění oblasti" volání se také používají k zaměření cílovou skupinu z umístění mobilní telefon ("hlášení polohy reálném čase" a "Opožděné hlášení umístění oblasti" musí být aktivována ze sady SDK). **Viz také:** [dokumentaci k sadě SDK - iOS – integrace][Link 5], [dokumentaci k sadě SDK - Android - integrace][Link 5]
* **Váš názor reach:** , můžete vybrat cílovou skupinu na základě jejich názorů předchozí oznámení reach prostřednictvím reach zpětnou vazbu od oznámení, hlasování a datová oznámení. To vám umožní lepší cíl cílovou skupinu po dva nebo tři kampaně reach než může poprvé. Je také slouží k filtrování uživatelů, kteří již obdržela oznámení o s podobnou obsah, nastavením kampaň k odeslání není pro uživatele, kteří už dostali konkrétní předchozí kampaně. Můžete vyloučit i uživatelé kdo jsou zahrnuty konkrétní kampaň, která je stále aktivní příjem nové oznámení. **Viz také:** [předávaný obsah dokumentace - Reach - uživatelského rozhraní][Link 29]
* **Instalace sledování:** můžete sledovat informace podle nainstalovanou uživatelům vaší aplikace. **Viz také:** [dokumentace k uživatelského rozhraní – nastavení][Link 20]
* **Profil uživatele:** vám může cíl na základě informací o standardní uživatel a vy můžete cíl založené na informace o vlastní aplikaci, kterou jste vytvořili. To zahrnuje uživatele, kteří jsou aktuálně přihlášení a uživatele, kteří mají odpověděli konkrétní otázky, kterého jste požádali, je nastavení v aplikace místo právě jak budou odpovědi na předchozí kampaň. Všechny informace pěkně aplikace definovaná pro vaše aplikace se zobrazí v tomto seznamu.
* Segmenty: Můžete také cílový podle segmentů, které jste vytvořili podle konkrétního uživatele chování obsahující více kritérií. Všechny segmenty definované pro vaše aplikace zobrazí v tomto seznamu. **Viz také:** [dokumentace k uživatelského rozhraní – segmenty][Link 18]
* **Informace o aplikaci:** možné vytvářet vlastní značky informace o aplikaci z "Nastavení" sledovat chování uživatele. **Viz také:** [dokumentace k uživatelského rozhraní – nastavení][Link 20]

## <a name="example"></a>Příklad:
Pokud chcete nabízená oznámení pouze dílčí sadu vaše uživatele, kteří provedli akci nákupy v aplikaci.

1. Přejděte na stránku nastavení aplikace, vyberte v nabídce "Informace o aplikaci" a vyberte možnost "Informace o nové aplikace"
2. Zaregistrovat nové informace o logické aplikaci názvem "inAppPurchase"
3. Aby vaše aplikace, nastavte tuto informaci o aplikaci na hodnotu true, když uživatel úspěšně provádí nákupy v aplikaci (s použitím sendAppInfo ("inAppPurchase",...) funkce)
4. Pokud nechcete, aby k tomu z vaší aplikace, můžete ho provádět z váš back-end pomocí zařízení rozhraní API)
5. Potom právě potřebujete vytvořit sdělení, pomocí kritéria omezení cílovou skupinu pro uživatele s "inAppPurchase" nastavena na "hodnotu true")

> [!NOTE]
> Cílení na základě kritérií než značky informace o aplikaci vyžaduje Azure Mobile Engagement shromažďovat informace ze zařízení uživatelů předtím, než odešle nabízeného oznámení a proto může dojít k prodlevě. Konfigurace komplexní nabízených možností (např. aktualizace odznaky) můžete také zpoždění nabízených oznámení. Použití "jeden snímek" kampaň z rozhraní API nabízené je absolutní nejrychlejší metodu nabízené v Azure Mobile Engagement. Pomocí pouze značek informace o aplikaci jako kritéria nabízené kampaně Reach (buď z rozhraní Reach API nebo uživatelského rozhraní) je další nejrychlejší metodu, protože značky informace o aplikaci se ukládají na straně serveru. Metodu nabízené nejvíce flexibilní ale nejpomalejší pomocí jiných cílení kritérií pro kampaně nabízených je, protože má Azure Mobile Engagement k dotazování zařízení, aby bylo možné odesílat kampaně.

![Reach Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Kritérium možnosti platí pro:
* **Technicals**     
* Název firmware: název firmwaru
* Verze firmwaru: verze firmwaru
* Model zařízení: model zařízení
* Výrobce zařízení: výrobce zařízení
* Verze aplikace: verze aplikace
* Poskytovatel název: Název operátora, není definovaná
* Poskytovatel země: poskytovatel země, není definovaná
* Typ sítě: typ sítě
* Národní prostředí: národní prostředí
* Velikost obrazovky: velikost obrazovky
* **Umístění**      
* Poslední známá oblasti: polohu zemi, oblast,
* Geografického vymezení reálném čase: seznam z POIs (název, akce), cyklické bodů zájmu (název, šířky, zeměpisnou délku, protokolu Radius v měřidla)
* **Dosažení zpětné vazby**     
* Zpětná vazba oznámení: oznámení, zpětné vazby
* Dotazování zpětné vazby: dotazování, zpětné vazby
* Dotazování odpovědí zpětné vazby: dotazování odpovědí zpětnou vazbu, otázku, výběru
* Zpětné vazby dat nabízené: Datová oznámení, zpětné vazby
* **Sledování instalací**     
* Úložiště: Úložiště, není definovaná
* Zdroj: Zdroje, není definovaná
* **Profil uživatele**     
* Pohlaví: muže nebo žena, není definovaná
* Datum narození: operátor, datum, není definovaná
* Výslovný souhlas: true nebo false, Nedefinovaná
* **Informace o aplikaci**      
* Řetězec: Řetězec, není definovaná
* Datum: operátor, datum, není definovaná
* Celé číslo: číslo, není definována, operátor
* Logická hodnota: true nebo false, není definovaná
* **Segment**    
* Název segmenty (z rozevíracího seznamu), vyloučení (cíloví uživatelé, kteří nejsou součástí tohoto segmentu).

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
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

