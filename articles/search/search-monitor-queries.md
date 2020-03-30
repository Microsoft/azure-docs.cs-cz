---
title: Sledování dotazů
titleSuffix: Azure Cognitive Search
description: Sledujte metriky dotazů pro výkon a propustnost. Shromažďujte a analyzujte vstupy řetězců dotazů v diagnostických protokolech.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: a3a313ef9cd74ba901f5a6a2d82a18e3c21145dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462513"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Monitorování požadavků na dotazy v Azure Cognitive Search

Tento článek vysvětluje, jak měřit výkon dotazu a svazku pomocí metriky a protokolování diagnostiky. Vysvětluje také, jak shromažďovat vstupní termíny použité v dotazech - potřebné informace, když potřebujete posoudit užitečnost a účinnost vašeho vyhledávacího korpusu.

Historická data, která se připoučují do metrik, se uchovávají po dobu 30 dnů. Pro delší uchovávání informací nebo sestavy provozních dat a řetězce dotazů, ujistěte se, že povolit [diagnostické nastavení,](search-monitor-logs.md) které určuje možnost úložiště pro trvalé protokolované události a metriky.

Podmínky, které maximalizují integritu měření dat, zahrnují:

+ Použijte fakturovatelnou službu (službu vytvořenou na úrovni Basic nebo Standard). Bezplatná služba je sdílena více předplatiteli, což zavádí určitou míru volatility, protože zatížení se posunují.

+ Pokud je to možné, použijte jednu repliku a oddíl k vytvoření uzavřeného a izolovaného prostředí. Pokud používáte více replik, metriky dotazu jsou zprůměrovány ve více uzlech, což může snížit přesnost výsledků. Podobně více oddílů znamená, že data jsou rozdělena, s potenciálem, že některé oddíly mohou mít různá data, pokud probíhá také indexování. Při ladění výkonu dotazu jeden uzel a oddíl poskytuje stabilnější prostředí pro testování.

