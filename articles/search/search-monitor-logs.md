---
title: Shromažďování dat protokolu
titleSuffix: Azure Cognitive Search
description: Shromažďujte a analyzujte data protokolu povolením diagnostického nastavení a potom pomocí dotazovacího jazyka Kusto prozkoumejte výsledky.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462346"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Shromažďování a analýza dat protokolu pro Azure Cognitive Search

Diagnostické nebo provozní protokoly poskytují přehled o podrobných operacích Azure Cognitive Search a jsou užitečné pro monitorování procesů služeb a úloh. Interně protokoly existují v back-endu na krátkou dobu, dostatečné pro šetření a analýzu, pokud soubor lístek podpory. Pokud však chcete vlastní směr nad provozními daty, měli byste nakonfigurovat diagnostické nastavení určující, kde se shromažďují informace o protokolování.

Nastavení protokolů je užitečné pro diagnostiku a zachování provozní historie. Po povolení protokolování můžete spouštět dotazy nebo vytvářet sestavy pro strukturovanou analýzu.

V následující tabulce jsou vyjmuty možnosti pro shromažďování a uchovávání dat.

| Prostředek | Použití |
|----------|----------|
| [Odeslat do pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Události a metriky jsou odesílány do pracovního prostoru Log Analytics, který může být dotazován na portálu vrátit podrobné informace. Úvod najdete v tématu [Začínáme s protokoly Azure Monitoru.](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Archivujte pomocí úložiště objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Události a metriky jsou archivovány do kontejneru objektů Blob a uloženy v souborech JSON. Protokoly mohou být poměrně podrobné (podle hodiny / minuty), užitečné pro výzkum konkrétního incidentu, ale ne pro otevřené vyšetřování. Pomocí editoru JSON můžete zobrazit nezpracovaný soubor protokolu nebo Power BI k agregaci a vizualizaci dat protokolu.|
| [Streamování do centra událostí](https://docs.microsoft.com/azure/event-hubs/) | Události a metriky se streamují do služby Azure Event Hubs. Tuto možnost zvolte jako alternativní službu shromažďování dat pro velmi velké protokoly. |

Protokoly Azure Monitoru i úložiště objektů blob jsou k dispozici jako bezplatná služba, takže si ji můžete vyzkoušet zdarma po dobu životnosti předplatného Azure. Application Insights je zdarma k registraci a použití tak dlouho, dokud velikost dat aplikace je pod určitými omezeními (viz [stránka s cenami](https://azure.microsoft.com/pricing/details/monitor/) podrobnosti).

## <a name="prerequisites"></a>Požadavky

Pokud používáte Log Analytics nebo Azure Storage, můžete předem vytvořit prostředky.

+ [Vytvoření pracovního prostoru analýzy protokolů](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Povolení shromažďování dat

Nastavení diagnostiky určují způsob shromažďování protokolovaných událostí a metrik.

1. V části **Monitorování** vyberte **Nastavení diagnostiky**.

   ![Nastavení diagnostiky](./media/search-monitor-usage/diagnostic-settings.png "Nastavení diagnostiky")

1. Vybrat **+ Přidat diagnostické nastavení**

1. Zkontrolujte **analýzu protokolů**, vyberte pracovní prostor a vyberte **OperationLogs** a **AllMetrics**.

   ![Konfigurace shromažďování dat](./media/search-monitor-usage/configure-storage.png "Konfigurace shromažďování dat")

1. Uložte nastavení.

1. Po povolení protokolování můžete pomocí vyhledávací služby spustit generování protokolů a metrik. Bude nějakou dobu trvat, než budou protokolované události a metriky k dispozici.

Pro Log Analytics bude trvat několik minut, než budou data k dispozici, po které můžete spustit dotazy Kusto pro vrácení dat. Další informace naleznete v tématu [Sledování požadavků na dotazy](search-monitor-logs.md).

Pro úložiště objektů Blob trvá jednu hodinu, než se kontejnery zobrazí ve úložišti objektů Blob. Na kontejner je jeden objekt blob za hodinu. Kontejnery jsou vytvořeny pouze v případě, že je aktivita protokolu nebo měření. Když jsou data zkopírována do účtu úložiště, data jsou formátována jako JSON a umístěna do dvou kontejnerů:

+ insights-logs-operationlogs: pro protokoly provozu vyhledávání
+ insights-metrics-pt1m: pro metriky

## <a name="query-log-information"></a>Informace protokolu dotazu

V diagnostických protokolech obsahují dvě tabulky protokoly a metriky pro Azure Cognitive Search: **AzureDiagnostics** a **AzureMetrics**.

1. V části **Sledování**vyberte **protokoly**.

1. Zadejte **AzureMetrics** v okně dotazu. Spusťte tento jednoduchý dotaz, abyste se seznámili s daty shromážděnými v této tabulce. Posunutím přes tabulku zobrazíte metriky a hodnoty. Všimněte si počtu záznamů v horní části a pokud vaše služba nějakou dobu shromažďuje metriky, můžete upravit časový interval, abyste získali spravovatelnou sadu dat.

   ![Tabulka AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabulka AzureMetrics")

1. Zadáním následujícího dotazu vrátíte tabulkovou sadu výsledků.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Opakujte předchozí kroky, počínaje **AzureDiagnostics** vrátit všechny sloupce pro informační účely, následovaný selektivnější dotaz, který extrahuje zajímavější informace.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabulka AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabulka AzureDiagnostics")

## <a name="log-schema"></a>Schéma protokolu

Datové struktury, které obsahují data protokolu Azure Cognitive Search, odpovídají níže uvedenému schématu. 

Pro úložiště objektů blob má každý objekt blob jeden kořenový objekt nazývaný **záznamy** obsahující pole objektů protokolu. Každý objekt blob obsahuje záznamy pro všechny operace, které proběhly během stejné hodiny.

V následující tabulce je částečný seznam polí společných pro diagnostické protokolování.

| Name (Název) | Typ | Příklad | Poznámky |
| --- | --- | --- | --- |
| timeGenerated |datetime |"2018-12-07T00:00:43.6872559Z" |Časové razítko operace |
| resourceId |řetězec |"/PŘEDPLATNÉ/11111111-1111-1111-1111-111111111111111111/<br/>SKUPINY ZDROJŮ/VÝCHOZÍ/POSKYTOVATELÉ/<br/> Microsoft. HLEDAT/HLEDATSLUŽBY/VYHLEDÁVACÍ SLUŽBY" |Vaše ResourceId |
| operationName |řetězec |"Dotaz.Hledat" |Název operace |
| operationVersion |řetězec |"2019-05-06" |Použitá verze api |
| category |řetězec |"OperationLogs" | – konstanta |
| resultType |řetězec |"Úspěch" |Možné hodnoty: Úspěch nebo neúspěch |
| resultSignature |int |200 |Kód výsledku PROTOKOLU HTTP |
| durationMS |int |50 |Doba trvání operace v milisekundách |
| properties |objekt |Viz následující tabulka |Objekt obsahující data specifická pro operaci |

### <a name="properties-schema"></a>Schéma vlastností

Níže uvedené vlastnosti jsou specifické pro Azure Cognitive Search.

| Name (Název) | Typ | Příklad | Poznámky |
| --- | --- | --- | --- |
| Description_s |řetězec |"GET /indexes('content')/docs" |Koncový bod operace |
| Documents_d |int |42 |Počet zpracovaných dokumentů |
| IndexName_s |řetězec |"test-index" |Název indexu přidruženého k operaci |
| Query_s |řetězec |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Parametry dotazu |

## <a name="metrics-schema"></a>Schéma metrik

Metriky jsou zachyceny pro požadavky na dotazy a měří v intervalech jedné minuty. Každá metrika zveřejňuje minimální, maximální a průměrné hodnoty za minutu. Další informace naleznete v tématu [Sledování požadavků na dotazy](search-monitor-queries.md).

| Name (Název) | Typ | Příklad | Poznámky |
| --- | --- | --- | --- |
| resourceId |řetězec |"/PŘEDPLATNÉ/11111111-1111-1111-1111-111111111111111111/<br/>SKUPINY ZDROJŮ/VÝCHOZÍ/POSKYTOVATELÉ/<br/>Microsoft. HLEDAT/HLEDATSLUŽBY/VYHLEDÁVACÍ SLUŽBY" |ID zdroje |
| metricName |řetězec |"Latence" |název metriky |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |časové razítko operace |
| Průměr |int |64 |Průměrná hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotky v sekundách nebo procento, v závislosti na metrice. |
| Minimum |int |37 |Minimální hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotky v sekundách. |
| Maximum |int |78 |Maximální hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotky v sekundách.  |
| celkem |int |258 |Celková hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotky v sekundách.  |
| count |int |4 |Počet metrik vyzařovaných z uzlu do protokolu v intervalu jedné minuty.  |
| časový zrno |řetězec |"PT1M" |Časové zrno metriky v ISO 8601. |

Je běžné, že dotazy spustit v milisekundách, takže pouze dotazy, které měří jako sekundy se zobrazí v metriky, jako je QPS.

Pro **metriku Vyhledávací dotazy za sekundu** je minimální nejnižší hodnota pro vyhledávací dotazy za sekundu, která byla zaregistrována během dané minuty. Totéž platí pro maximální hodnotu. Průměr, je agregát po celou minutu. Například během jedné minuty může mít vzor, jako je tento: jedna sekunda vysoké zatížení, které je maximální pro SearchQueriesPerSecond, následuje 58 sekund průměrné zatížení a nakonec jednu sekundu pouze jeden dotaz, což je minimum.

Pro **omezené vyhledávací dotazy procento**, minimální, maximální, průměrné a celkové, všechny mají stejnou hodnotu: procento vyhledávacích dotazů, které byly omezeny, z celkového počtu vyhledávacích dotazů během jedné minuty.

## <a name="view-raw-log-files"></a>Zobrazit nezpracované soubory protokolu

Úložiště objektů blob se používá pro archivaci souborů protokolu. K zobrazení souboru protokolu můžete použít libovolný editor JSON. Pokud ho nemáte, doporučujeme [kód sady Visual Studio](https://code.visualstudio.com/download).

1. Na webu Azure Portal si otevřete účet úložiště. 

2. V levém navigačním podokně klikněte na **objekty Blob .** Měli byste vidět **přehledy protokoly-operationlogs** a **insights-metrics-pt1m**. Tyto kontejnery jsou vytvořené Azure Cognitive Search při exportu dat protokolu do úložiště objektů blob.

3. Klikněte dolů na hierarchii složek, dokud se nedostanete k souboru JSON.  Ke stažení souboru použijte kontextovou nabídku.

Po stažení souboru otevřete jej v editoru JSON a zobrazte obsah.

## <a name="next-steps"></a>Další kroky

Pokud jste tak ještě neučinili, přečtěte si základy monitorování vyhledávacích služeb, abyste se dozvěděli o celé řadě funkcí dohledu.

> [!div class="nextstepaction"]
> [Monitorování operací a aktivit v Azure Cognitive Search](search-monitor-usage.md)