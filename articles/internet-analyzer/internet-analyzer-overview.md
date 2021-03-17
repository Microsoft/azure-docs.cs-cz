---
title: Azure Internet Analyzer | Microsoft Docs
description: Přečtěte si o Azure Internet Analyzer
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 2512092c69390ce604c26ab6c5dd000afafc7c05
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91360884"
---
# <a name="what-is-internet-analyzer-preview"></a>Co je Internet Analyzer? (Preview)

Internet Analyzer je měřicí platforma na straně klienta, která testuje, jak se změní síťová infrastruktura, která má vliv na výkon vašich zákazníků. Ať už provádíte migraci z místního prostředí do Azure, nebo vyhodnocujete novou službu Azure, Internet Analyzer vám umožní využívat data vašich uživatelů a bohaté analýzy Microsoftu k tomu, abyste ještě před migrací lépe porozuměli vaší síťové architektuře v Azure a mohli ji optimalizovat.

Internet Analyzer používá k měření latence od koncových uživatelů do vaší vybrané sady síťových cílů malý JavaScriptový klient vložený do vaší webové aplikace. tyto _koncové body_volají. Nástroj Internet Analyzer umožňuje nastavit vícenásobné souběžné testy, což vám umožní vyhodnotit celou řadu scénářů, které se vaše infrastruktura a zákaznická potřeba vyvíjí. Internet Analyzer poskytuje vlastní a předem nakonfigurované koncové body, které vám poskytnou pohodlí a flexibilitu při rozhodování o důvěryhodných vznesených uživatelích. 


> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Rychlé & přizpůsobitelných testů

