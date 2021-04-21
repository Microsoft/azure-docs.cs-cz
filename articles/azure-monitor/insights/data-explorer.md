---
title: Azure Průzkumník dat Insights (ADX Insights Preview) | Microsoft Docs
description: Tento článek popisuje službu Azure Průzkumník dat Insights (ADX Insights).
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: a8aae2dc03ba87e9782cdf3952be1bfc4a1aae75
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767036"
---
# <a name="azure-data-explorer-insights-preview"></a>Azure Průzkumník dat Insights (Preview)

Azure Průzkumník dat Insights (Preview) poskytuje ucelený přehled o clusterech tím, že přináší jednotný přehled o výkonu, provozu, využití a selháních clusteru.
Tento článek vám pomůže porozumět tomu, jak se zaregistruje a jak používat Azure Průzkumník dat Insights (Preview).

## <a name="introduction-to-azure-data-explorer-insights-preview"></a>Seznámení se službou Azure Průzkumník dat Insights (Preview)

Před přechodem na prostředí byste měli pochopit, jak prezentuje a vizualizují informace.
-    **V perspektivě škály** , která ukazuje zobrazení snímků primárních metrik clusterů, můžete snadno sledovat výkon dotazů, přijímání a operací exportu.
-   Procházejte **analýzu** konkrétního clusteru Azure Průzkumník dat, abyste pomohli provádět podrobnou analýzu.
-    **Přizpůsobitelné** , kde můžete měnit metriky, které chcete zobrazit, upravit nebo nastavit prahové hodnoty, které odpovídají vašim limitům, a uložit vlastní sešity. Grafy v sešitě lze připnout na řídicí panely Azure.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Zobrazit z Azure Monitor (v perspektivě stupnice)

Z Azure Monitor můžete zobrazit hlavní metriky výkonu pro cluster, včetně metrik pro dotazy, ingestování a operace exportu z více clusterů ve vašem předplatném a pomáhat identifikovat problémy s výkonem.

Pokud chcete zobrazit výkon svých clusterů ve všech předplatných, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)

2. V Azure Portal v levém podokně vyberte **monitor** a v části Centrum Insights vyberte **clustery Azure Průzkumník dat (Preview)**.

