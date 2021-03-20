---
title: Monitorování dotazů
titleSuffix: Azure Cognitive Search
description: Sledujte metriky dotazů pro výkon a propustnost. Shromažďovat a analyzovat vstupy řetězců dotazů v protokolech prostředků
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 169a90c12b30e0d083ce5c53ab7c6dd2495c4c23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592388"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Monitorování požadavků na dotazy v Azure Kognitivní hledání

Tento článek vysvětluje, jak změřit výkon a objem dotazů pomocí metrik a protokolování prostředků. Vysvětluje také, jak shromažďovat vstupní výrazy používané v dotazech, které jsou nutné k vyhodnocení nástroje a účinnosti corpus hledání.

Historická data, která jsou v metrikách, se uchovávají po dobu 30 dnů. Pro delší dobu uchování, nebo pro hlášení provozních dat a řetězců dotazů, Nezapomeňte povolit [nastavení diagnostiky](search-monitor-logs.md) , které určuje možnost úložiště pro uchování protokolovaných událostí a metrik.

Mezi podmínky, které maximalizují integritu měření dat patří:

+ Použijte fakturovatelnou službu (službu vytvořenou buď na úrovni Basic, nebo na úrovni Standard). Bezplatnou službu sdílí více předplatitelů, což zavádí určitou velikost nestálosti při načítání Shift.

+ Pokud je to možné, použijte jednu repliku a oddíl, aby se vytvořilo izolované prostředí. Pokud používáte více replik, jsou metriky dotazů průměrně rozloženy na více uzlech, což může snížit přesnost výsledků. Podobně několik oddílů znamená, že jsou data rozdělená, a to tak, že některé oddíly můžou mít při indexování taky různá data. Při ladění výkonu dotazů nabízí jeden uzel a oddíl stabilnější prostředí pro testování.