Internet Analyzer řeší otázky související s výkonem při migraci do cloudu, nasazování do nových nebo dalších oblastí Azure nebo testování nových aplikací a platforem pro doručování obsahu v Azure, jako jsou například [přední dveře Azure](https://azure.microsoft.com/services/frontdoor/) a [Microsoft Azure CDN](https://azure.microsoft.com/services/cdn/). 

Každý test, který vytvoříte v nástroji Internet Analyzer, se skládá ze dvou koncových bodů – koncový bod a a koncový bod B. výkon se analyzuje relativně ke koncovému bodu A. 

Můžete buď nakonfigurovat vlastní koncový bod, nebo vybrat z nejrůznějších předkonfigurovaných koncových bodů Azure. Vlastní koncové body by se měly používat k vyhodnocení místních úloh, instancí v jiných poskytovatelích cloudu nebo vašich vlastních konfiguracích Azure. Testy mohou být tvořeny dvěma vlastními koncovými body; minimálně jeden vlastní koncový bod musí být hostovaný v Azure. Předem nakonfigurované koncové body Azure představují rychlý a snadný způsob, jak vyhodnotit výkon oblíbených síťových platforem Azure, jako jsou například přední vrátka Azure, Azure Traffic Manager a Azure CDN. 

Během období Preview jsou k dispozici následující předem nakonfigurované koncové body: 

* **Oblasti Azure**
    * Brazil South
    * Indie – střed
    * USA – střed
    * Východní Asie
    * East US
    * Japonsko – západ
    * Severní Evropa
    * Jižní Afrika – sever
    * Southeast Asia 
    * Spojené arabské emiráty sever
    * Spojené království – západ  
    * West Europe
    * USA – západ 
    * Západní USA 2
* **Několik kombinací oblastí Azure** 
    * Východní USA, Brazílie – jih 
    * Východní USA Východní Asie 
    * Západní Evropa, Brazílie – jih
    * Západní Evropa, jihovýchodní Asie
    * Západní Evropa, Spojené arabské emiráty sever
    * Západní USA Východní USA 
    * Západní USA Západní Evropa
    * Západní USA, Spojené arabské emiráty sever
    * Západní Evropa, Spojené arabské emiráty sever, jihovýchodní Asie
    * Západní USA, Západní Evropa Východní Asie
    * Západní USA, Severní Evropa, jihovýchodní Asie, Spojené arabské emiráty sever, Jižní Afrika – sever 
* **Azure a přední dvířka** Azure – nasazené v jedné nebo několika kombinacích oblastí Azure, které jsou uvedené výše
* **Azure + Azure CDN od Microsoftu** nasazené na libovolnou kombinaci oblastí Azure uvedenou výše
* **Azure a azure Traffic Manager** – nasazené ve více kombinacích oblastí Azure uvedených výše

## <a name="suggested-test-scenarios"></a>Navrhované testovací scénáře 

Pomocí nástroje Internet Analyzer vám pomůže vyhodnotit pro vaše zákazníky nejlepší rozhodnutí o výkonu, abyste mohli vyhodnotit dva koncové body pro vaše konkrétní populace koncových uživatelů. 

I když může Internet Analyzer odpovědět na velké množství otázek, jsou některé z nejběžnějších: 
* Jaký je dopad migrace na Cloud? 
    * *Navrhovaný test: vlastní (vaše aktuální místní infrastruktura) vs. Azure (libovolný nakonfigurovaný koncový bod)*
* Jaká je hodnota umístění dat na hranici vs. v datovém centru? 
    *  *Navrhovaný test: Azure vs. přední vrátka Azure, Azure vs. Azure CDN od Microsoftu*
* Jaké jsou výhody pro výkon front-end služby Azure?
    *  *Navrhovaný test: Custom/Azure/CDN vs. přední dvířka Azure*
* Jaké jsou výhody Azure CDN od Microsoftu? 
    *  *Navrhovaný test: Custom/Azure/AFD vs. Azure CDN od Microsoftu*
* Jak se Azure CDN ze služby Microsoft Stack? 
    *  *Navrhovaný test: vlastní (jiný koncový bod CDN) vs. Azure CDN od Microsoftu*
* Jaký je nejlepší Cloud pro naplnění vašich koncových uživatelů v jednotlivých oblastech? 
    *  *Navrhovaný test: vlastní (jiná cloudová služba) vs. Azure (libovolný nakonfigurovaný koncový bod)*

## <a name="how-it-works"></a>Jak to funguje

Chcete-li použít nástroj Internet Analyzer, nastavte prostředek pro Internet Analyzer v portál Microsoft Azure a do aplikace nainstalujte malého klienta v jazyce JavaScript. Klient měří latenci od koncových uživatelů k vybraným koncovým bodům stažením obrázku s jedním pixelem přes protokol HTTPS. Po shromáždění měření latence klient odešle data o měření do nástroje Internet Analyzer.

Když uživatel navštíví webovou aplikaci, klient jazyka JavaScript vybere dva koncové body pro měření ve všech nakonfigurovaných testech. U každého koncového bodu klient provádí _studené_ a _teplé_ měření. Dopředné _měření způsobí_ další latenci při čisté latenci sítě mezi uživatelem a koncovým bodem, jako je třeba překlad DNS, handshake připojení TCP a vyjednávání SSL/TLS. Měření _teplého_ postupu následuje hned po dokončení _studeného_ měření a využívá trvalou správu připojení TCP v moderních prohlížečích k dosažení přesné míry celkové latence. V případě, že prohlížeč uživatele podporuje, používá se rozhraní API pro časování prostředků W3C k přesnému časování měření. V současné době se k analýze používají pouze měření latence v teplu.

![Diagram znázorňuje koncového uživatele, který se připojuje k aplikačnímu serveru s vloženým klientem a ke dvěma koncovým bodům na internetu z několika možností. Uživatel nahrává měření do nástroje Internet Analyzer.](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Metrik 

Po spuštění testu se data telemetrie zobrazí v prostředku nástroje Internet Analyzer na kartě scorecard. Tato data jsou vždycky agregovaná. Pomocí následujících filtrů můžete změnit zobrazení zobrazených dat: 

* **Test:** Vyberte test, pro který chcete zobrazit výsledky. Data testů se zobrazí, jakmile je k dokončení analýzy k dispozici dostatek dat – ve většině případů do 24 hodin. 
* **Časové období & koncové datum:** Internet Analyzer vygeneruje každý den tři přehledy výkonnostních metrik – každý Scorecard odráží jiné časové období agregace – 24 hodin před (dnem), sedm dní před (týden) a 30 dní před (měsíc). Pomocí filtru "koncové datum" vyberte časové období, které chcete zobrazit. 
* **Země:** Pomocí tohoto filtru můžete zobrazit data specifická pro koncové uživatele nacházející se v zemi. Globální filtr zobrazuje data napříč všemi geografickými oblastmi.  

Další informace o přehledech výkonnostních metrik najdete na stránce [Interpretace stránky s přehledem výkonnostních](internet-analyzer-scorecard.md) metrik. 


## <a name="next-steps"></a>Další kroky

* Naučte se, jak [vytvořit první prostředek služby Internet Analyzer](internet-analyzer-create-test-portal.md).
* Přečtěte si [Nejčastější dotazy k Internet Analyzer](internet-analyzer-faq.md). 