> [!Tip]
> S dalším kódem na straně klienta a Application Insights můžete také zachytit data prokliku pro hlubší přehled o tom, co přitahuje zájem uživatelů vaší aplikace. Další informace naleznete [v tématu Search traffic analytics](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Objem dotazu (QPS)

Svazek se měří jako **vyhledávací dotazy za sekundu** (QPS), předdefinovaná metrika, která může být hlášena jako průměr, počet, minimum nebo maximální hodnoty dotazů, které se spouštějí v rámci jednoho minutového okna. Jednominutové intervaly (TimeGrain = "PT1M") pro metriky je stanovena v rámci systému.

Je běžné, že dotazy spustit v milisekundách, takže pouze dotazy, které měří jako sekundy se zobrazí v metriky.

| Typ agregace | Popis |
|------------------|-------------|
| Průměr | Průměrný počet sekund během minuty, během kterého došlo k spuštění dotazu.|
| Počet | Počet metrik vyzařovaných do protokolu v intervalu jedné minuty. |
| Maximum | Nejvyšší počet vyhledávacích dotazů za sekundu registrovaných během minuty. |
| Minimální | Nejnižší počet vyhledávacích dotazů za sekundu registrovaných během minuty.  |
| Součet | Součet všech dotazů provedených v rámci minuty.  |

Například během jedné minuty může mít vzor, jako je tento: jedna sekunda vysoké zatížení, které je maximální pro SearchQueriesPerSecond, následuje 58 sekund průměrné zatížení a nakonec jednu sekundu pouze jeden dotaz, což je minimum.

Další příklad: Pokud uzel vyzařuje 100 metrik, kde hodnota každé metriky je 40, pak "Počet" je 100, "Součet" je 4000, "Průměr" je 40 a "Max" je 40.

## <a name="query-performance"></a>Výkon dotazů

Výkon dotazu pro celou službu, výkon dotazu se měří jako latence vyhledávání (jak dlouho trvá dokončení dotazu) a omezené dotazy, které byly vynechány v důsledku tvrzení o prostředku.

### <a name="search-latency"></a>Latence vyhledávání

| Typ agregace | Latence | 
|------------------|---------|
| Průměr | Průměrná doba trvání dotazu v milisekundách. | 
| Počet | Počet metrik vyzařovaných do protokolu v intervalu jedné minuty. |
| Maximum | Nejdelší spuštěný dotaz v ukázce. | 
| Minimální | Nejkratší spuštěný dotaz v ukázce.  | 
| Celkem | Celková doba provádění všech dotazů v ukázce, provádění v intervalu (jedna minuta).  |

Vezměme si následující příklad **metriky latence vyhledávání:** bylo vzorkováno 86 dotazů s průměrnou dobou trvání 23,26 milisekund. Minimálně 0 označuje, že některé dotazy byly vynechány. Dokončení nejdelšího spuštěného dotazu trvalo 1000 milisekund. Celková doba provádění byla 2 sekundy.

![Agregace latence](./media/search-monitor-usage/metrics-latency.png "Agregace latence")

### <a name="throttled-queries"></a>Omezené dotazy

Omezené dotazy odkazuje na dotazy, které jsou vynechány namísto procesu. Ve většině případů je omezení normální součástí spuštění služby.  To není nutně známkou toho, že je něco špatně.

Omezení dochází, když počet požadavků aktuálně zpracovaných překročit dostupné prostředky. Může se zobrazit zvýšení požadavků omezení při repliky vyjmout z otáčení nebo během indexování. Požadavky na dotazy i indexování jsou zpracovány stejnou sadou prostředků.

Služba určuje, zda mají být požadavky přetažením v y na základě spotřeby prostředků. Procento prostředků spotřebovaných v paměti, procesoru a vod vi disků se zprůměruje za určité časové období. Pokud toto procento překročí prahovou hodnotu, všechny požadavky na index jsou omezeny, dokud není snížen objem požadavků. 

V závislosti na klientovi lze požadavek na omezení indikovat následujícími způsoby:

+ Služba vrátí chybu "Odesíláte příliš mnoho požadavků. Zkuste to prosím znovu později.“ 
+ Služba vrátí kód chyby 503 označující, že služba je aktuálně nedostupná. 
+ Pokud používáte portál (například Průzkumník hledání), dotaz je bezobslužně zrušen a budete muset znovu kliknout na Hledat.

Chcete-li potvrdit omezené dotazy, použijte **metriku dotazů s omezením.** Můžete prozkoumat metriky na portálu nebo vytvořit metriku výstrahy, jak je popsáno v tomto článku. Pro dotazy, které byly vynechány v rámci intervalu vzorkování, použijte *Celkem* získat procento dotazů, které nebyly provedeny.

| Typ agregace | Throttling |
|------------------|-----------|
| Průměr | Procento dotazů kleslv rámci intervalu. |
| Počet | Počet metrik vyzařovaných do protokolu v intervalu jedné minuty. |
| Maximum | Procento dotazů kleslv rámci intervalu.|
| Minimální | Procento dotazů kleslv rámci intervalu. |
| Celkem | Procento dotazů kleslv rámci intervalu. |

Pro **omezené vyhledávací dotazy procento**, minimální, maximální, průměrné a celkové, všechny mají stejnou hodnotu: procento vyhledávacích dotazů, které byly omezeny, z celkového počtu vyhledávacích dotazů během jedné minuty.

Na následujícím snímku obrazovky je první číslo počet (nebo počet metrik odeslaných do protokolu). Další agregace, které se zobrazují nahoře nebo při najetí na ukazatel, zahrnují průměr, maximum a součet. V této ukázce byly vynechány žádné požadavky.

![Škrticí agregace](./media/search-monitor-usage/metrics-throttle.png "Škrticí agregace")

## <a name="explore-metrics-in-the-portal"></a>Prozkoumejte metriky na portálu

Pro rychlý pohled na aktuální čísla, **karta Monitorování** na stránce Přehled služby zobrazuje tři metriky (**Latence vyhledávání**, Vyhledávací dotazy za **sekundu (na vyhledávací jednotku)**, **Procento dotazů ve vyhledávání )** v pevných intervalech měřených v hodinách, dnech a týdnech s možností změny typu agregace.

Pro hlubší zkoumání otevřete průzkumník metrik z nabídky **Monitorování,** abyste mohli vrstvit, přiblížit a vizualizovat data a prozkoumat trendy nebo anomálie. Další informace o průzkumníku metrik najdete v tomto [kurzu o vytvoření grafu metrik](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer).

1. V části Monitorování vyberte **Metriky,** chcete-li otevřít průzkumník metrik s oborem nastaveným pro vyhledávací službu.

1. V části Metrika vyberte jednu z rozevíracího seznamu a zkontrolujte seznam dostupných agregací pro upřednostňovaný typ. Agregace definuje, jak budou shromážděné hodnoty vzorkovány v každém časovém intervalu.

   ![Průzkumník metrik pro metriku QPS](./media/search-monitor-usage/metrics-explorer-qps.png "Průzkumník metrik pro metriku QPS")

1. V pravém horním rohu nastavte časový interval.

1. Zvolte vizualizaci. Výchozí je spojnicový graf.

1. Vrstva další agregace výběrem **Přidat metriku** a výběrem různých agregací.

1. Přibližte oblast zájmu v spojnicovém grafu. Položte ukazatel myši na začátek oblasti, klepněte na levé tlačítko myši a podržte ho, táhněte na druhou stranu oblasti a uvolněte tlačítko. Graf tento časový rozsah zvětší.

## <a name="identify-strings-used-in-queries"></a>Identifikovat řetězce používané v dotazech

Když povolíte protokolování diagnostiky, systém zachytí požadavky na dotazy v tabulce **AzureDiagnostics.** Jako předpoklad musíte mít již povoleno [protokolování diagnostiky](search-monitor-logs.md), určení pracovního prostoru analýzy protokolů nebo jiné možnosti úložiště.

1. V části Monitorování vyberte **Protokoly,** chcete-li otevřít prázdné okno dotazu v Log Analytics.

1. Spusťte následující výraz pro vyhledávání operací Query.Search, vraťte tabulkovou sadu výsledků skládající se z názvu operace, řetězce dotazu, dotazovaného indexu a počtu nalezených dokumentů. Poslední dva příkazy vylučují řetězce dotazu skládající se z prázdnénebo nespecifikované hledání přes ukázkový index, který snižuje šum ve výsledcích.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2019-05-06&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Volitelně můžete nastavit filtr sloupec na *Query_s* pro vyhledávání přes určitou syntaxi nebo řetězec. Můžete například filtrovat *je rovno* `?api-version=2019-05-06&search=*&%24filter=HotelName`).

   ![Řetězce protokolovaných dotazů](./media/search-monitor-usage/log-query-strings.png "Řetězce protokolovaných dotazů")