> [!Tip]
> S dodatečným kódem a Application Insights na straně klienta můžete také zachytit interaktivní data a získat tak hlubší přehled o tom, co je zajímavé pro uživatele aplikace. Další informace najdete v tématu věnovaném [vyhledávání analýz provozu](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Svazek dotazu (QPS)

Svazek se měří jako **vyhledávací dotazy za sekundu** (QPS), vestavěnou metriku, která se dá ohlásit jako průměr, počet, minimum nebo maximální hodnoty dotazů, které se spouštějí v rámci jednoho minutového okna. Intervaly jedné minuty (TimeGrain = "PT1M") pro metriky jsou v rámci systému opraveny.

Je běžné, že dotazy se mají spouštět v milisekundách, takže se v metrikách zobrazí jenom dotazy, které měří jenom sekundy.

| Typ agregace | Description |
|------------------|-------------|
| Průměr | Průměrný počet sekund během minuty, během kterých došlo k provedení dotazu.|
| Počet | Počet metrik, které byly vygenerovány do protokolu v rámci intervalu 1 – minut. |
| Maximum | Nejvyšší počet vyhledávacích dotazů za sekundu zaregistrovaných během minuty. |
| Minimum | Nejnižší počet vyhledávacích dotazů za sekundu zaregistrovaných během minuty.  |
| Sum | Součet všech dotazů provedených během minuty.  |

Například během jedné minuty můžete mít podobný vzor: jedna sekunda vysokého zatížení, která je maximální hodnota pro SearchQueriesPerSecond, následovaná 58 sekundami průměrného zatížení a nakonec jedna sekunda pouze jedním dotazem, což je minimální hodnota.

Jiný příklad: Pokud uzel emituje 100 metrik, kde hodnota každé metriky je 40, pak "Count" je 100, "Sum" je 4000, "Average" je 40 a "Max" je 40.

## <a name="query-performance"></a>Výkon dotazů

Výkon dotazů v rámci služby se měří jako latence hledání (jak dlouho trvá dotaz), a omezené dotazy, které byly vyřazeny z důvodu kolizí prostředků.

### <a name="search-latency"></a>Latence hledání

| Typ agregace | Latence | 
|------------------|---------|
| Průměr | Průměrná doba trvání dotazu v milisekundách | 
| Počet | Počet metrik, které byly vygenerovány do protokolu v rámci intervalu 1 – minut. |
| Maximum | Nejdelší běžící dotaz v ukázce. | 
| Minimum | Nejkratší běžící dotaz v ukázce.  | 
| Celkem | Celková doba provádění všech dotazů v ukázce prováděná v intervalu (jedna minuta).  |

Vezměte v úvahu následující příklad metrik **latence hledání** : byly navzorkované dotazy 86, jejichž průměrná doba je 23,26 milisekund. Minimum z 0 značí, že některé dotazy byly vyřazeny. Nejdelší běžící dotaz trvalo dokončení 1000 MS. Celková doba spuštění byla 2 sekundy.

![Agregace latence](./media/search-monitor-usage/metrics-latency.png "Agregace latence")

### <a name="throttled-queries"></a>Omezené dotazy

Omezené dotazy odkazují na dotazy, které jsou vyřazeny namísto procesu. Ve většině případů je omezování normální součástí používání služby.  Nemusí nutně znamenat, že došlo k nějakému problému.

K omezování dochází, když počet aktuálně zpracovaných požadavků překročí dostupné prostředky. V případě, že je replika při vynechání nebo při indexování provedena, se může zobrazit zvýšení počtu omezených požadavků. Požadavky na dotazy i indexování jsou zpracovávány stejnou sadou prostředků.

Služba určuje, zda se mají vyřadit žádosti na základě spotřeby prostředků. Procento prostředků spotřebovaných v paměti, CPU a v/v disku je v časovém intervalu průměrně. Pokud toto procento překročí prahovou hodnotu, všechny požadavky na daný index jsou omezeny, dokud nedojde ke snížení objemu požadavků. 

V závislosti na vašem klientovi je možné vyznačit omezený požadavek následujícími způsoby:

+ Služba vrátí chybu "posíláte příliš mnoho žádostí. Zkuste to prosím znovu později.“ 
+ Služba vrátí kód chyby 503, což znamená, že služba je momentálně nedostupná. 
+ Pokud používáte portál (například Průzkumník služby Search), dotaz bude odstraněn tiše a bude třeba znovu kliknout na tlačítko Hledat.

K potvrzení omezených dotazů použijte metriku **omezených vyhledávacích dotazů** . Můžete prozkoumat metriky na portálu nebo vytvořit metriku výstrahy, jak je popsáno v tomto článku. U dotazů, které byly vyřazeny v intervalu vzorkování, použijte příkaz *Total* k získání procenta z dotazů, které nebyly provedeny.

| Typ agregace | Throttling |
|------------------|-----------|
| Průměr | Procento dotazů vyřazených v intervalu. |
| Počet | Počet metrik, které byly vygenerovány do protokolu v rámci intervalu 1 – minut. |
| Maximum | Procento dotazů vyřazených v intervalu.|
| Minimum | Procento dotazů vyřazených v intervalu. |
| Celkem | Procento dotazů vyřazených v intervalu. |

U **omezených vyhledávacích dotazů procento**, minimum, maximum, průměr a součet musí mít všechny stejnou hodnotu: procento vyhledávacích dotazů, které byly omezeny, z celkového počtu vyhledávacích dotazů během jedné minuty.

Na následujícím snímku obrazovky je první číslo počet (nebo počet metrik odeslaných do protokolu). Další agregace, které se zobrazí v horní části nebo při najetí myší na metriku, zahrnují průměr, maximum a celkem. V této ukázce nebyly žádné požadavky vyřazeny.

![Omezené agregace](./media/search-monitor-usage/metrics-throttle.png "Omezené agregace")

## <a name="explore-metrics-in-the-portal"></a>Prozkoumat metriky na portálu

Chcete-li rychle zobrazit aktuální čísla, karta **monitorování** na stránce Přehled služby zobrazuje tři metriky (**latence hledání**, **hledané dotazy za sekundu (na jednotku vyhledávání)**, **procento omezených vyhledávacích dotazů**) za pevné intervaly měřené v hodinách, dnech a týdnech s možností změny typu agregace.

Pro hlubší zkoumání otevřete Průzkumníka metrik z nabídky **monitorování** , abyste mohli vrstvy, přiblížení a vizualizaci dat prozkoumat trendy a anomálií. Další informace o Průzkumníkovi metrik najdete [v tomto kurzu o vytváření grafu metrik](../azure-monitor/essentials/tutorial-metrics-explorer.md).

1. V části monitorování vyberte **metriky** a otevřete Průzkumníka metrik s oborem nastaveným na vyhledávací službu.

1. V části metrika vyberte položku v rozevíracím seznamu a Prohlédněte si seznam dostupných agregací pro preferovaný typ. Agregace definuje, jak budou shromážděné hodnoty v každém časovém intervalu odebírány.

   ![Průzkumník metrik pro metriku QPS](./media/search-monitor-usage/metrics-explorer-qps.png "Průzkumník metrik pro metriku QPS")

1. V pravém horním rohu nastavte časový interval.

1. Vyberte vizualizaci. Výchozím nastavením je spojnicový graf.

1. Další agregace vrstev zvolíte **přidáním metriky** a výběrem různých agregací.

1. Přiblížit oblast zájmu na spojnicovém grafu. Umístěte ukazatel myši na začátek oblasti, klikněte na levé tlačítko myši, přetáhněte ho na druhou stranu oblasti a uvolněte tlačítko. Graf se v tomto časovém rozsahu přiblíží.

## <a name="identify-strings-used-in-queries"></a>Identifikujte řetězce používané v dotazech.

Když povolíte protokolování prostředků, systém zachytí požadavky na dotazy v tabulce **AzureDiagnostics** . Je nutné, abyste už povolili [protokolování prostředků](search-monitor-logs.md), zadali jste pracovní prostor Log Analytics nebo jinou možnost úložiště.

1. V části monitorování vyberte **protokoly** a otevřete tak prázdné okno dotazu v Log Analytics.

1. Spusťte následující výraz pro hledání dotazu. operace hledání vrátí tabulkovou sadu výsledků, která se skládá z názvu operace, řetězce dotazu, dotazu a počtu nalezených dokumentů. Poslední dva příkazy vyloučí řetězce dotazu sestávající z prázdného nebo neurčeného hledání, přes vzorový index, který snižuje šum ve vašich výsledcích.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2020-06-30&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Volitelně můžete nastavit filtr sloupce pro *Query_s* a vyhledat konkrétní syntaxi nebo řetězec. Například můžete filtrovat přes *je rovno* `?api-version=2020-06-30&search=*&%24filter=HotelName` ).

   ![Řetězce dotazů v protokolu](./media/search-monitor-usage/log-query-strings.png "Řetězce dotazů v protokolu")

