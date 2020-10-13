---
title: Marketplace Insights – komerční tržiště Microsoftu, Microsoft AppSource a Azure Marketplace
description: Získejte přístup k souhrnným webovým analýzám Marketplace, které vám umožní měřit zapojení zákazníka v Microsoft AppSource a Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 1a645a333db9b24005639f4adbb2913a2b887b66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89055664"
---
# <a name="marketplace-insights-dashboard-in-partner-center"></a>Řídicí panel přehledů Marketplace v partnerském centru

Tento článek poskytuje informace o řídicím panelu přehledů Marketplace v partnerském centru. Tento řídicí panel zobrazuje souhrn Web Analytics pro Marketplace, který umožňuje vydavatelům měřit zapojení zákazníků na příslušné stránky s podrobnostmi o produktu, které jsou uvedené v online obchodech na komerčním webu Marketplace: Microsoft AppSource a Azure Marketplace.

## <a name="marketplace-insights-dashboard"></a>Řídicí panel přehledů Marketplace

Pokud chcete získat přístup k **řídicímu panelu přehledů Marketplace** v partnerském centru, otevřete **[kartu analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** v části komerční web Marketplace.

Můžete zobrazit grafické reprezentace následujících položek:  

- [Shrnutí webu Marketplace Insights](#marketplace-insights-summary)
- [Návštěvy stránky podle geografického umístění](#page-visits-by-geography)  
- [Návštěvy stránky versus trend jedinečných návštěvníků](#page-visits-versus-unique-visitors-trend)
- [Volání akce (výzva) oproti jedinečným návštěvníkům s CTAs](#call-to-action-versus-unique-visitors-with-ctas)
- [Návštěvy stránky a volání akce pomocí nabídek](#page-visits-and-calls-to-action-by-offers)
- [Volání procenta trendu akce](#call-to-action-percentage-trend)
- [Návštěvy stránky a volání akce podle domén odkazů](#page-visits-and-calls-to-action-by-referral-domains)
- [Tabulka podrobností webu Marketplace Insights](#marketplace-insights-details-table)

Maximální latence mezi uživateli, kteří navštěvují nabídky na Azure Marketplace nebo AppSource a vytváření sestav v partnerském centru, je 48 hodin.

>[!NOTE]
> Podrobné definice terminologie analýz najdete v tématu [Nejčastější dotazy a terminologie pro komerční analýzy na webu Marketplace](./faq-terminology.md).

### <a name="insights-dashboard-layout"></a>Rozložení řídicího panelu Insights

Prohlédněte si komerční metriky na webu Marketplace různými způsoby:

- Karty Online Storu
- Filtry stránky
- Filtry data

**Karty online obchodu**: můžete zobrazit metriky svých nabídek samostatně prostřednictvím karet AppSource & Azure Marketplace. Vyberte nabídky (nabídky) z rozevíracího seznamu nabídky vpravo a zobrazte vizualizaci metriky pro vybrané nabídky (y). Ve výchozím nastavení jsou vybrány všechny nabídky.

![Rozevírací seznam nabídky na řídicím panelu služby partner Center Insights](./media/insights-offer-dropdown.png)

**Filtry stránky Insights**: Tyto filtry se aplikují na úrovni nabídky (Stránka s podrobnostmi o produktu). Můžete vybrat více filtrů pro kritéria, která chcete zobrazit. Tento filtr platí pro celý oddíl Marketplace Insights, včetně grafů a podrobností.

![Filtry stránky řídicího panelu Insights v partnerském centru](./media/insights-page-filter.png)

- Názvy nabídek jsou uvedeny pouze pro nabídky, které mají návštěvy stránky ve vybraném časovém rozsahu.  
- Výchozí výběr pro každou z možností filtru je ALL.
- Použité filtry zobrazují počet výběrů pro provedené volby. Použité filtry se nezobrazí pro výchozí výběr vše.

![Aplikované filtry služby partner Center Insights](./media/insights-page-filter-two.png)

**Filtry data Insights**: Tento filtr platí pro celý oddíl Marketplace Insights. Filtry můžou zahrnovat předem určené rozsahy dat nebo vlastní rozsah dat.

![Filtry data z partnerského centra přehledy](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Shrnutí webu Marketplace Insights

Část Souhrn Marketplace Insights zobrazuje počet **návštěv stránek**, **volání akce**a **jedinečné návštěvníky** pro vybraný rozsah kalendářních dat.

>[!NOTE]
>Řídicí panel přehledů Marketplace poskytuje navštívených data, která by se neměla korelovat se zájemci vygenerovanými v koncovém bodu zájemce.

### <a name="page-visits"></a>Návštěvy stránky

Toto číslo představuje počet odlišných uživatelských relací na stránce nabídky (Stránka s podrobnostmi o produktu) pro vybraný rozsah kalendářních dat. Indikátor procenta červené/zelené představuje nárůst počtu návštěv stránky. Graf trendu představuje počet návštěv stránek v měsíci na měsíc.

### <a name="unique-visitors"></a>Jedineční návštěvníci

Toto číslo představuje jedinečný počet návštěvníků během vybraného rozsahu dat pro nabídky vybrané ve filtru stránky. Návštěvník, který navštívil jednu nebo více stránek s podrobnostmi o produktu, bude počítat jako jeden jedinečný návštěvník.

### <a name="call-to-action"></a>Zavolat na akci

Toto číslo představuje počet volání tlačítka **Akce** na stránce nabídky (Stránka s podrobnostmi o produktu). **Volání akce** se počítá, když uživatel vybere tlačítka **získat nyní**, **bezplatná zkušební verze**, **Kontaktní osoba**nebo **testovací jednotka** .

![Souhrn akcí v partnerském centru pro přehled](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Návštěvy stránky podle geografického umístění

Heatmapu dole zobrazuje počet **návštěv stránek**, **volání akce**a **jedinečné návštěvníky v závislosti na zemi zákazníka**. Větší návštěvy stránky jsou reprezentovány tmavšími barvami mapy a dolními návštěvami stránky jsou reprezentovány světlejšími barvami mapy.

![Geografická dvojstránka partnerského centra – přehled](./media/insights-geography.png)

Heatmapu obsahuje následující funkce:

- Heatmapu má doplňkovou mřížku pro zobrazení podrobností o **návštěvě stránky**, **volání akce** a **jedinečných návštěvníků** v určitém umístění. Pokud upřednostňujete, můžete zvětšit konkrétní umístění.  
- Rozmístění **zemí nebo oblastí** je počet všech zemí nebo oblastí, od kterých vaši zákazníci oznámili návštěvy stránky během vybraného rozsahu kalendářních dat.
- Můžete vyhledat a vybrat zemi v mřížce pro přiblížení do umístění v mapě. Vraťte se k původnímu zobrazení výběrem možnosti **Domů** na mapě.

## <a name="page-visits-versus-unique-visitors-trend"></a>Návštěvy stránky versus trend jedinečných návštěvníků

Níže uvedené sloupce představují počet návštěv na stránce, které se zobrazují na ose Y (osy na levé straně grafu). Trendová čára představuje měsíční trend jedinečných návštěvníků, který se zobrazí na sekundární ose Y (na pravé straně grafu) pro vaše nabídky publikované v online obchodech: Azure Marketplace a AppSource.

![Návštěvy stránky Datacenter Center Insights versus trend jedinečných návštěvníků](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Volání akce versus jedinečné návštěvníky pomocí CTAs

Skládané sloupce představují měsíční volání akce (výzva), které jsou rozděleny podle typů výzva (**získat je nyní**, **kontaktovat mě**a **bezplatnou zkušební verzi**) a vykresleny na ose Y (na levé straně stránky). Trendová čára představuje měsíční trend jedinečných návštěvníků s CTAs, který se zobrazí na sekundární ose Y (na pravé straně grafu) pro vaše nabídky publikované v Azure Marketplace a AppSource.

![Volání služby partner Center Insights k akci versus jedinečné návštěvníky pomocí CTAs](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Návštěvy stránky a volání akcí na základě nabídek

Vnější výsečový graf představuje rozpis **návštěv stránek** na základě nabídek, které jste publikovali na webu Marketplace a vybrali je ve filtru. Vnitřní graf představuje volání rozčlenění **akcí** pro stejné nabídky.

![Návštěvy a volání stránky v partnerském centru – nabídky](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Volání procenta trendu akce

Výsledkem **volání procentuální hodnoty trendu** je výzva procento pro nabídky publikované na webu Marketplace. VÝZVA% = (návštěvy CTAs/stránky) * 100.

![Trend procenta volání partnerského centra pro akce](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Návštěvy stránky a volání akce podle domén odkazů

Následující graf obsahuje hlavní referenční domény 50. Když vyberete konkrétní referenční doménu, zobrazí se měsíční trend návštěv stránky a volání do akce v grafu napravo.

![Návštěvy a volání na stránce v partnerském centru – odkazy na akce podle domén odkazů a kampaní](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Tabulka podrobností webu Marketplace Insights

Tato tabulka obsahuje zobrazení seznamu návštěv stránky a volání akcí pro vybrané nabídky seřazené podle data.

![Tabulka podrobností partnerského centra pro přehledy](./media/insights-details-page.png)

- Data je možné extrahovat do souboru CSV, pokud je počet záznamů menší než 1000.
- Pokud je počet záznamů vyšší než 1000, exportovaná data budou asynchronně umístěna na stránku ke stažení po dobu příštích 30 dnů.
- Filtrovat data podle názvů a názvů kampaní pro zobrazení dat, která vás zajímají.

## <a name="next-steps"></a>Další kroky

- Přehled analytických sestav dostupných na obchodním tržišti partnerského centra najdete v tématu [analýzy pro komerční tržiště v partnerském centru](./analytics.md).
- Grafy, trendy a hodnoty agregovaných dat, která shrnují aktivity Marketplace pro vaši nabídku, najdete v tématu [souhrnný řídicí panel v části komerční analýza na webu Marketplace](./summary-dashboard.md).
- Informace o vašich objednávkách v grafickém formátu a ve formátu ke stažení najdete v tématu věnovaném [řídicímu panelu objednávky v rámci komerčních tržišť](./orders-dashboard.md).
- U virtuálních počítačů nabízí metriky využití a měření fakturace, viz [řídicí panel využití v komerčních obchodech na webu Marketplace](./usage-dashboard.md).
- Podrobné informace o vašich zákaznících, včetně trendů růstu, najdete [v tématu řídicí panel zákazníka v analytickém obchodě na komerčním webu](./customer-dashboard.md).
- Seznam vašich žádostí o stažení za posledních 30 dní najdete [v tématu řídicí panel ke stažení v komerčních obchodech na webu Marketplace](./downloads-dashboard.md).
- Chcete-li zobrazit konsolidované zobrazení zpětné vazby od zákazníků pro nabídky Azure Marketplace a AppSource, přečtěte si téma [hodnocení a recenze řídicího panelu na komerčních analýzách na webu Marketplace](./ratings-reviews.md).
- Nejčastější dotazy týkající se komerčních analýz na webu Marketplace a ucelený slovník datových podmínek najdete v tématu [Nejčastější dotazy a terminologie pro komerční analýzy na webu Marketplace](./faq-terminology.md).
