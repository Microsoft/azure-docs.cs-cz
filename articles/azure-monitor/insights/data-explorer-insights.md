---
title: Monitorování clusteru Azure Průzkumník dat pomocí Azure Monitor pro Azure Průzkumník dat (Preview) | Microsoft Docs
description: Tento článek popisuje Azure Monitor přehledy pro clustery Azure Průzkumník dat.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: e5884cbbc13fb2353898c617dffdf18b114f1b86
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901695"
---
# <a name="monitoring-your-azure-data-explorer-cluster-with-azure-monitor-for-azure-data-explorer-preview"></a>Monitorování clusteru Azure Průzkumník dat pomocí Azure Monitor pro Azure Průzkumník dat (Preview)
Azure Monitor pro Azure Průzkumník dat (Preview) poskytuje ucelený přehled o clusterech tím, že nabízí jednotný přehled o výkonu, provozu, využití a selháních clusteru.
Tento článek vám pomůže porozumět tomu, jak začlenit a používat Azure Monitor pro Azure Průzkumník dat (Preview).

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Úvod do Azure Monitor pro Azure Průzkumník dat (Preview)

Před přechodem na prostředí byste měli pochopit, jak prezentuje a vizualizují informace.
-    **V perspektivě škály** , která ukazuje zobrazení snímků primárních metrik clusterů, můžete snadno sledovat výkon dotazů, přijímání a operací exportu.
-   Procházejte **analýzu** konkrétního clusteru Azure Průzkumník dat, abyste pomohli provádět podrobnou analýzu.
-    **Přizpůsobitelné** , kde můžete měnit metriky, které chcete zobrazit, upravit nebo nastavit prahové hodnoty, které odpovídají vašim limitům, a uložit vlastní sešity. Grafy v sešitě lze připnout na řídicí panely Azure.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Zobrazit z Azure Monitor (v perspektivě stupnice)

Z Azure Monitor můžete zobrazit hlavní metriky výkonu pro cluster, včetně metrik pro dotazy, ingestování a operace exportu z více clusterů ve vašem předplatném a pomáhat identifikovat problémy s výkonem.

Pokud chcete zobrazit výkon svých clusterů ve všech předplatných, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)

2. V Azure Portal v levém podokně vyberte **monitor** a v části Centrum Insights vyberte **clustery Azure Průzkumník dat (Preview)**.

![Snímek obrazovky s přehledem možností více grafů](./media/data-explorer-insights/insights-hub.png)

### <a name="overview-tab"></a>Karta Přehled

Na kartě **Přehled** u vybraného předplatného zobrazuje tabulka interaktivní metriky pro clustery Azure Průzkumník dat seskupené v rámci předplatného. Výsledky můžete filtrovat podle vybraných možností z následujících rozevíracích seznamů:

* Předplatná – seznam obsahuje jenom předplatná, která mají clustery Azure Průzkumník dat.

* Clustery Azure Průzkumník dat – ve výchozím nastavení se předem vybere jenom pět clusterů. Pokud v voliči oboru vyberete možnost všechny nebo víc clusterů, vrátí se až 200 clusterů.

* Časový rozsah – ve výchozím nastavení zobrazuje posledních 24 hodin informací na základě odpovídajících výběrů.

Dlaždice počítadlo v rozevíracím seznamu shrnuje celkový počet clusterů Azure Průzkumník dat ve vybraných předplatných a odráží počet vybraných. Existují podmíněná barevná kódování pro tyto sloupce: udržování naživu, CPU, využití příjmu a využití mezipaměti. Oranžová – kódované buňky mají hodnoty, které nejsou pro cluster udržitelné. 

