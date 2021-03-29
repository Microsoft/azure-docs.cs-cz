---
title: Shromažďovat data protokolu
titleSuffix: Azure Cognitive Search
description: Shromážděte a analyzujte data protokolu povolením nastavení diagnostiky a pak použijte dotazovací jazyk Kusto k prozkoumání výsledků.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3c8dd5cd9da2fd1e741635a6471c0662066d147e
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709935"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Shromažďování a analýza dat protokolu pro Azure Kognitivní hledání

Diagnostické nebo provozní protokoly poskytují přehled o podrobných operacích služby Azure Kognitivní hledání a jsou užitečné pro monitorování procesů služeb a úloh. Interně společnost Microsoft zachovává systémové informace v back-endu po krátkou dobu (asi 30 dní), stačí pro šetření a analýzu, pokud zadáte lístek podpory. Pokud ale chcete vlastnictví pro provozní data, měli byste nakonfigurovat nastavení diagnostiky, které určuje, kde se mají shromažďovat informace o protokolování.

Protokolování diagnostiky je povoleno prostřednictvím integrace s [Azure monitor](../azure-monitor/index.yml). 

Při nastavování diagnostického protokolování budete požádáni o zadání mechanismu úložiště. Následující tabulka uvádí možnosti pro shromažďování a uchovávání dat.

| Prostředek | Použití |
|----------|----------|
| [Odeslání do pracovního prostoru služby Log Analytics](../azure-monitor/essentials/tutorial-resource-logs.md) | Události a metriky se odesílají do Log Analytics pracovního prostoru, který se dá dotazovat na portálu, aby vracel podrobné informace. Úvod najdete v tématu Začínáme [s protokoly Azure monitor](../azure-monitor/logs/log-analytics-tutorial.md) . |
| [Archivace s úložištěm objektů BLOB](../storage/blobs/storage-blobs-overview.md) | Události a metriky se archivují do kontejneru objektů BLOB a ukládají se do souborů JSON. Protokoly můžou být poměrně podrobné (za hodinu a minutu), které jsou užitečné pro zkoumání konkrétního incidentu, ale ne pro vyšetřování otevřeného a nedokončeného. K zobrazení nezpracovaného souboru protokolu nebo Power BI k agregaci a vizualizaci dat protokolu použijte Editor JSON.|
| [Streamování do centra událostí](../event-hubs/index.yml) | Události a metriky se streamují do služby Azure Event Hubs. Tuto možnost vyberte jako alternativní službu pro shromažďování dat pro velmi velké protokoly. |

## <a name="prerequisites"></a>Požadavky

Vytvářejte prostředky předem, abyste při konfiguraci diagnostického protokolování mohli vybrat jednu nebo víc.

+ [Vytvoření pracovního prostoru Log Analytics](../azure-monitor/logs/quick-create-workspace.md)

+ [Vytvoření účtu úložiště](../storage/common/storage-account-create.md)