![Snímek obrazovky s přehledem možností více grafů](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Karta Přehled

Na kartě **Přehled** u vybraného předplatného zobrazuje tabulka interaktivní metriky pro clustery Azure Průzkumník dat seskupené v rámci předplatného. Výsledky můžete filtrovat podle vybraných možností z následujících rozevíracích seznamů:

* Předplatná – seznam obsahuje jenom předplatná, která mají clustery Azure Průzkumník dat.

* Clustery Azure Průzkumník dat – ve výchozím nastavení se předem vybere jenom pět clusterů. Pokud v voliči oboru vyberete možnost všechny nebo víc clusterů, vrátí se až 200 clusterů.

* Časový rozsah – ve výchozím nastavení zobrazuje posledních 24 hodin informací na základě odpovídajících výběrů.

Dlaždice počítadlo v rozevíracím seznamu shrnuje celkový počet clusterů Azure Průzkumník dat ve vybraných předplatných a odráží počet vybraných. Existují podmíněná barevná kódování pro tyto sloupce: udržování naživu, CPU, využití příjmu a využití mezipaměti. Oranžová – kódované buňky mají hodnoty, které nejsou pro cluster udržitelné. 

Abyste lépe pochopili, co jednotlivé metriky představují, doporučujeme si je přečíst v dokumentaci k [Azure Průzkumník dat metrikami](/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Karta výkon dotazu

Tato karta zobrazuje dobu trvání dotazu, celkový počet souběžných dotazů a celkový počet neomezených dotazů.

![Snímek obrazovky s kartou výkon dotazů](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Karta výkon pro přijímání

Tato karta zobrazuje latenci příjmu, úspěšnost ingestování, výsledky ingestování, objem ingest a události zpracované pro služby Event/IoT Hub.

[![Snímek obrazovky s kartou pro sledování výkonu pro přijímání](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Karta pro sledování výkonu pro streamování

Tato karta poskytuje informace o průměrné míře dat, průměrné době trvání a rychlosti požadavků.

### <a name="export-performance-tab"></a>Karta výkon exportu

Tato karta poskytuje informace o exportovaných záznamech, zpožděnosti, počtu probíhajících a procento využití pro operace průběžného exportu.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Zobrazení z prostředku clusteru Azure Průzkumník dat (analýza procházení k podrobnostem)

Přístup k Azure Průzkumník dat Insights přímo z clusteru Azure Průzkumník dat:

1. V Azure Portal vyberte **clustery Azure Průzkumník dat**.

2. V seznamu vyberte cluster Azure Průzkumník dat. V části monitorování vyberte **přehledy (Preview)**.

Tato zobrazení jsou přístupná taky tak, že v zobrazení služby Azure Monitor Insights vyberete název prostředku clusteru Azure Průzkumník dat.

Azure Průzkumník dat Insights kombinuje jak protokoly, tak metriky, které poskytují globální řešení monitorování. Zahrnutí vizualizací založených na protokolech vyžaduje, aby uživatelé [povolili protokolování diagnostiky svého clusteru Azure Průzkumník dat a odesílali je do pracovního prostoru Log Analytics.](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) Diagnostické protokoly, které by se měly povolit, jsou: **Command**, **Query**, **TableDetails** a **TableUsageStatistics**.

![Snímek obrazovky s modrým tlačítkem, který zobrazuje text "povolit protokoly pro monitorování"](./media/data-explorer/enable-logs.png)


 Karta **Přehled** zobrazuje:

- Dlaždice metriky zvýrazňují dostupnost a celkový stav clusteru, aby bylo možné rychle vyhodnotit svůj stav.

- Shrnutí doporučení aktivní služby [Advisor](/azure/data-explorer/azure-advisor) a stavu [prostředků](/azure/data-explorer/monitor-with-resource-health) .

- Grafy znázorňující hlavní uživatele CPU a paměti a počet jedinečných uživatelů v průběhu času.


[![Snímek obrazovky se zobrazením z prostředku clusteru Azure Průzkumník dat](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

Na kartě **klíčové metriky** se zobrazuje jednotný pohled na některé metriky clusteru seskupené podle: obecné metriky, související s dotazem, ingestování a ingestování streamování, které se týkají přijímání.

[![Snímek obrazovky s zobrazením chyb](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

Karta **použití** umožňuje uživatelům hluboko podrobně výkon příkazů a dotazů clusteru. Na této stránce můžete:
 
 - Podívejte se, které skupiny úloh, uživatelé a aplikace odesílají nejvíc dotazů nebo využívají nejvíc procesorů a paměti (takže můžete pochopit, které úlohy odesílají nejtěžší dotazy pro zpracování clusteru).
 - Identifikujte nejdůležitější skupiny úloh, uživatele a aplikace podle neúspěšných dotazů.
 - Identifikujte nedávné změny v počtu dotazů v porovnání s historickým denním průměrem (za posledních 16 dní) podle skupiny úloh, uživatele a aplikace.
 - Identifikujte trendy a špičky v počtu dotazů, paměti a spotřeby procesoru podle skupin úloh, uživatelů, aplikací a typů příkazů.

[![Snímek obrazovky s zobrazením operací s prstencovými grafy hlavní aplikace podle počtu příkazů a dotazů, hlavních objektů zabezpečení podle příkazů a počtu dotazů a hlavních příkazů podle typů příkazů](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Snímek obrazovky s zobrazením operací se spojnicovým grafem počtu dotazů podle aplikace, celkového množství paměti podle aplikace a celkového využití CPU podle aplikace](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

Karta **tabulky** zobrazuje nejnovější a historické vlastnosti tabulek v clusteru. Můžete zjistit, které tabulky využívají nejvíce místa, sledovat historii růstu podle velikosti tabulky, aktivních dat a počtu řádků v průběhu času.

Karta **mezipaměť** umožňuje uživatelům analyzovat vlastní vzory dotazů a porovnat je s nakonfigurovanými zásadami mezipaměti (pro každou tabulku). Můžete identifikovat tabulky používané nejvíce dotazy a tabulkami, na kterých se nedotazuje vůbec, a odpovídajícím způsobem upravit zásady mezipaměti. Můžete získat konkrétní doporučení zásad mezipaměti pro konkrétní tabulky v Azure Advisor (v současné době jsou doporučení pro mezipaměť dostupná jenom z [hlavního řídicího panelu Azure Advisor](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)) na základě skutečných dotazů v posledních 30 dnech a neoptimalizované zásady mezipaměti pro minimálně 95% dotazů. Doporučení pro snížení velikosti mezipaměti v Azure Advisor jsou dostupná pro clustery, které jsou vázané na data (což znamená, že cluster má nízké využití CPU a nízké příjem dat, ale kvůli vysoké kapacitě dat se nepovedlo škálovat nebo škálovat clustery).

[![Snímek obrazovky s podrobnostmi o mezipaměti](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

Karta **hranice clusteru** zobrazuje hranice clusteru na základě vašeho využití. Na této kartě můžete zkontrolovat využití procesoru, ingestování a ukládání do mezipaměti. Tyto metriky jsou vyhodnoceny jako "nízká", "střední" nebo "vysoká". Tyto metriky a skóre jsou důležité při rozhodování o optimálním počtu SKU a instancích pro váš cluster a jsou zohledněny při Azure Advisor doporučení SKU/velikosti. Na této kartě můžete vybrat dlaždici metrik a hloubkové podrobněy, abyste porozuměli jejímu vývoji a způsobu, jakým je jejich skóre rozhodnuto. Můžete si také prohlédnout Azure Advisor doporučení skladové jednotky/velikosti pro váš cluster. Například na následujícím obrázku vidíte, že všechny metriky jsou vyhodnoceny jako "nízká", a tím cluster přijme doporučení pro snížení kapacity a úspory a úsporu nákladů.

> [!div class="mx-imgBorder"]
> [![Snímek obrazovky s hranicemi clusteru.](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-azure-dashboard&quot;></a>Připnout na řídicí panel Azure

Libovolný oddíl metriky (z perspektivy &quot;na úrovni") můžete připnout na řídicí panel Azure tak, že vyberete ikonu připínáčku v pravém horním rohu oddílu.

![Snímek obrazovky s vybranou ikonou připnutí](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>Přizpůsobení služby Azure Průzkumník dat Insights

Tato část popisuje běžné scénáře úprav sešitu pro přizpůsobení v rámci podpory vašich potřeb analýzy dat:
* Určení rozsahu sešitu pro vždycky výběr konkrétního předplatného nebo clusterů Azure Průzkumník dat
* Změna metrik v mřížce
* Změna prahových hodnot nebo vykreslení nebo kódování barev

Přizpůsobení můžete zahájit úpravou režimu úprav tak, že v horním panelu nástrojů vyberete tlačítko **přizpůsobit** .

![Snímek obrazovky s tlačítkem přizpůsobit](./media/data-explorer/customize.png)

Vlastní nastavení se ukládají do vlastního sešitu, aby se zabránilo přepsání výchozí konfigurace v našem publikovaném sešitu. Sešity se ukládají v rámci skupiny prostředků, a to buď v části Mé sestavy, která je pro vás nebo ve sdílených sestavách přístupná pro všechny s přístupem ke skupině prostředků. Po uložení vlastního sešitu musíte přejít do galerie sešitů a spustit ho.

![Snímek obrazovky Galerie sešitů](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Řešení potíží

Obecné pokyny k odstraňování potíží najdete v článku věnovaném [řešení problémů](troubleshoot-workbooks.md)na základě vyhrazeného sešitu.

Tato část vám pomůže s diagnostikou a řešením potíží s některými běžnými problémy, se kterými se můžete setkat při používání Azure Průzkumník dat Insights (Preview). K vyhledání informací týkajících se konkrétního problému použijte níže uvedený seznam.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Proč ve výběru předplatného nevidím všechna moje předplatná?

Zobrazujeme jenom předplatná, která obsahují clustery Azure Průzkumník dat vybrané z vybraného filtru předplatného, který se v hlavičce Azure Portal vybere v adresáři a v předplatném.

![Snímek obrazovky s filtrem předplatných](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Proč nevidím v sekcích použití, tabulek nebo mezipaměti žádná data pro svůj cluster Azure Průzkumník dat?

Pokud chcete zobrazit data založená na protokolech, budete muset [Povolit diagnostické protokoly](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) pro každý cluster Azure Průzkumník dat, který chcete monitorovat. To se dá udělat v nastavení diagnostiky pro každý cluster. Data budete potřebovat k odeslání do Log Analytics pracovního prostoru. Diagnostické protokoly, které by se měly povolit, jsou: Command, Query, TableDetails a TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Už mám Povolené protokoly pro svůj cluster Azure Průzkumník dat, proč se mi v příkazech a dotazech pořád nedají zobrazit data?

Diagnostické protokoly v současné době nefungují zpětně, takže se data začnou zobrazovat, jenom když dojde k provedeným akcím ve službě Azure Průzkumník dat. V závislosti na tom, jak je váš cluster Azure Průzkumník dat, může trvat nějakou dobu v rozsahu od hodin až po jeden den.


## <a name="next-steps"></a>Další kroky

Seznamte se s scénáři, které jsou navržené tak, aby podporovaly, vytváření nových a přizpůsobení existujících sestav a další informace najdete v tématu [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../visualize/workbooks-overview.md).
