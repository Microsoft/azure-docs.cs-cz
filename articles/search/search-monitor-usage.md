---
title: Monitorování využití a statistiky pro službu search – Azure Search
description: Sledování využití a index velikost prostředku pro službu Azure Search, hostované cloudové vyhledávací službě v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 584d1d8ce3285f9f5fb986c9779d3c403ce13d1b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314155"
---
# <a name="monitor-an-azure-search-service-in-azure-portal"></a>Monitorování služby Azure Search na webu Azure portal

Azure Search nabízí různé prostředky pro sledování využití a výkonu služby search. Poskytuje přístup k metriky, protokoly, indexové statistiky a rozšířené možnosti monitorování v Power BI. Tento článek popisuje, jak povolit různé strategie monitorování a jak interpretovat Výsledná data.

## <a name="azure-search-metrics"></a>Metriky Azure Search
Metriky vám poskytují téměř v reálném čase přehled o vaší vyhledávací služby a jsou k dispozici pro každou službu s nic dalšího nastavovat. Umožňují sledovat výkon služby po dobu až 30 dnů.

Služba Azure Search shromažďuje data pro tři různé požadované metriky:

* Latence vyhledávání: Čas službu search potřebné ke zpracování vyhledávacích dotazů, agregují za minutu.
* Vyhledávací dotazy za sekundu (QPS): Počet vyhledávací dotazy přijaté za sekundu, agregují za minutu.
* Procento omezených vyhledávacích dotazů: Procento vyhledávacích dotazů, které byly omezené agregovat za minutu.

![Snímek obrazovky QPS aktivity][1]

### <a name="set-up-alerts"></a>Nastavení výstrah
Na stránce podrobností metriky můžete nakonfigurovat výstrahy pro aktivaci e-mailové oznámení nebo automatizované akce, pokud metrika překročí mezní hodnotu, která jste definovali.

Další informace o metrikách zkontrolujte úplnou dokumentaci k Azure Monitor.  

## <a name="how-to-track-resource-usage"></a>Jak sledovat využití prostředků
Sledování růst indexy a velikosti dokumentu vám může pomoct proaktivně upravit kapacitu před dosažení horní mez, kterou jste vytvořili pro vaši službu. Můžete to provést na portálu nebo programově pomocí rozhraní REST API.

### <a name="using-the-portal"></a>Použití portálu