Zatímco tato technika funguje pro ad hoc šetření, vytváření sestavy umožňuje konsolidovat a prezentovat řetězce dotazu v rozložení vhodnější pro analýzu.

## <a name="identify-long-running-queries"></a>Identifikace dlouhotrvajících dotazů

Přidáním sloupce doba trvání získáte čísla pro všechny dotazy, nikoli pouze pro ty, které jsou vyzvednuty jako metrika. Řazení těchto dat ukazuje, které dotazy trvat nejdéle k dokončení.

1. V části Monitorování vyberte **protokoly,** chcete-li zadat dotaz na informace protokolu.

1. Spusťte následující dotaz a vraťte dotazy seřazené podle doby trvání v milisekundách. Nejdéle spuštěné dotazy jsou nahoře.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Řazení dotazů podle doby trvání](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Řazení dotazů podle doby trvání")

## <a name="create-a-metric-alert"></a>Vytvoření upozornění na metriku

Upozornění metriky stanoví prahovou hodnotu, při které obdržíte oznámení nebo aktivujete nápravnou akci, kterou definujete předem. 

Pro vyhledávací službu je běžné vytvořit upozornění metriky pro latenci vyhledávání a omezené dotazy. Pokud víte, kdy jsou dotazy vynechány, můžete hledat opravné prostředky, které snižují zatížení nebo zvýšení kapacity. Například pokud omezené dotazy zvýšit během indexování, můžete odložit, dokud aktivita dotazu odezní.

Při odesílání omezení konkrétní konfigurace oddílu repliky nastavení výstrahy pro prahové hodnoty objemu dotazu (QPS) je také užitečné.

1. V části Monitorování vyberte **Výstrahy** a klikněte na **+ Nové pravidlo výstrah**. Ujistěte se, že je jako prostředek vybrána vyhledávací služba.

1. V části Podmínka klikněte na **Přidat**.

1. Konfigurace logiky signálu. Pro typ signálu zvolte **metriky** a pak vyberte signál.

1. Po výběru signálu můžete pomocí grafu vizualizovat historická data pro informované rozhodnutí o tom, jak postupovat při nastavení podmínek.

1. Dále přejděte dolů na logiku výstrahy. Pro proof-of-concept můžete zadat uměle nízkou hodnotu pro účely testování.

   ![Logika výstrah](./media/search-monitor-usage/alert-logic-qps.png "Logika výstrah")

1. Dále zadejte nebo vytvořte skupinu akcí. Toto je odpověď vyvolat při splnění prahové hodnoty. Může se jedná o nabízené oznámení nebo automatickou odpověď.

1. Nakonec zadejte podrobnosti výstrahy. Pojmenujte a popište výstrahu, přiřaďte hodnotu závažnosti a určete, zda se má pravidlo vytvořit v povoleném nebo zakázaném stavu.

   ![Podrobnosti výstrahy](./media/search-monitor-usage/alert-details.png "Podrobnosti upozornění")

Pokud jste zadali e-mailové oznámení, obdržíte e-mail z "Microsoft Azure" s `<your rule name>`předmětem "Azure: Aktivovaná závažnost: 3 ".

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Další kroky

Pokud jste tak ještě neučinili, přečtěte si základy monitorování vyhledávacích služeb, abyste se dozvěděli o celé řadě funkcí dohledu.

> [!div class="nextstepaction"]
> [Monitorování operací a aktivit v Azure Cognitive Search](search-monitor-usage.md)