Abyste lépe pochopili, co jednotlivé metriky představují, doporučujeme si je přečíst v dokumentaci k [Azure Průzkumník dat metrikami](https://docs.microsoft.com/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Karta výkon dotazu

Tato karta zobrazuje dobu trvání dotazu, celkový počet souběžných dotazů a celkový počet neomezených dotazů.

![Snímek obrazovky s kartou výkon dotazů](./media/data-explorer-insights/query-performance.png)

### <a name="ingestion-performance-tab"></a>Karta výkon pro přijímání

Tato karta zobrazuje latenci příjmu, úspěšnost ingestování, výsledky ingestování, objem ingest a události zpracované pro služby Event/IoT Hub.

[![Snímek obrazovky s kartou pro sledování výkonu pro přijímání](./media/data-explorer-insights/ingestion-performance.png)](./media/data-explorer-insights/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Karta pro sledování výkonu pro streamování

Tato karta poskytuje informace o průměrné míře dat, průměrné době trvání a rychlosti požadavků.

### <a name="export-performance-tab"></a>Karta výkon exportu

Tato karta poskytuje informace o exportovaných záznamech, zpožděnosti, počtu probíhajících a procento využití pro operace průběžného exportu.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Zobrazení z prostředku clusteru Azure Průzkumník dat (analýza procházení k podrobnostem)

Přístup k Azure Monitor pro clustery Azure Průzkumník dat přímo z clusteru Azure Průzkumník dat:

1. V Azure Portal vyberte **clustery Azure Průzkumník dat**.

2. V seznamu vyberte cluster Azure Průzkumník dat. V části monitorování vyberte **přehledy (Preview)**.

Tato zobrazení jsou přístupná taky tak, že v zobrazení služby Azure Monitor Insights vyberete název prostředku clusteru Azure Průzkumník dat.

Azure Monitor pro Azure Průzkumník dat kombinuje jak protokoly, tak metriky, které poskytují globální řešení monitorování. Zahrnutí vizualizací založených na protokolech vyžaduje, aby uživatelé [povolili protokolování diagnostiky svého clusteru Azure Průzkumník dat a odesílali je do pracovního prostoru Log Analytics.](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) Diagnostické protokoly, které by se měly povolit, jsou: **Command**, **Query**, **TableDetails** a **TableUsageStatistics**.

![Snímek obrazovky s modrým tlačítkem, který zobrazuje text "povolit protokoly pro monitorování"](./media/data-explorer-insights/enable-logs.png)


 Karta **Přehled** zobrazuje:

- Dlaždice metriky zvýrazňují dostupnost a celkový stav clusteru, aby bylo možné rychle vyhodnotit svůj stav.

- Shrnutí doporučení aktivní služby [Advisor](https://docs.microsoft.com/azure/data-explorer/azure-advisor) a stavu [prostředků](https://docs.microsoft.com/azure/data-explorer/monitor-with-resource-health) .

- Grafy znázorňující hlavní uživatele CPU a paměti a počet jedinečných uživatelů v průběhu času.


[![Snímek obrazovky se zobrazením z prostředku clusteru Azure Průzkumník dat](./media/data-explorer-insights/overview.png)](./media/data-explorer-insights/overview.png#lightbox)

Na kartě **klíčové metriky** se zobrazuje jednotný pohled na některé metriky clusteru seskupené podle: obecné metriky, související s dotazem, ingestování a ingestování streamování, které se týkají přijímání.

[![Snímek obrazovky s zobrazením chyb](./media/data-explorer-insights/key-metrics.png)](./media/data-explorer-insights/key-metrics.png#lightbox)

Karta **použití** umožňuje uživatelům hluboko podrobně výkon příkazů a dotazů clusteru. Na této stránce můžete:
 
 - Podívejte se, kteří uživatelé a aplikace odesílají nejvíc dotazů nebo využívají nejvíc procesorů a paměti (takže můžete pochopit, kteří uživatelé odesílají nejtěžší dotazy pro zpracování clusteru).
 - Identifikujte hlavní uživatele a aplikace podle neúspěšných dotazů.
 - Identifikujte nedávné změny v počtu dotazů v porovnání s historickým denním průměrem (za posledních 16 dní) podle uživatele a aplikace.
 - Identifikujte trendy a špičky v počtu dotazů, paměti a spotřeby procesoru podle uživatele, typu aplikace a příkazu.

[![Snímek obrazovky s zobrazením operací s prstencovými grafy hlavní aplikace podle počtu příkazů a dotazů, hlavních objektů zabezpečení podle příkazů a počtu dotazů a hlavních příkazů podle typů příkazů](./media/data-explorer-insights/usage.png)](./media/data-explorer-insights/usage.png#lightbox)

[![Snímek obrazovky s zobrazením operací se spojnicovým grafem počtu dotazů podle aplikace, celkového množství paměti podle aplikace a celkového využití CPU podle aplikace](./media/data-explorer-insights/usage-2.png)](./media/data-explorer-insights/usage-2.png#lightbox)

Karta **tabulky** zobrazuje nejnovější a historické vlastnosti tabulek v clusteru. Můžete zjistit, které tabulky využívají nejvíce místa, sledovat historii růstu podle velikosti tabulky, aktivních dat a počtu řádků v průběhu času.

Karta **mezipaměť** umožňuje uživatelům analyzovat vlastní vzory dotazů a porovnat je s nakonfigurovanými zásadami mezipaměti (pro každou tabulku). Můžete identifikovat tabulky používané nejvíce dotazy a tabulkami, na kterých se nedotazuje vůbec, a odpovídajícím způsobem upravit zásady mezipaměti. Můžete získat konkrétní doporučení zásad mezipaměti pro konkrétní tabulky v Azure Advisor (v současné době jsou doporučení pro mezipaměť dostupná jenom z [hlavního řídicího panelu Azure Advisor](https://docs.microsoft.com/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)) na základě skutečných dotazů v posledních 30 dnech a neoptimalizované zásady mezipaměti pro minimálně 95% dotazů. Doporučení pro snížení velikosti mezipaměti v Azure Advisor jsou dostupná pro clustery, které jsou vázané na data (což znamená, že cluster má nízké využití CPU a nízké příjem dat, ale kvůli vysoké kapacitě dat se nepovedlo škálovat nebo škálovat clustery). 

[![Snímek obrazovky s podrobnostmi o mezipaměti](./media/data-explorer-insights/cache-tab.png)](./media/data-explorer-insights/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Připnout na řídicí panel Azure

Libovolný oddíl metriky (z perspektivy "na úrovni") můžete připnout na řídicí panel Azure tak, že vyberete ikonu připínáčku v pravém horním rohu oddílu.

![Snímek obrazovky s vybranou ikonou připnutí](./media/data-explorer-insights/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Přizpůsobení Azure Monitor pro cluster Azure Průzkumník dat

Tato část popisuje běžné scénáře úprav sešitu pro přizpůsobení v rámci podpory vašich potřeb analýzy dat:
* Určení rozsahu sešitu pro vždycky výběr konkrétního předplatného nebo clusterů Azure Průzkumník dat
* Změna metrik v mřížce
* Změna prahových hodnot nebo vykreslení nebo kódování barev

Přizpůsobení můžete zahájit úpravou režimu úprav tak, že v horním panelu nástrojů vyberete tlačítko **přizpůsobit** .

![Snímek obrazovky s tlačítkem přizpůsobit](./media/data-explorer-insights/customize.png)

Vlastní nastavení se ukládají do vlastního sešitu, aby se zabránilo přepsání výchozí konfigurace v našem publikovaném sešitu. Sešity se ukládají v rámci skupiny prostředků, a to buď v části Mé sestavy, která je pro vás nebo ve sdílených sestavách přístupná pro všechny s přístupem ke skupině prostředků. Po uložení vlastního sešitu musíte přejít do galerie sešitů a spustit ho.

![Snímek obrazovky Galerie sešitů](./media/data-explorer-insights/gallery.png)

## <a name="troubleshooting"></a>Řešení potíží

Obecné pokyny k odstraňování potíží najdete v článku věnovaném [řešení problémů](troubleshoot-workbooks.md)na základě vyhrazeného sešitu.

Tato část vám pomůže s diagnostikou a řešením potíží s některými běžnými problémy, se kterými se můžete setkat při použití Azure Monitor pro cluster Azure Průzkumník dat (Preview). K vyhledání informací týkajících se konkrétního problému použijte níže uvedený seznam.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Proč ve výběru předplatného nevidím všechna předplatná

Zobrazujeme jenom předplatná, která obsahují clustery Azure Průzkumník dat vybrané z vybraného filtru předplatného, který se v hlavičce Azure Portal vybere v adresáři a v předplatném.

![Snímek obrazovky s filtrem předplatných](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Proč nevidím v sekcích použití, tabulek nebo mezipaměti žádná data pro cluster Azure Průzkumník dat

Pokud chcete zobrazit data založená na protokolech, budete muset [Povolit diagnostické protokoly](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) pro každý cluster Azure Průzkumník dat, který chcete monitorovat. To se dá udělat v nastavení diagnostiky pro každý cluster. Data budete potřebovat k odeslání do Log Analytics pracovního prostoru. Diagnostické protokoly, které by se měly povolit, jsou: Command, Query, TableDetails a TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Už jsem povolil protokoly pro svůj cluster Azure Průzkumník dat, proč se mi v příkazech a dotazech pořád nedají zobrazit data.

Diagnostické protokoly v současné době nefungují zpětně, takže se data začnou zobrazovat, jenom když dojde k provedeným akcím ve službě Azure Průzkumník dat. V závislosti na tom, jak je váš cluster Azure Průzkumník dat, může trvat nějakou dobu v rozsahu od hodin až po jeden den.


## <a name="next-steps"></a>Další kroky

Seznamte se s scénáři, které jsou navržené tak, aby podporovaly, vytváření nových a přizpůsobení existujících sestav a další informace najdete v tématu [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../platform/workbooks-overview.md).
