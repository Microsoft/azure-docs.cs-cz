---
title: Sledování využití a statistické údaje ve službě Azure Search | Microsoft Docs
description: Sledování spotřeby a index velikost prostředků pro službu Azure Search, hostované cloudové vyhledávací službě v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 286569eef8e17909ecab017b67b0ffc044a4bfe4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="monitoring-an-azure-search-service"></a>Monitorování služby Azure Search

Služba Azure Search nabízí různé prostředky pro sledování využití a výkonu služby vyhledávání. Umožňuje vám přístup k metriky, protokoly, index statistiky a rozšířené možnosti monitorování v Power BI. Tento článek popisuje, jak povolit různé strategie monitorování a jak interpretovat Výsledná data.

## <a name="azure-search-metrics"></a>Azure Search metriky
Metriky získáte téměř v reálném čase přehled služby search a jsou dostupné pro všechny služby, bez další nastavení. Které vám umožní sledovat výkon služby po dobu 30 dnů.

Služba Azure Search shromažďuje data pro tři různé metriky:

* Hledání latence: čas vyhledávání službu, kterou potřebuje zpracovat vyhledávací dotazy, agregovat za minutu.
* Vyhledávací dotazy za sekundu (QPS): počet vyhledávání dotazů přijatých za sekundu, agregovat za minutu.
* Procento dotazuje omezenému vyhledávání: procento vyhledávací dotazy, které byly omezené, agregován za minutu.

![Snímek obrazovky QPS aktivity][1]

### <a name="set-up-alerts"></a>nastavení výstrah
Na stránce podrobností metriky můžete nakonfigurovat výstrahy pro aktivaci e-mailových oznámení nebo automatizované akce, když metriky překračuje prahovou hodnotu, která jste definovali.

Další informace o metrikách zkontrolujte úplná dokumentace k Azure monitorování.  

## <a name="how-to-track-resource-usage"></a>Postup sledování využití prostředků
Sledování růst indexy a velikost dokumentu můžete proaktivně upravit kapacity před stiskne horní limit, který jste vytvořili pro vaši službu. Můžete to provést na portálu nebo programově pomocí rozhraní REST API.

### <a name="using-the-portal"></a>Použití portálu