Pokud chcete monitorovat využití prostředků, zobrazit počty a statistiky pro vaši službu v [portál](https://portal.azure.com).

1. Přihlaste se k [portálu](https://portal.azure.com).
2. Otevřete řídící panel služby Azure Search. Dlaždice pro službu najdete na domovské stránce nebo můžete přejít na službu z procházení na panelu vlevo.

Využití část obsahuje měření, dozvíte, jaká část dostupných prostředků se právě používají. Informace o omezení za služeb pro úložiště, indexy a dokumenty, naleznete v tématu [omezení služby](search-limits-quotas-capacity.md).

  ![Dlaždice využití][2]

> [!NOTE]
> Na snímku obrazovky výše je bezplatná služba, která může mít nejvýše jednu repliku a každý oddíl a mohou pouze hostitele 3 indexy, 10 000 dokumentů nebo 50 MB dat, podle toho, co nastane dřív. Mnohem větší limity služby jsou služby vytvořené na úrovni Basic nebo Standard. Další informace o volbě vrstvu, naleznete v tématu [zvolte úrovně nebo SKU](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>S využitím REST API
REST API služby Azure Search a sady .NET SDK poskytují programový přístup k metrikám služby.  Pokud používáte [indexery](https://msdn.microsoft.com/library/azure/dn946891.aspx) načtení indexu z Azure SQL Database nebo Azure Cosmos DB, je možné získat čísla, budete potřebovat další rozhraní API.

* [Získání statistik indexu](/rest/api/searchservice/get-index-statistics)
* [Počet dokumentů](/rest/api/searchservice/count-documents)
* [Získat stav indexeru](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Jak exportovat protokoly a metriky

Můžete exportovat protokoly operací pro vaši službu a nezpracovaných dat pro metriky je popsáno v předchozí části. Protokoly operací umožňují víte, jak službu používá a můžou je využívat z Power BI po zkopírování dat do účtu úložiště. Azure search poskytuje monitorování balíček obsahu Power BI pro tento účel.


### <a name="enabling-monitoring"></a>Povolení sledování
Otevřete svou službu Azure Search v [webu Azure portal](http://portal.azure.com) pod možnost povolit monitorování.

Vyberte data, která chcete exportovat: Protokoly, metriky nebo obojí. Můžete zkopírovat do účtu úložiště, odesílat do centra událostí nebo exportovat je do Log Analytics.

![Povolení monitorování na portálu][3]

Pokud chcete povolit pomocí Powershellu nebo rozhraní příkazového řádku Azure, najdete v dokumentaci [tady](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Metriky a protokoly a schématy
Když jsou data zkopírována do účtu úložiště, jeho data formátovaná jako JSON a jeho pořadí ve dvou kontejnerů:

* insights – protokoly operationlogs: pro protokoly přenosů služby search
* insights-metrics-pt1m: pro metriky

Existuje jeden objekt blob, za hodinu a kontejner.

Příklad cesty: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Schéma protokolu
Objekty BLOB protokolů obsahují protokoly přenosů služby search.
Každý objekt blob má jeden kořenový objekt volána **záznamy** , která obsahuje pole objektů protokolu.
Každý objekt blob má záznamy na všechny operace, ke kterým došlo během jedné hodiny.

| Název | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |Časové razítko operace |
| resourceId |řetězec |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>VÝCHOZÍ/RESOURCEGROUPS/POSKYTOVATELE /<br/> SPOLEČNOSTI MICROSOFT. HLEDÁNÍ/SEARCHSERVICES/SEARCHSERVICE" |Vaše ID prostředku |
| operationName |řetězec |"Query.Search" |Název operace |
| operationVersion |řetězec |"2015-02-28" |Používá rozhraní api-version |
| category |řetězec |"OperationLogs" |Konstanty |
| resultType |řetězec |"Success" |Možné hodnoty: Úspěch nebo neúspěch |
| resultSignature |int |200 |Kód výsledku protokolu HTTP |
| doby trvání v MS |int |50 |Doba trvání operace v milisekundách |
| properties |objekt |v následující tabulce najdete |Objekt obsahující data specifická pro operace |

**Vlastnosti schématu**

| Název | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| Popis |řetězec |"GET /indexes('content')/docs" |Operace koncového bodu |
| Dotaz |řetězec |"? hledání = AzureSearch & $count = true & verzi api-version = 2015-02-28" |Parametry dotazu |
| Dokumenty |int |42 |Počet zpracovaných dokumentů |
| indexName |řetězec |"testindex" |Název přidružený k operaci indexu |

#### <a name="metrics-schema"></a>Schématu metrik

| Název | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| resourceId |řetězec |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>VÝCHOZÍ/RESOURCEGROUPS/POSKYTOVATELE /<br/>SPOLEČNOSTI MICROSOFT. HLEDÁNÍ/SEARCHSERVICES/SEARCHSERVICE" |vaše id prostředku |
| metricName |řetězec |"Latence" |Název metriky |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |časové razítko operace |
| průměr |int |64 |Průměrná hodnota nezpracovanou ukázky metriky časový interval |
| minimum |int |37 |Minimální hodnota nezpracovanou ukázky metriky časový interval |
| maximum |int |78 |Maximální hodnota nezpracovanou ukázky metriky časový interval |
| celkem |int |258 |Celková hodnota nezpracovanou ukázky metriky časový interval |
| count |int |4 |Počet nezpracovaných vzorků sloužící ke generování metriku |
| timegrain |řetězec |"PT1M" |Časový interval metrika ve formátu ISO 8601 |

Všechny metriky se vykazují v minutových intervalech. Každý metrika uvádí minimální, maximální a průměrné hodnoty za minutu.

Metriky SearchQueriesPerSecond minimum je nejnižší hodnota vyhledávací dotazy za sekundu, která byla zaregistrována tohoto minutě. Totéž platí i pro maximální hodnotu. Průměr je agregace napříč celou minutu.
Představte si, že o tomto scénáři během jedné minuty: jedné sekundy vysoké zatížení, který je maximální pro SearchQueriesPerSecond, za nímž následuje 58 sekund průměrné zatížení a nakonec jedné sekundy se jenom jeden dotaz, což je minimální.

Pro ThrottledSearchQueriesPercentage, minimální, maximální, průměrné a celkový počet, všechny mají stejnou hodnotu: procento vyhledávacích dotazů, které byly omezené z celkového počtu vyhledávací dotazy za jednu minutu.

## <a name="analyzing-your-data-with-power-bi"></a>Analýza dat pomocí Power BI

Doporučujeme používat [Power BI](https://powerbi.microsoft.com) k prozkoumání a vizualizace dat služby. Můžete snadno připojit k účtu úložiště Azure a rychle začít analýza vašich dat.

Azure Search poskytuje [balíček obsahu Power BI](https://app.powerbi.com/getdata/services/azure-search) , který vám umožní monitorovat a porozumět provozu vyhledávání pomocí předdefinovaných grafů a tabulek. Obsahuje sadu sestav Power BI, které automaticky připojují k vašim datům a nabízejí vizuální přehled o vaší vyhledávací služby. Další informace najdete v tématu [stránce nápovědy balíčku obsahu](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Řídicí panel Power BI pro službu Azure Search][4]

## <a name="next-steps"></a>Další postup
Kontrola [horizontální oddíly a replikami](search-limits-quotas-capacity.md) pokyny, jak vyrovnávat rozdělení oddílů a replik pro existující služby.

Navštivte [Správa služby Search v Microsoft Azure](search-manage.md) Další informace o správu služby, nebo [výkon a optimalizace](search-performance-optimization.md) pro Průvodce laděním.

Další informace o vytváření úžasných sestav. Zobrazit [Začínáme s Power BI Desktopu](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) podrobnosti

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
