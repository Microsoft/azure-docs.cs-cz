---
title: Ingestování dat v Azure Průzkumník dat
description: Seznamte se s různými způsoby, jak můžete ingestovat (načítat) data v Azure Průzkumník dat
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 4846a19c403cce16bed704ed4e7c70499f3b5d13
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187981"
---
# <a name="azure-data-explorer-data-ingestion"></a>Ingestování dat v Azure Průzkumník dat

Ingestování dat je proces, který se používá k načtení záznamů dat z jednoho nebo více zdrojů k vytvoření nebo aktualizaci tabulky v Azure Průzkumník dat. Po ingestování budou data dostupná pro dotaz. Následující diagram ukazuje kompletní tok pro práci v Azure Průzkumník dat, včetně přijímání dat.

![Tok dat](media/ingest-data-overview/data-flow.png)

Služba správy dat Azure Průzkumník dat, která zodpovídá za příjem dat, nabízí tyto funkce:

1. **Vyžádání dat**: vyžádání dat z externích zdrojů (Event Hubs) nebo čtení žádostí o přijetí změn z fronty Azure.

1. **Dávkování**: dávkové zpracování dat do stejné databáze a tabulky pro optimalizaci propustnosti přijímání.

1. **Ověření**: v případě potřeby předběžné ověření a převod formátu.

1. **Manipulace s daty**: porovnání schématu, uspořádání, indexování, kódování a komprimace dat.

1. **Bod trvalosti v toku**příjmu: umožňuje spravovat přijímání příjmu na stroji a zpracovávat opakované pokusy po přechodných chybách.

1. **Potvrďte příjem dat**: zpřístupňuje data pro dotaz.

## <a name="ingestion-methods"></a>Metody přijímání

Azure Průzkumník dat podporuje několik metod příjmu, každý s vlastními cílovými scénáři, výhodami a nevýhodami. Azure Průzkumník dat nabízí kanály a konektory pro běžné služby, programové přijímání pomocí sad SDK a přímý přístup k modulu pro účely průzkumu.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Přijímání zpráv pomocí kanálů, konektorů a modulů plug-in

Azure Průzkumník dat v současné době podporuje:

* Kanál Event Grid, který se dá spravovat pomocí Průvodce správou v Azure Portal. Další informace najdete v tématu ingestování [objektů blob Azure do Azure Průzkumník dat](ingest-data-event-grid.md).

* Kanál centra událostí, který se dá spravovat pomocí Průvodce správou v Azure Portal. Další informace najdete v tématu ingestování [dat z centra událostí do Azure Průzkumník dat](ingest-data-event-hub.md).

* Modul plug-in Logstash najdete v tématu ingestování [dat z Logstash do Azure Průzkumník dat](ingest-data-logstash.md).