Pokud chcete monitorovat využití prostředků, zobrazit počet a statistiky pro vaši službu v [portál](https://portal.azure.com).

1. Přihlaste se k [portál](https://portal.azure.com).
2. Otevřete řídící panel služby Azure Search. Dlaždice pro službu najdete na domovské stránce, nebo můžete přejít na službu z procházení na panelu vlevo.

V části využití zahrnuje monitorování, která vám ukáže, jaká část dostupné prostředky jsou právě používány. Informace o omezení za služby pro indexy, dokumenty a úložiště najdete v tématu [omezení služby](search-limits-quotas-capacity.md).

  ![Dlaždice využití][2]

> [!NOTE]
> Výše uvedený snímek obrazovky pro bezplatné služby, která může mít nejvýše jednu repliku a každý oddílu a můžete pouze indexy hostitele 3, 10 000 dokumentů nebo 50 MB dat, nastane dříve. Mnohem větší limity služby jsou služby vytvořené na úroveň Basic nebo Standard. Další informace o volba úrovně najdete v tématu [vyberte úroveň nebo SKU](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>Pomocí rozhraní REST API
REST API služby Azure Search a .NET SDK poskytují programový přístup k metrikám služby.  Pokud používáte [indexery](https://msdn.microsoft.com/library/azure/dn946891.aspx) indexu načítat z Azure SQL Database nebo Azure Cosmos DB, je k dispozici získat čísla, která budete potřebovat další rozhraní API.

* [Získat statistiku indexu](/rest/api/searchservice/get-index-statistics)
* [Počet dokumentů](/rest/api/searchservice/count-documents)
* [Získat stav indexeru](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Postup exportu protokoly a metriky

Protokoly operací můžete exportovat služby a nezpracovaná data metrik, které jsou popsané v předchozí části. Operace protokolů umožňují víte, jak služba se používá a mohou být využívány z Power BI po zkopírování dat do účtu úložiště. Vyhledávání systému Azure poskytuje monitorování balíček obsahu Power BI pro tento účel.


### <a name="enabling-monitoring"></a>Povolení monitorování
Otevřete služby Azure Search v [portál Azure](http://portal.azure.com) pod možnost povolit monitorování.

Data, která chcete exportovat: protokoly, metriky nebo obojí. Můžete ho zkopírujte do účtu úložiště, odešle do centra událostí nebo exportovat k analýze protokolů.

![Postup povolení monitorování na portálu][3]

Pokud chcete povolit pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure CLI, najdete v dokumentaci [zde](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Protokoly a metriky schémata
Po zkopírování dat do účtu úložiště dat formátu JSON a jeho místě v dvě kontejnerů:

* insights-logs-operationlogs: vyhledávání přenosů protokolů
* Statistika. metriky pt1m: metrik

Neexistuje jeden objekt blob, za hodinu na kontejneru.

Příklad cesty: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Schéma protokolu
Objekty BLOB protokoly obsahovat protokolů přenosů služby vyhledávání.
Každý objekt blob má jeden kořenový objekt názvem **záznamy** obsahující pole objektů protokolu.
Každý objekt blob má záznamy na všechny operace, ke kterým došlo během tutéž hodinu.

| Název | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |Časové razítko operace |
| resourceId |řetězec |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>SKUPINYPROSTŘEDKŮ/VÝCHOZÍ NEBO ZPROSTŘEDKOVATELE NEBO<br/> SPOLEČNOSTI MICROSOFT. HLEDÁNÍ NEBO SEARCHSERVICES/SEARCHSERVICE" |Vaše ID prostředku |
| operationName |řetězec |"Query.Search" |Název operace |
| operationVersion |řetězec |"2015-02-28" |Použít verze rozhraní api |
| category |řetězec |"OperationLogs" |Konstantní |
| resultType |řetězec |"ÚSPĚCH" |Možné hodnoty: úspěch nebo neúspěch |
| resultSignature |celá čísla |200 |Kód výsledku HTTP |
| durationMS |celá čísla |50 |Doba trvání operace v milisekundách |
| properties |objekt |najdete v následující tabulce |Objekt obsahující data specifická pro operace |

**Vlastnosti schématu**
| Název | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| Popis |řetězec |"GET /indexes('content')/docs" |Operace koncového bodu |
| Dotaz |řetězec |"? hledání = AzureSearch & $count = true & verze api-version = 2015-02-28" |Parametry dotazu |
| Dokumenty |celá čísla |42 |Počet zpracovaných dokumentů |
| indexName |řetězec |"testindex" |Název přidružené k operaci indexu |

#### <a name="metrics-schema"></a>Metriky schématu
| Název | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| resourceId |řetězec |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>SKUPINYPROSTŘEDKŮ/VÝCHOZÍ NEBO ZPROSTŘEDKOVATELE NEBO<br/>SPOLEČNOSTI MICROSOFT. HLEDÁNÍ NEBO SEARCHSERVICES/SEARCHSERVICE" |vaše id prostředku |
| metricName |řetězec |"Latence" |Název metriky |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |časové razítko operace |
| průměr |celá čísla |64 |Průměrná hodnota nezpracovaná ukázky metriky časový interval |
| minimum |celá čísla |37 |Minimální hodnota nezpracovaná ukázky metriky časový interval |
| maximum |celá čísla |78 |Maximální hodnota, která nezpracovaná ukázky metriky časový interval |
| celkem |celá čísla |258 |Celkové hodnoty nezpracovaná ukázky metriky časový interval |
| počet |celá čísla |4 |Počet nezpracovaných vzorků sloužící ke generování metriku |
| časovými úseky |řetězec |"PT1M" |Časový úsek metriky v ISO 8601. |

Všechny metriky oznamuje v intervalech jedné minuty. Každý metrika uvádí minimální, maximální a průměrné hodnoty za minutu.

Pro metriku SearchQueriesPerSecond minimum je nejnižší hodnotu pro vyhledávací dotazy za sekundu, který byl zaregistrován během tohoto minutu. Totéž platí i pro maximální hodnota. Průměrná, je agregace napříč celou minutu.
Představte si, že o tomto scénáři během jedné minuty: sekundu vysokou načíst tedy maximální pro SearchQueriesPerSecond, za nímž následuje 58 sekund průměrné zatížení, a nakonec jednu sekundu s pouze jeden dotaz, což je minimální.

Pro ThrottledSearchQueriesPercentage, minimální, maximální, průměrná a celkový počet, všechny mají stejnou hodnotu: procento vyhledávací dotazy, které byly omezené z celkový počet vyhledávacích dotazů za jednu minutu.

## <a name="analyzing-your-data-with-power-bi"></a>Analýza dat s Power BI

Doporučujeme používat [Power BI](https://powerbi.microsoft.com) umožní zkoumat a vizualizovat vaše data. Můžete snadno připojit k účtu úložiště Azure a rychle začít analýza vaše data.

Poskytuje službě Azure Search [balíček obsahu Power BI](https://app.powerbi.com/getdata/services/azure-search) , které umožňuje sledovat a pochopit provozu vyhledávání pomocí předdefinovaných grafů a tabulek. Obsahuje sadu sestavy Power BI, které automaticky připojit k datům a nabízejí visual přehled o vaši službu vyhledávání. Další informace najdete v tématu [balíček obsahu stránce nápovědy](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Řídicí panel Power BI pro službu Azure Search][4]

## <a name="next-steps"></a>Další postup
Zkontrolujte [škálování repliky a oddíly, které](search-limits-quotas-capacity.md) pokyny o tom, jak vyrovnávání přidělení replik pro existující službu a oddíly.

Navštivte [Správa služby Search v Microsoft Azure](search-manage.md) Další informace o správě služby, nebo [výkonu a optimalizace](search-performance-optimization.md) pro ladění pokyny.

Další informace o vytváření úžasné sestav. V tématu [Začínáme s Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) podrobnosti

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
