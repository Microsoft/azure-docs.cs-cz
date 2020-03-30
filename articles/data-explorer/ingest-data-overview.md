---
title: Ingestování dat V Azure Data Exploreru
description: Informace o různých způsobech ingestování (načítání) dat v Azure Data Exploreru
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 4846a19c403cce16bed704ed4e7c70499f3b5d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246393"
---
# <a name="azure-data-explorer-data-ingestion"></a>Ingestování dat V Azure Data Exploreru

Při přijímání dat se používá proces, který se používá k načtení datových záznamů z jednoho nebo více zdrojů k vytvoření nebo aktualizaci tabulky v Průzkumníku dat Azure. Po požití budou data k dispozici pro dotaz. Následující diagram znázorňuje tok od konce pro práci v Průzkumníku dat Azure, včetně ingestování dat.

![Tok dat](media/ingest-data-overview/data-flow.png)

Služba správy dat Azure Data Explorer, která je zodpovědná za ingestování dat, poskytuje následující funkce:

1. **Vyžádat si**: Vyžádat data z externích zdrojů (Event Hubs) nebo číst požadavky na ingestování z fronty Azure.

1. **Dávkování**: Dávková data toku do stejné databáze a tabulky pro optimalizaci propustnost ingestování.

1. **Ověření**: Předběžné ověření a převod formátu v případě potřeby.

1. **Manipulace s daty**: Párování schématu, uspořádání, indexování, kódování a komprese dat.

1. **Bod perzistence v toku ingestování**: Správa zatížení přijím zatížení motoru a zpracování opakování při přechodných poruchách.

1. **Potvrzení ingestování dat**: Zpřístupní data pro dotaz.

## <a name="ingestion-methods"></a>Metody požití

Azure Data Explorer podporuje několik metod ingestování, každý s vlastní cílové scénáře, výhody a nevýhody. Azure Data Explorer nabízí kanály a konektory pro běžné služby, programové ingestování pomocí sad SDK a přímý přístup k modulu pro účely průzkumu.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Požití pomocí kanálů, konektorů a pluginů

Azure Data Explorer aktuálně podporuje:

* Kanál Grid událostí, který lze spravovat pomocí průvodce správou na webu Azure Portal. Další informace najdete [v tématu Ingestestování objektů BLOB Azure do Průzkumníka dat Azure](ingest-data-event-grid.md).

* Kanál Centra událostí, který lze spravovat pomocí průvodce správou na webu Azure Portal. Další informace najdete v tématu [Ingestování dat z Centra událostí do Průzkumníka dat Azure](ingest-data-event-hub.md).

* Logstash plugin, viz [Ingestování dat z Logstash do Průzkumníka dat Azure](ingest-data-logstash.md).

