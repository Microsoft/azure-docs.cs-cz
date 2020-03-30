---
title: Řídicí panel Marketplace Insights v analytice na Komerčním tržišti v Partnerském centru
description: Získejte přístup ke souhrnu webové analýzy marketplace, která vydavatelům umožňuje měřit zapojení zákazníků v obchodech AppSource a Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: a547ced9df98298361360ecab88036599cd86027
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275845"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Řídicí panel Marketplace Insights v analýze komerčního tržiště

Tento článek obsahuje informace na řídicím panelu Marketplace Insights v Centru partnerů. Tento řídicí panel zobrazuje souhrn webové analýzy marketplace, která vydavatelům umožňuje měřit zájem zákazníků o příslušné stránky s podrobnostmi o produktu uvedené na výloze marketplace: AppSource a Azure Marketplace.

## <a name="marketplace-insights-dashboard"></a>Řídicí panel přehledů Marketplace

Pokud chcete získat přístup k **řídicímu panelu Marketplace Insights** v Centru partnerů, otevřete **[kartu Analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** v části Komerční tržiště.

Můžete zobrazit grafické znázornění následujících položek:  

- [Souhrn přehledů Marketplace](#marketplace-insights-summary)
- [Návštěvy stránek podle zeměpisné polohy](#page-visits-by-geography)  
- [Trend návštěv stránek versus jedinečných návštěvníků](#page-visits-versus-unique-visitors-trend)
- [Výzva k akci versus unikátní návštěvníci s CTA](#call-to-action-versus-unique-visitors-with-ctas)
- [Návštěvy stránek a výzva k akci podle nabídek](#page-visits-and-calls-to-action-by-offers)
- [Trend procenta výzvy k akci](#call-to-action-percentage-trend)
- [Návštěvy stránek a výzvy k akci podle doporučujících domén](#page-visits-and-calls-to-action-by-referral-domains)
- [Tabulka podrobností přehledů marketplace](#marketplace-insights-details-table)

>[!NOTE]
> Podrobné definice terminologie analýzy naleznete v [tématu Nejčastější dotazy a terminologie pro analýzu komerčního tržiště](./faq-terminology.md).

### <a name="insights-dashboard-layout"></a>Rozložení řídicího panelu Přehledy

Metriky marketplace můžete zobrazit různými způsoby:

- Karty výlohy
- Filtry stránek
- Filtry data

**Karty u obchodu**: Metriky nabídek můžete zobrazit samostatně na kartách AppSource & Azure Marketplace. Vyberte nabídky z rozevíracího seznamu nabídek vpravo, abyste viděli vizualizaci metrik pro vybrané nabídky. Ve výchozím nastavení jsou vybrány všechny nabídky.

![Rozevírací seznam nabídek přehledů přehledů Služby Přehledy centra partnerů](./media/insights-offer-dropdown.png)

**Filtry stránek Přehledy**: Tyto filtry se použijí na úrovni nabídky (stránka podrobností o produktu). Pro kritéria, která chcete zobrazit, můžete vybrat více filtrů. Tento filtr se vztahuje na celou část Marketplace Insights včetně grafů a podrobností.

![Filtry stránek panelu Přehledy Centra partnerů](./media/insights-page-filter.png)

- Názvy nabídek jsou uvedeny pouze pro nabídky, které mají návštěvy stránek ve vybraném období.  
- Výchozí výběr je "Vše" pro každou z možností filtru.
- Použité filtry zobrazují počet výběrů pro provedené volby. Použité filtry se nezobrazí pro výchozí výběr Vše.

![Použité filtry Přehledy Centra partnerů](./media/insights-page-filter-two.png)

**Filtry data přehledů**: Tento filtr se vztahuje na celou část Marketplace Insights. Filtry mohou obsahovat předem určená období nebo vlastní rozsah dat.

![Filtry dat přehledů Centra partnerů](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Souhrn přehledů Marketplace

Souhrnpřehled marketplace zobrazuje počet **návštěv stránky**, **výzvy k akci**a Unikátní **návštěvníky** pro vybrané období.

### <a name="page-visits"></a>Návštěvy stránek

Toto číslo představuje počet různých uživatelských relací na stránce nabídky (stránka s podrobnostmi o produktu) pro vybrané období. Ukazatel červené/zelené procentní sazby představuje nárůst % návštěv stránek. Graf trendů představuje počet návštěv stránek meziměsíčně.

### <a name="unique-visitors"></a>Unikátní návštěvníci

Toto číslo představuje odlišný počet návštěvníků během vybraného rozsahu dat pro nabídky vybrané ve filtru stránek. Návštěvník, který navštívil jednu nebo více stránek s podrobnostmi o produktu, bude považován za jednoho jedinečného návštěvníka.

### <a name="call-to-action"></a>Výzva k akci

Toto číslo představuje počet kliknutí na tlačítko **Výzva k akci** dokončených na stránce nabídky (stránka s podrobnostmi o produktu). **Výzvy k akci se** počítají, když jsou vybrána tlačítka **Získat nyní**, **Bezplatná zkušební verze**, **Kontaktujte mě**a Testovat **disk.**

![Souhrn výzvy k akci Přehledy Centra partnerů](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Návštěvy stránek podle zeměpisné polohy

Heatmap níže zobrazuje počet **návštěv stránky**, **výzvy k akci**a Unikátní návštěvníci podle země **zákazníka**. Vyšší návštěvy stránek jsou reprezentovány tmavšími barvami mapy a nižší návštěvy stránek jsou reprezentovány světlejšími barvami mapy.

![Geografické rozložení Statistiky partnerského centra](./media/insights-geography.png)

Heatmap obsahuje následující funkce:

- Heatmap má doplňkovou mřížku pro zobrazení podrobností o **návštěvách stránky**, **volání k akci** a **jedinečných návštěvnících** v určitém místě; můžete přiblížit konkrétní místo, pokud je to preferováno.  
- **Rozložení zemí** je počet všech zemí, ze kterých zákazníci hlásili návštěvy stránek během vybraného období.
- Můžete vyhledat a vybrat zemi v mřížce pro přiblížení místa v mapě. Vraťte se k původnímu zobrazení výběrem **možnosti Domů** na mapě.

## <a name="page-visits-versus-unique-visitors-trend"></a>Trend návštěv stránek versus jedinečných návštěvníků

Níže uvedené sloupce představují počet měsíčních návštěv stránek, které jsou zobrazeny na ose Y (osa na levé straně grafu). Spojnice trendu představuje měsíční trend jedinečných návštěvníků, který se zobrazuje na sekundární ose Y (osa na pravé straně grafu) pro vaše nabídky publikované v obchodech: Azure Marketplace a AppSource.

![Trend návštěv stránky Přehledy centra partnerů versus trend jedinečných návštěvníků](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Výzva k akci versus unikátní návštěvníci s CTA

Skládané sloupce představují měsíční výzvy k akci (CTA), které jsou rozděleny podle typů CTA **(Get it now**, **Kontaktujte mě**a **bezplatnou zkušební verzi)** a vykresleny na ose Y (osa na levé straně stránky). Spojnice trendu představuje měsíční trend jedinečných návštěvníků s CTA, který se zobrazuje na vedlejší ose Y (osa na pravé straně grafu) pro vaše nabídky publikované na Azure Marketplace a AppSource.

![Výzva k akci v Centru partnerů versus jedineční návštěvníci s CTA](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Návštěvy stránek a výzvy k akci podle nabídek

Vnější výsečový graf představuje rozpis **návštěv stránky** na základě nabídek, které jste publikovali na trhu a které byly vybrány ve filtru. Vnitřní graf představuje **volání k akci** členění pro stejné nabídky.

![Návštěvy a výzvy k akci pomocí nabídek na stránce Přehledy centra partnerů](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Trend procenta výzvy k akci

**Trend procenta výzvy k akci** představuje procento CTA pro nabídky zveřejněné na trhu. CTA % = (CTA/návštěvy stránek) * 100.

![Trend výzvy k akci přehledů Centra partnerů](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Návštěvy stránek a výzvy k akci podle doporučujících domén

Níže uvedený graf představuje 50 nejlepších referenčních domén. Výběr konkrétní domény odkazů zobrazuje měsíční trend návštěv stránek a výzev k akci v grafu vpravo.

![Návštěvy a výzvy k akci pomocí domén a kampaní, které se nazývají odkazy, a návštěvy přehledů centra partnerů](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Tabulka podrobností přehledů marketplace

Tato tabulka obsahuje zobrazení seznamu návštěv stránek a výzvy k akci vybraných nabídek seřazené podle data.

![Tabulka podrobností centra partnerů](./media/insights-details-page.png)

- Data lze extrahovat do souboru CSV, pokud je počet záznamů menší než 1000.
- Pokud je počet záznamů vyšší než 1000, exportovaná data budou asynchronně umístěna na stránce stahování po dobu následujících 30 dnů.
- Filtry lze použít k zobrazení dat, která vás zajímají. Data lze filtrovat podle názvů nabídek a názvů kampaní.  

## <a name="next-steps"></a>Další kroky

- Přehled analytických přehledů dostupných na komerčním trhu Partnerského centra najdete v [článku Analýza pro komerční tržiště v Centru partnerů](./analytics.md).
- Grafy, trendy a hodnoty souhrnných dat, které shrnují aktivitu na marketplace pro vaši nabídku, najdete [v tématu Summary Dashboard in Commercial Marketplace analytics](./summary-dashboard.md).
- Informace o vašich objednávkách v grafickém formátu a formátu ke stažení najdete [v tématu Dashboard objednávek v analýze komerčního tržiště](./orders-dashboard.md).
- Pro virtuální počítač (VM) nabízí využití a účtované metriky účtované podle objemu dat, najdete [v tématu Využití Řídicí panel v komerčních Marketplace analýzy](./usage-dashboard.md).
- Podrobné informace o zákaznících, včetně trendů růstu, najdete [v tématu Řídicí panel zákazníků v analýze komerčního webu Marketplace](./customer-dashboard.md).
- Seznam žádostí o stažení za posledních 30 dní najdete v článku [Panel stahování v analýze komerčního tržiště](./downloads-dashboard.md).
- Konsolidované zobrazení zpětné vazby od zákazníků u nabídek na Azure Marketplace a AppSource najdete [v tématu Hodnocení a recenze na řídicím panelu Commercial Marketplace analytics](./ratings-reviews.md).
- Nejčastější dotazy týkající se analýzy na komerčním marketplace a komplexní slovník datových termínů naleznete [v tématu Nejčastější dotazy a terminologie pro analýzu komerčního tržiště](./faq-terminology.md).