* Konektor Kafka, přečtěte si téma ingestování [dat z Kafka do Azure Průzkumník dat](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Ingestování pomocí integračních služeb

* Azure Data Factory (ADF), plně spravovaná služba pro integraci dat pro analytické úlohy v Azure, ke kopírování dat do a z Azure Průzkumník dat pomocí [podporovaných úložišť a formátů dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats). Další informace najdete v tématu [kopírování dat z Azure Data Factory do Azure Průzkumník dat](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Programové přijímání

Azure Průzkumník dat poskytuje sady SDK, které se dají použít k dotazování a přijímání dat. Programová příjemce je optimalizována pro snížení nákladů na příjem příjmu (COGs) tím, že minimalizuje transakce úložiště během procesu příjmu a po něm.

**Dostupné sady SDK a open source projekty**:

Kusto nabízí klientskou sadu SDK, která se dá použít k ingestování a dotazování dat pomocí:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Sada SDK pro Node](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Techniky pro programové přijímání**:

* Ingestování dat prostřednictvím služby Azure Průzkumník dat Data Management (vysoce propustnost a spolehlivé ingestování):

    Ingestování [**dávky**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (poskytované sadou SDK): klient nahraje data do úložiště objektů BLOB v Azure (určené službou správy dat Azure Průzkumník dat) a odešle oznámení do fronty Azure. Pro zajištění vysokého objemu, spolehlivého a levného příjmu dat se doporučuje postupovat v dávce.

* Ingestování dat přímo do modulu Azure Průzkumník dat (nejvhodnější pro zkoumání a vytváření prototypů):

  * Ingestování: řídicí příkaz (ingestní **inline)** , který obsahuje data v pásmu, je určený pro účely testování ad hoc.

  * Ingestování **z dotazu**: řídicí příkaz (. set,. set-nebo-Append,. set-nebo-nahrazování), který odkazuje na výsledky dotazu, se používá pro generování sestav nebo malých dočasných tabulek.

  * Ingestování **z úložiště**: řídicí příkaz (. ingestovat do) s daty uloženými externě (například Azure Blob Storage) umožňuje efektivní hromadné přijímání dat.

**Latence různých metod**:

| Metoda | Latence |
| --- | --- |
| **Ingestování vloženého textu** | Okamžité |
| **Ingestování z dotazu** | Doba zpracování dotazu + |
| **Ingestování z úložiště** | Doba stahování a doba zpracování |
| **Přijímání zpráv do fronty** | Čas dávky a doba zpracování |
| |

Doba zpracování závisí na velikosti dat, a to méně než několik sekund. Výchozí doba dávky je 5 minut.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Výběr nejvhodnější metody ingestování

Než začnete ingestovat data, měli byste se zeptat na následující otázky.

* Kde se nacházejí moje data? 
* Jaký je formát dat a je možné ho změnit? 
* Jaká jsou požadovaná pole k dotazování? 
* Jaký je očekávaný objem dat a rychlost? 
* Kolik typů událostí se očekává (odráží se jako počet tabulek)? 
* Jak často se očekává, že se má změnit schéma událostí? 
* Kolik uzlů bude generovat data? 
* Jaký je zdrojový operační systém? 
* Jaké jsou požadavky na latenci? 
* Může se použít jeden ze stávajících kanálů spravovaného příjmu? 

Pro organizace s existující infrastrukturou, která je založená na službě zasílání zpráv, jako je centrum událostí a IoT Hub, je pravděpodobně nejvhodnějším řešením použití konektoru. Přijímání zpráv do fronty je vhodné pro velké objemy dat.

## <a name="supported-data-formats"></a>Podporované formáty dat

Pro všechny metody příjmu kromě ingestování z dotazu naformátujte data tak, aby je mohla Azure Průzkumník dat analyzovat. 
* Podporovány jsou formáty dat: TXT, CSV, TSV, TSVE, PSV, SCSV, SOH, JSON (oddělený čárkami, víceřádkové), Avro, orc a Parquet. 
* Podporuje kompresi ZIP a GZIP.

> [!NOTE]
> Při ingestování dat jsou datové typy odvozeny na základě sloupců cílové tabulky. Pokud je záznam neúplný nebo nelze analyzovat pole jako požadovaný datový typ, budou odpovídající sloupce tabulky naplněny hodnotami null.

## <a name="ingestion-recommendations-and-limitations"></a>Doporučení a omezení přijímání příjmu

* Efektivní zásady uchovávání dat ingestně se odvozují ze zásad uchovávání databáze. Podrobnosti najdete v tématu [zásady uchovávání informací](/azure/kusto/concepts/retentionpolicy) . Ingestování dat vyžaduje pro ingestování **tabulek** nebo pro ingestování **databází** oprávnění.
* Ingestování podporuje maximální velikost souboru 5 GB. Doporučením je ingestování souborů mezi 100 MB a 1 GB.

## <a name="schema-mapping"></a>Mapování schématu

Mapování schématu pomáhá navazovat zdrojová datová pole na sloupce cílové tabulky.

* [Mapování CSV](/azure/kusto/management/mappings?branch=master#csv-mapping) (volitelné) funguje se všemi ordinálními formáty. Dá se udělat pomocí parametru příkazu ingestovat nebo [předem vytvořit v tabulce](/azure/kusto/management/create-ingestion-mapping-command) a odkazovat z parametru příkazu ingestování.
* [Mapování JSON](/azure/kusto/management/mappings?branch=master#json-mapping) (povinné) a [mapování Avro](/azure/kusto/management/mappings?branch=master#avro-mapping) (povinné) je možné provést pomocí parametru příkazového řádku ingestování. Lze je také [předem vytvořit v tabulce](/azure/kusto/management/create-ingestion-mapping-command) a odkazovat z parametru příkazu ingest.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ingestování dat z centra událostí do Azure Průzkumník dat](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Ingestování dat pomocí Event Grid předplatného do Azure Průzkumník dat](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Ingestování dat z Kafka do Azure Průzkumník dat](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Ingestování dat pomocí knihovny Pythonu v Azure Průzkumník dat](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingestování dat pomocí knihovny uzlů Azure Průzkumník dat](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingestování dat pomocí sady Azure Průzkumník dat .NET Standard SDK (Preview)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingestování dat z Logstash do Azure Průzkumník dat](ingest-data-logstash.md)