I když tato technika funguje pro šetření ad hoc, vytváření sestav vám umožní konsolidovat a prezentovat řetězce dotazů v rozložení, které je vhodnější pro analýzu.

## <a name="identify-long-running-queries"></a>Identifikace dlouho běžících dotazů

Přidáním sloupce Duration získáte čísla pro všechny dotazy, nikoli pouze ty, které jsou vyzvednuty jako metriky. Při řazení těchto dat se dozvíte, které dotazy mají dokončení dotazů nejdelší.

1. V části monitorování vyberte **protokoly** , které se mají dotazovat na informace protokolu.

1. Spusťte následující dotaz, který vrátí dotazy seřazené podle doby trvání v milisekundách. Nejdéle běžící dotazy jsou v horní části.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Seřadit dotazy podle doby trvání](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Seřadit dotazy podle doby trvání")

## <a name="create-a-metric-alert"></a>Vytvoření upozornění na metriku

Výstraha metriky vytváří prahovou hodnotu, při které budete buď dostávat oznámení, nebo aktivovat opravnou akci, kterou definujete předem. 

U vyhledávací služby je běžné vytvořit výstrahu metriky pro latenci hledání a omezené dotazy. Pokud víte, kdy jsou dotazy vyřazené, můžete vyhledat nápravná opatření, která omezují zatížení nebo zvýšit kapacitu. Pokud se například omezené dotazy během indexování zvýšily, můžete ji odložit, dokud se podklady aktivity dotazu.

Při nahrávání omezení konkrétní konfigurace oddílu repliky je také užitečné nastavit výstrahy pro prahové hodnoty svazku dotazů (QPS).

1. V části monitorování vyberte **výstrahy** a pak klikněte na **+ nové pravidlo výstrahy**. Ujistěte se, že je vaše vyhledávací služba vybraná jako prostředek.

1. V části podmínka klikněte na **Přidat**.

1. Nakonfigurujte logiku signálu. Jako typ signálu vyberte **metriky** a pak vyberte signál.

1. Po výběru signálu můžete použít graf k vizualizaci historických dat pro rozhodování o tom, jak pokračovat v nastavení podmínek.

1. Potom se posuňte dolů k logice výstrahy. Pro testování konceptu můžete zadat umělou nízkou hodnotu pro testovací účely.

   ![Logika výstrahy](./media/search-monitor-usage/alert-logic-qps.png "Logika výstrahy")

1. Dále zadejte nebo vytvořte skupinu akcí. Toto je odpověď na vyvolání při splnění prahové hodnoty. Může se jednat o nabízené oznámení nebo o automatizovanou reakci.

1. Nakonec zadejte podrobnosti výstrahy. Zadejte název a popis výstrahy, přiřaďte hodnotu závažnosti a určete, jestli se má pravidlo vytvořit v povoleném nebo zakázaném stavu.

   ![Podrobnosti výstrahy](./media/search-monitor-usage/alert-details.png "Podrobnosti výstrahy")

Pokud jste zadali e-mailové oznámení, obdržíte e-mailovou zprávu od "Microsoft Azure" s řádkem předmětu "Azure: aktivované závažnost: 3 `<your rule name>` ".

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, Projděte si základní informace o monitorování služby Search, kde se dozvíte o plném rozsahu funkcí dohledu.

> [!div class="nextstepaction"]
> [Monitorování operací a aktivit v Azure Kognitivní hledání](search-monitor-usage.md)