+ [Vytvoření centra událostí](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>Povolení shromažďování dat

Nastavení diagnostiky určují, jak se shromažďují protokolované události a metriky.

1. V části **Monitorování** vyberte **Nastavení diagnostiky**.

   ![Nastavení diagnostiky](./media/search-monitor-usage/diagnostic-settings.png "Nastavení diagnostiky")

1. Vybrat **+ Přidat nastavení diagnostiky**

1. Zaškrtněte **Log Analytics**, vyberte svůj pracovní prostor a vyberte **OperationLogs** a **AllMetrics**.

   ![Konfigurace shromažďování dat](./media/search-monitor-usage/configure-storage.png "Konfigurace shromažďování dat")

1. Nastavení uložte.

1. Po povolení protokolování můžete pomocí vyhledávací služby začít generovat protokoly a metriky. Bude trvat čas před tím, než budou události protokolu a metriky k dispozici.

V případě Log Analytics bude k dispozici několik minut, než budou data k dispozici, a potom můžete spustit dotazy Kusto a vracet data. Další informace najdete v tématu [monitorování požadavků na dotazy](search-monitor-logs.md).

Pro úložiště objektů BLOB trvá jednu hodinu, než se kontejnery zobrazí v úložišti objektů BLOB. Jeden objekt BLOB se za hodinu vychází z každého kontejneru. Kontejnery se vytvářejí pouze v případě, že existuje aktivita pro protokolování nebo měření. Když se data zkopírují do účtu úložiště, data se naformátují jako JSON a umístí se do dvou kontejnerů:

+ Insights-logs-operationlogs: pro hledání protokolů přenosů
+ Insights – metriky – pt1m: pro metriky

## <a name="query-log-information"></a>Informace protokolu dotazu

Dvě tabulky obsahují protokoly a metriky pro Azure Kognitivní hledání: **AzureDiagnostics** a **AzureMetrics**.

1. V části **monitorování** vyberte **protokoly**.

1. Do okna dotazu zadejte **AzureMetrics** . Spusťte tento jednoduchý dotaz, abyste se seznámili s daty shromážděnými v této tabulce. Posuňte se v tabulce, abyste zobrazili metriky a hodnoty. Všimněte si, že počet záznamů v horní části a pokud vaše služba shromažďuje metriky pro určitou chvíli, možná budete chtít upravit časový interval, abyste získali spravovatelnou datovou sadu.

   ![Tabulka AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabulka AzureMetrics")

1. Zadejte následující dotaz, který vrátí tabelární sadu výsledků.

   ```kusto
   AzureMetrics
   | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Opakujte předchozí kroky, počínaje **AzureDiagnostics** a vraťte všechny sloupce pro informativní účely, a potom klikněte na další selektivní dotaz, který extrahuje zajímavé informace.

   ```kusto
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabulka AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabulka AzureDiagnostics")

## <a name="kusto-query-examples"></a>Příklady dotazů Kusto

Pokud jste povolili diagnostické protokolování, můžete zadat dotaz na **AzureDiagnostics** pro seznam operací, které byly spuštěny ve vaší službě a kdy. Můžete také korelovat aktivitu a prozkoumat změny v výkonu.

#### <a name="example-list-operations"></a>Příklad: výpis operací 

Vrátí seznam operací a počet každého z nich.

```kusto
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Příklad: korelace operací

Porovnejte požadavky na dotazy s operacemi indexování a vykreslete datové body v grafu s časovým plánem, abyste viděli, že se operace shodují.

```kusto
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>Protokolované operace

Události zaznamenané v Azure Monitor zahrnují i ty, které souvisejí s indexováním a dotazy. Tabulka **AzureDiagnostics** v Log Analytics shromažďuje provozní data týkající se dotazů a indexování.

| OperationName | Description |
|---------------|-------------|
| ServiceStats | Tato operace je rutinním voláním metody [Get Service STATISTICS](/rest/api/searchservice/get-service-statistics), která je volána přímo nebo implicitně k naplnění stránky přehledu portálu při načtení nebo aktualizaci. |
| Dotaz. Search |  Dotazy na požadavky na index najdete v tématu [monitorování dotazů](search-monitor-queries.md) pro informace o protokolovaných dotazech.|
| Indexuje se. index  | Tato operace je voláním pro [Přidání, aktualizaci nebo odstranění dokumentů](/rest/api/searchservice/addupdate-or-delete-documents). |
| indexy. Vzorový | Toto je index vytvořený Průvodcem importem dat. |
| Indexery. Create | Vytvořte indexer explicitně nebo implicitně prostřednictvím Průvodce importem dat. |
| Indexery. Get | Vrátí název indexeru pokaždé, když je indexer spuštěn. |
| Indexery. stav | Vrátí stav indexeru pokaždé, když je indexer spuštěn. |
| Zdroje dat. Get | Vrátí název zdroje dat pokaždé, když je spuštěn indexer.|
| Indexy. Get | Při každém spuštění indexeru vrátí název indexu. |

## <a name="log-schema"></a>Schéma protokolu

Pokud vytváříte vlastní sestavy, datové struktury, které obsahují data protokolu Azure Kognitivní hledání, odpovídají schématu uvedenému níže. Pro úložiště objektů BLOB má každý objekt BLOB jeden kořenový objekt nazvaný **záznamy** obsahující pole objektů log. Každý objekt BLOB obsahuje záznamy pro všechny operace, které byly provedeny během stejné hodiny.

Následující tabulka uvádí částečný seznam polí společných pro protokolování prostředků.

| Název | Typ | Příklad | Poznámky |
| --- | --- | --- | --- |
| timeGenerated |datetime |"2018-12-07T00:00:43.6872559 Z" |Časové razítko operace |
| resourceId |řetězec |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/VÝCHOZÍ/POSKYTOVATELÉ/<br/> Microsoft. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Vaše ResourceId |
| operationName |řetězec |"Query. Search" |Název operace |
| operationVersion |řetězec |"2020-06-30" |Použitá verze rozhraní API |
| category |řetězec |"OperationLogs" | – konstanta |
| resultType |řetězec |Nástup |Možné hodnoty: úspěch nebo neúspěch |
| resultSignature |int |200 |Kód výsledku HTTP |
| Trvání v MS |int |50 |Doba trvání operace v milisekundách |
| properties |object |Podívejte se na následující tabulku. |Objekt obsahující data specifická pro danou operaci |

### <a name="properties-schema"></a>Schéma vlastností

Níže uvedené vlastnosti jsou specifické pro Azure Kognitivní hledání.

| Název | Typ | Příklad | Poznámky |
| --- | --- | --- | --- |
| Description_s |řetězec |"GET/Indexes (' Content ')/docs" |Koncový bod operace |
| Documents_d |int |42 |Počet zpracovaných dokumentů |
| IndexName_s |řetězec |"test-index" |Název indexu přidruženého k operaci |
| Query_s |řetězec |"? Search = AzureSearch&$count = true&API-Version = 2020-06-30" |Parametry dotazu |

## <a name="metrics-schema"></a>Schéma metrik

Metriky jsou zachyceny pro požadavky na dotazy a měřené v jednom minutovém intervalu. Každá metrika zpřístupňuje minimální, maximální a průměrné hodnoty za minutu. Další informace najdete v tématu [monitorování požadavků na dotazy](search-monitor-queries.md).

| Název | Typ | Příklad | Poznámky |
| --- | --- | --- | --- |
| resourceId |řetězec |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/VÝCHOZÍ/POSKYTOVATELÉ/<br/>Microsoft. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |ID prostředku |
| metricName |řetězec |Latence |název metriky |
| time |datetime |"2018-12-07T00:00:43.6872559 Z" |časové razítko operace |
| Průměr |int |64 |Průměrná hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotkách v sekundách nebo procentech v závislosti na metrikě. |
| Minimum |int |37 |Minimální hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotek v sekundách. |
| Maximum |int |78 |Maximální hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotek v sekundách.  |
| celkem |int |258 |Celková hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotek v sekundách.  |
| count |int |4 |Počet metrik vygenerovaných z uzlu do protokolu v intervalu 1 minuty.  |
| timegrain |řetězec |"PT1M" |Časový interval metriky v normě ISO 8601. |

Je běžné, že dotazy se mají spouštět v milisekundách, takže se v metrikě jako QPS zobrazí jenom dotazy, které měří jako sekunda.

U **vyhledávacích dotazů za sekundu** metrika minimální je nejnižší hodnota pro vyhledávací dotazy za sekundu, které byly zaregistrovány během dané minuty. Totéž platí pro maximální hodnotu. Průměrná hodnota je agregovaná napříč celou minutou. Například během jedné minuty můžete mít podobný vzor: jedna sekunda vysokého zatížení, která je maximální hodnota pro SearchQueriesPerSecond, následovaná 58 sekundami průměrného zatížení a nakonec jedna sekunda pouze jedním dotazem, což je minimální hodnota.

U **omezených vyhledávacích dotazů procento**, minimum, maximum, průměr a součet musí mít všechny stejnou hodnotu: procento vyhledávacích dotazů, které byly omezeny, z celkového počtu vyhledávacích dotazů během jedné minuty.

## <a name="view-raw-log-files"></a>Zobrazit nezpracované soubory protokolu

Úložiště objektů BLOB se používá k archivaci souborů protokolu. K zobrazení souboru protokolu můžete použít libovolný editor JSON. Pokud ho nemáte, doporučujeme [Visual Studio Code](https://code.visualstudio.com/download).

1. V Azure Portal otevřete svůj účet úložiště. 

2. V levém navigačním podokně klikněte na **objekty blob**. Měli byste vidět **Insights-logs-operationlogs** a **Insights – metriky – pt1m**. Tyto kontejnery vytvoří služba Azure Kognitivní hledání, když se data protokolu exportují do úložiště objektů BLOB.

3. Klikněte na hierarchii složek dolů, dokud nedosáhnete souboru. JSON.  K stažení souboru použijte kontextovou nabídku.

Po stažení souboru ho otevřete v editoru JSON, abyste mohli zobrazit jeho obsah.

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, Projděte si základní informace o monitorování služby Search, kde se dozvíte o plném rozsahu funkcí dohledu.

> [!div class="nextstepaction"]
> [Monitorování operací a aktivit v Azure Kognitivní hledání](search-monitor-usage.md)