---
title: Shromažďovat data protokolu
titleSuffix: Azure Cognitive Search
description: Shromážděte a analyzujte data protokolu povolením nastavení diagnostiky a pak použijte dotazovací jazyk Kusto k prozkoumání výsledků.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 192591dedb0b5519fdcecde8c8683be87237c828
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127815"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Shromažďování a analýza dat protokolu pro Azure Kognitivní hledání

Diagnostické nebo provozní protokoly poskytují přehled o podrobných operacích služby Azure Kognitivní hledání a jsou užitečné pro monitorování procesů služeb a úloh. Interně protokoly existují v back-endu po krátkou dobu, postačující pro šetření a analýzu, pokud zadáte lístek podpory. Pokud ale chcete, aby se pro provozní data řídil směr od sebe, měli byste nakonfigurovat nastavení diagnostiky, které určuje, kde se mají shromažďovat informace o protokolování.

Nastavení protokolů je užitečné pro diagnostiku a zachovávání provozní historie. Po povolení protokolování můžete spouštět dotazy nebo sestavovat sestavy pro strukturované analýzy.

Následující tabulka uvádí možnosti pro shromažďování a uchovávání dat.

| Prostředek | Použití |
|----------|----------|
| [Odeslat do pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Události a metriky se odesílají do Log Analytics pracovního prostoru, který se dá dotazovat na portálu, aby vracel podrobné informace. Úvod najdete v tématu Začínáme [s protokoly Azure monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) . |
| [Archivace s úložištěm objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Události a metriky se archivují do kontejneru objektů BLOB a ukládají se do souborů JSON. Protokoly můžou být poměrně podrobné (za hodinu a minutu), které jsou užitečné pro zkoumání konkrétního incidentu, ale ne pro vyšetřování otevřeného a nedokončeného. K zobrazení nezpracovaného souboru protokolu nebo Power BI k agregaci a vizualizaci dat protokolu použijte Editor JSON.|
| [Streamování do centra událostí](https://docs.microsoft.com/azure/event-hubs/) | Události a metriky se streamují do služby Azure Event Hubs. Tuto možnost vyberte jako alternativní službu pro shromažďování dat pro velmi velké protokoly. |

Protokoly Azure Monitor a BLOB Storage jsou k dispozici jako bezplatná služba, takže si ji můžete vyzkoušet bez poplatků za dobu života předplatného Azure. Application Insights se zaregistrovat a použít, pokud je velikost dat aplikace za určitých mezí (podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) ).

## <a name="prerequisites"></a>Požadavky

Pokud používáte Log Analytics nebo Azure Storage, můžete vytvořit prostředky předem.

+ [Vytvoření pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [vytvořit účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

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

1. V části **monitorování**vyberte **protokoly**.

1. Do okna dotazu zadejte **AzureMetrics** . Spusťte tento jednoduchý dotaz, abyste se seznámili s daty shromážděnými v této tabulce. Posuňte se v tabulce, abyste zobrazili metriky a hodnoty. Všimněte si, že počet záznamů v horní části a pokud vaše služba shromažďuje metriky pro určitou chvíli, možná budete chtít upravit časový interval, abyste získali spravovatelnou datovou sadu.

   ![Tabulka AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabulka AzureMetrics")

1. Zadejte následující dotaz, který vrátí tabelární sadu výsledků.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Opakujte předchozí kroky, počínaje **AzureDiagnostics** a vraťte všechny sloupce pro informativní účely, a potom klikněte na další selektivní dotaz, který extrahuje zajímavé informace.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabulka AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabulka AzureDiagnostics")

## <a name="log-schema"></a>Schéma protokolu

Datové struktury, které obsahují data protokolu Azure Kognitivní hledání, odpovídají schématu níže. 

Pro úložiště objektů BLOB má každý objekt BLOB jeden kořenový objekt nazvaný **záznamy** obsahující pole objektů log. Každý objekt BLOB obsahuje záznamy pro všechny operace, které byly provedeny během stejné hodiny.

Následující tabulka uvádí částečný seznam polí společných pro protokolování prostředků.

| Název | Typ | Příklad | Poznámky |
| --- | --- | --- | --- |
| timeGenerated |datetime |"2018-12-07T00:00:43.6872559 Z" |Časové razítko operace |
| resourceId |řetězec |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/VÝCHOZÍ/POSKYTOVATELÉ/<br/> Microsoft. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Vaše ResourceId |
| operationName |řetězec |"Query. Search" |Název operace |
| operationVersion |řetězec |"2019-05-06" |Použitá verze rozhraní API |
| category |řetězec |"OperationLogs" | – konstanta |
| resultType |řetězec |Nástup |Možné hodnoty: úspěch nebo neúspěch |
| resultSignature |int |200 |Kód výsledku HTTP |
| Trvání v MS |int |50 |Doba trvání operace v milisekundách |
| properties |objekt |Podívejte se na následující tabulku. |Objekt obsahující data specifická pro danou operaci |

### <a name="properties-schema"></a>Schéma vlastností

Níže uvedené vlastnosti jsou specifické pro Azure Kognitivní hledání.

| Název | Typ | Příklad | Poznámky |
| --- | --- | --- | --- |
| Description_s |řetězec |"GET/Indexes (' Content ')/docs" |Koncový bod operace |
| Documents_d |int |42 |Počet zpracovaných dokumentů |
| IndexName_s |řetězec |"test-index" |Název indexu přidruženého k operaci |
| Query_s |řetězec |"? Search = AzureSearch&$count = true&API-Version = 2019-05-06" |Parametry dotazu |

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