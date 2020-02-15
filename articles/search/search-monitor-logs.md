---
title: Shromažďovat data protokolu
titleSuffix: Azure Cognitive Search
description: Shromážděte a analyzujte data protokolu povolením nastavení diagnostiky a pak použijte dotazovací jazyk Kusto k prozkoumání výsledků.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 2849dc94f1c45dda3da09120adebba6e004eb96b
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211175"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Shromažďování a analýza dat protokolu pro Azure Kognitivní hledání

Diagnostické nebo provozní protokoly poskytují přehled o podrobných operacích služby Azure Kognitivní hledání a jsou užitečné pro monitorování procesů služeb a úloh. Interně protokoly existují v back-endu po krátkou dobu, postačující pro šetření a analýzu, pokud zadáte lístek podpory. Pokud ale chcete, aby se pro provozní data řídil směr od sebe, měli byste nakonfigurovat nastavení diagnostiky, které určuje, kde se mají shromažďovat informace o protokolování. 

Nastavení protokolů je užitečné pro diagnostiku a zachovávání provozní historie. Po povolení protokolování můžete spouštět dotazy nebo sestavovat sestavy pro strukturované analýzy.

Následující tabulka uvádí možnosti pro shromažďování a uchovávání dat.

| Prostředek | Použití |
|----------|----------|
| [Odeslat do pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Protokolované události a metriky dotazů na základě níže uvedených schémat. Události jsou protokolovány do Log Analytics pracovního prostoru. Pomocí Log Analytics můžete spustit dotazy a vracet podrobné informace. Další informace najdete v tématu [Začínáme s protokoly Azure monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) . |
| [Archivace s úložištěm objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Protokolované události a metriky dotazů na základě níže uvedených schémat. Události se zaznamenávají do kontejneru objektů BLOB a ukládají se do souborů JSON. Protokoly můžou být poměrně podrobné (za hodinu a minutu), které jsou užitečné pro zkoumání konkrétního incidentu, ale ne pro vyšetřování otevřeného a nedokončeného. K zobrazení souboru protokolu použijte Editor JSON.|
| [Streamování do centra událostí](https://docs.microsoft.com/azure/event-hubs/) | Protokolované události a metriky dotazů na základě schémat popsaných v tomto článku. Tuto možnost vyberte jako alternativní službu pro shromažďování dat pro velmi velké protokoly. |

Protokoly Azure Monitor a BLOB Storage jsou k dispozici jako bezplatná služba, takže si ji můžete vyzkoušet bez poplatků za dobu života předplatného Azure. Application Insights se zaregistrovat a použít, pokud je velikost dat aplikace za určitých mezí (podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) ).

## <a name="prerequisites"></a>Požadavky

Pokud používáte Log Analytics nebo Azure Storage, můžete vytvořit prostředky předem.

+ [Vytvoření pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ Pokud potřebujete archiv protokolu, [vytvořte účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) .

## <a name="create-a-log"></a>Vytvoření protokolu

Nastavení diagnostiky definují shromažďování dat. Nastavení určuje, jak a co se shromáždí. 

1. V části **monitorování**vyberte **nastavení diagnostiky**.

   ![Nastavení diagnostiky](./media/search-monitor-usage/diagnostic-settings.png "Nastavení diagnostiky")

1. Vybrat **+ Přidat nastavení diagnostiky**

1. Zvolte data, kterou chcete exportovat: protokoly, metriky nebo obojí. Data můžete shromažďovat v účtu úložiště, v pracovním prostoru Log Analytics nebo je streamovat do centra událostí.

   Doporučuje se Log Analytics, protože na portálu se můžete dotazovat na pracovní prostor.

   Pokud používáte také úložiště objektů blob, kontejnery a objekty BLOB se při exportu dat protokolu vytvoří podle potřeby.

   ![Konfigurace shromažďování dat](./media/search-monitor-usage/configure-storage.png "Konfigurace shromažďování dat")

1. Nastavení uložte.

1. Otestujte vytvořením nebo odstraněním objektů (vytvoří události protokolu) a odesláním dotazů (generuje metriky). 

V úložišti objektů BLOB se kontejnery vytvářejí jenom v případě, že existuje aktivita pro protokolování nebo měření. Když se data zkopírují do účtu úložiště, data se naformátují jako JSON a umístí se do dvou kontejnerů:

* insights – protokoly operationlogs: pro protokoly přenosů služby search
* insights-metrics-pt1m: pro metriky

**Trvá to jednu hodinu, než se kontejnery zobrazí v úložišti objektů BLOB. Jeden objekt BLOB se za hodinu vychází z každého kontejneru.**

Protokoly se archivují každou hodinu, během které dojde k aktivitě. Následující cesta je příkladem jednoho souboru protokolu vytvořeného v lednu 12 2020 v 9:00 ráno. kde každý `/` je složka: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2020/m=01/d=12/h=09/m=00/name=PT1H.json`

## <a name="log-schema"></a>Schéma protokolu

Datové struktury, které obsahují data protokolu Azure Kognitivní hledání, odpovídají schématu níže. 

Pro úložiště objektů BLOB má každý objekt BLOB jeden kořenový objekt nazvaný **záznamy** obsahující pole objektů log. Každý objekt BLOB obsahuje záznamy pro všechny operace, které byly provedeny během stejné hodiny.

Následující tabulka uvádí částečný seznam polí společných pro diagnostické protokolování.

| Název | Typ | Příklad | Poznámky |
| --- | --- | --- | --- |
| timeGenerated |datetime |"2018-12-07T00:00:43.6872559Z" |Časové razítko operace |
| resourceId |řetězec |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>VÝCHOZÍ/RESOURCEGROUPS/POSKYTOVATELE /<br/> SPOLEČNOSTI MICROSOFT. HLEDÁNÍ/SEARCHSERVICES/SEARCHSERVICE" |Vaše ID prostředku |
| operationName |řetězec |"Query.Search" |Název operace |
| operationVersion |řetězec |"2019-05-06" |Používá rozhraní api-version |
| category |řetězec |"OperationLogs" |Konstanty |
| resultType |řetězec |"Success" |Možné hodnoty: úspěch nebo neúspěch |
| resultSignature |int |200 |Kód výsledku protokolu HTTP |
| doby trvání v MS |int |50 |Doba trvání operace v milisekundách |
| Vlastnosti |object |v následující tabulce najdete |Objekt obsahující data specifická pro operace |

### <a name="properties-schema"></a>Schéma vlastností

Níže uvedené vlastnosti jsou specifické pro Azure Kognitivní hledání.

| Název | Typ | Příklad | Poznámky |
| --- | --- | --- | --- |
| Description_s |řetězec |"GET /indexes('content')/docs" |Operace koncového bodu |
| Documents_d |int |42 |Počet zpracovaných dokumentů |
| IndexName_s |řetězec |"test-index" |Název přidružený k operaci indexu |
| Query_s |řetězec |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Parametry dotazu |

## <a name="metrics-schema"></a>Schématu metrik

Metriky jsou zachyceny pro požadavky na dotazy a měřené v jednom minutovém intervalu. Každý metrika uvádí minimální, maximální a průměrné hodnoty za minutu. Další informace najdete v tématu [monitorování požadavků na dotazy](search-monitor-queries.md).

| Název | Typ | Příklad | Poznámky |
| --- | --- | --- | --- |
| resourceId |řetězec |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>VÝCHOZÍ/RESOURCEGROUPS/POSKYTOVATELE /<br/>SPOLEČNOSTI MICROSOFT. HLEDÁNÍ/SEARCHSERVICES/SEARCHSERVICE" |ID prostředku |
| metricName |řetězec |"Latence" |Název metriky |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |časové razítko operace |
| průměr |int |64 |Průměrná hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotkách v sekundách nebo procentech v závislosti na metrikě. |
| minimum |int |37 |Minimální hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotek v sekundách. |
| maximum |int |78 |Maximální hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotek v sekundách.  |
| celkem |int |258 |Celková hodnota nezpracovaných vzorků v časovém intervalu metriky, jednotek v sekundách.  |
| count |int |4 |Počet metrik vygenerovaných z uzlu do protokolu v intervalu 1 minuty.  |
| timegrain |řetězec |"PT1M" |Časový interval metriky v normě ISO 8601. |

Je běžné, že dotazy se mají spouštět v milisekundách, takže se v metrikě jako QPS zobrazí jenom dotazy, které měří jako sekunda.

U **vyhledávacích dotazů za sekundu** metrika minimální je nejnižší hodnota pro vyhledávací dotazy za sekundu, které byly zaregistrovány během dané minuty. Totéž platí i pro maximální hodnotu. Průměr je agregace napříč celou minutu. Například během jedné minuty můžete mít podobný vzor: jedna sekunda vysokého zatížení, která je maximální hodnota pro SearchQueriesPerSecond, následovaná 58 sekundami průměrného zatížení a nakonec jedna sekunda pouze jedním dotazem, což je minimální hodnota.

U **omezených vyhledávacích dotazů procento**, minimum, maximum, průměr a součet musí mít všechny stejnou hodnotu: procento vyhledávacích dotazů, které byly omezeny, z celkového počtu vyhledávacích dotazů během jedné minuty.

## <a name="view-log-files"></a>Zobrazit soubory protokolu

Úložiště objektů BLOB se používá k archivaci souborů protokolu. K zobrazení souboru protokolu můžete použít libovolný editor JSON. Pokud ho nemáte, doporučujeme [Visual Studio Code](https://code.visualstudio.com/download).

1. V Azure Portal otevřete svůj účet úložiště. 

2. V levém navigačním podokně klikněte na **objekty blob**. Měli byste vidět **Insights-logs-operationlogs** a **Insights – metriky – pt1m**. Tyto kontejnery vytvoří služba Azure Kognitivní hledání, když se data protokolu exportují do úložiště objektů BLOB.

3. Klikněte na hierarchii složek dolů, dokud nedosáhnete souboru. JSON.  K stažení souboru použijte kontextovou nabídku.

Po stažení souboru ho otevřete v editoru JSON, abyste mohli zobrazit jeho obsah.

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, Projděte si základní informace o monitorování služby Search, kde se dozvíte o plném rozsahu funkcí dohledu.

> [!div class="nextstepaction"]
> [Monitorování operací a aktivit v Azure Kognitivní hledání](search-monitor-usage.md)