* Kafka konektor, viz [Ingestování dat z Kafka do Průzkumníka dat Azure](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Požití pomocí integračních služeb

* Azure Data Factory (ADF), plně spravovaná služba integrace dat pro analytické úlohy v Azure, ke kopírování dat do a z Azure Data Explorerpomocí [podporovaných úložišť dat a formátů](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats). Další informace najdete [v tématu Kopírování dat z Azure Data Factory do Průzkumníka dat Azure](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Programové požití

Azure Data Explorer poskytuje sady SDK, které se dá použít pro dotazování a přijím dat. Programové ingestování je optimalizováno pro snížení nákladů na ingestování (COGs) minimalizací transakcí úložiště během a po procesu ingestování.

**Dostupné sady SDK a projekty s otevřeným zdrojovým kódem**:

Kusto nabízí klientské sady SDK, které lze použít k ingestování a dotazování dat pomocí:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [Sada SDK rozhraní .NET](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [ROZHRANÍ API PRO ODPOČINEK](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programové techniky požití**:

* Ingestování dat prostřednictvím služby Azure Data Explorer pro správu dat (vysoká propustnost a spolehlivé ingestování):

    [**Dávkové ingestování**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (poskytované sadou SDK): klient nahraje data do úložiště objektů Blob Azure (určené službou Azure Data Explorer pro správu dat) a odešle oznámení do fronty Azure. Dávkové požití je doporučená technika pro velkoobjemové, spolehlivé a levné požití dat.

* Ingestování dat přímo do modulu Průzkumníka dat Azure (nejvhodnější pro zkoumání a vytváření prototypů):

  * **Inline ingestování**: příkaz řízení (.ingest inline) obsahující in-band data je určena pro účely testování ad hoc.

  * **Ingestovat z dotazu:** příkaz ovládacího prvku (.set, .set-or-append, .set-or-replace), který odkazuje na výsledky dotazu se používá pro generování sestav nebo malé dočasné tabulky.

  * **Ingestování z úložiště:** příkaz řízení (.ingest do) s daty uloženými externě (například Azure Blob Storage) umožňuje efektivní hromadné ingestování dat.

**Latence různých metod**:

| Metoda | Latence |
| --- | --- |
| **Inline požití** | Okamžité |
| **Ingestování z dotazu** | Čas dotazu + doba zpracování |
| **Ingestování z úložiště** | Doba stahování + doba zpracování |
| **Požití ve frontě** | Doba dávkování + doba zpracování |
| |

Doba zpracování závisí na velikosti dat, méně než několik sekund. Batching čas výchozí 5 minut.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Výběr nejvhodnější metody požití

Než začnete ingestovat data, měli byste si položit následující otázky.

* Kde se nacházejí moje data? 
* Jaký je formát dat a lze jej změnit? 
* Jaká jsou požadovaná pole, která mají být dotazována? 
* Jaký je očekávaný objem dat a rychlost? 
* Kolik typů událostí se očekává (odráží se jako počet tabulek)? 
* Jak často se má schéma události měnit? 
* Kolik uzlů bude generovat data? 
* Co je zdrojový operační operační tým? 
* Jaké jsou požadavky na latenci? 
* Lze použít jeden ze stávajících kanálů spravovaného ingestování? 

Pro organizace s existující infrastrukturou, které jsou založeny na službě zasílání zpráv, jako je Event Hub a IoT Hub, je použití konektoru pravděpodobně nejvhodnějším řešením. Ingestování ve frontě je vhodné pro velké svazky dat.

## <a name="supported-data-formats"></a>Podporované formáty dat

Pro všechny metody ingestování než ingestování z dotazu naformátujte data tak, aby je Azure Data Explorer mohl analyzovat. 
* Podporované datové formáty jsou: TXT, CSV, TSV, TSVE, PSV, SCSV, SOH, JSON (line-separated, multi-line), Avro, Orc a Parquet. 
* Podporuje kompresi ZIP a GZIP.

> [!NOTE]
> Při ingestování dat jsou datové typy odvozeny na základě sloupců cílové tabulky. Pokud je záznam neúplný nebo pole nelze analyzovat jako požadovaný datový typ, budou odpovídající sloupce tabulky naplněny nulovými hodnotami.

## <a name="ingestion-recommendations-and-limitations"></a>Doporučení a omezení pro požití

* Efektivní zásady uchovávání ingeminovaných dat je odvozen ze zásad uchovávání informací databáze. Podrobnosti naleznete v [zásadách uchovávání informací.](/azure/kusto/concepts/retentionpolicy) Ingestování dat vyžaduje **tabulka ingestor** nebo **database ingestor** oprávnění.
* Ingestování podporuje maximální velikost souboru 5 GB. Doporučujeme ingestovat soubory mezi 100 MB a 1 GB.

## <a name="schema-mapping"></a>Mapování schématu

Mapování schématu pomáhá svázat zdrojová datová pole se sloupci cílové tabulky.

* [Mapování CSV](/azure/kusto/management/mappings?branch=master#csv-mapping) (volitelné) funguje se všemi formáty řadového zařízení. Lze jej provést pomocí parametru příkazu ingest nebo [předem vytvořeného v tabulce](/azure/kusto/management/create-ingestion-mapping-command) a odkazovaného z parametru příkazu ingest.
* [Mapování JSON](/azure/kusto/management/mappings?branch=master#json-mapping) (povinné) a [Avro mapování](/azure/kusto/management/mappings?branch=master#avro-mapping) (povinné) lze provádět pomocí parametru příkazu ingest. Mohou být také [předem vytvořeny v tabulce](/azure/kusto/management/create-ingestion-mapping-command) a odkazovány z parametru příkazu ingest.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ingestování dat z Centra událostí do Průzkumníka dat Azure](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Ingestování dat pomocí předplatného Event Grid do Průzkumníka dat Azure](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Ingestování dat z Kafky do Průzkumníka dat Azure](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Ingestování dat pomocí knihovny Pythonu Průzkumníka dat Azure](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingestování dat pomocí knihovny uzlů Průzkumníka dat Azure](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingestování dat pomocí azure data exploreru .NET Standard SDK (Preview)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingestování dat z Logstash do Průzkumníka dat Azure](ingest-data-logstash.md)
