---
title: Příjem dat Azure Průzkumník dat
description: Seznamte se s různými způsoby, které dokáží ingestovat data (načíst) v Průzkumníku dat Azure
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 2/18/2019
ms.openlocfilehash: 4fd0f0990163963fc0cc3c7caf221609da487909
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340174"
---
# <a name="azure-data-explorer-data-ingestion"></a>Příjem dat Azure Průzkumník dat

Příjem dat je proces používaný k načtení záznamů dat z jednoho nebo více zdrojů k vytvoření nebo aktualizaci tabulky v Průzkumníku dat Azure. Jakmile ingestuje, budou data k dispozici pro dotaz. Následující diagram znázorňuje tok začátku do konce pro práci v Průzkumníku dat Azure, včetně ingestování.

![Tok dat](media/ingest-data-overview/data-flow.png)

Průzkumník dat Azure služba správy dat, která zodpovídá za příjem dat, poskytuje následující funkce:

1. **Načtení dat**: Načítání dat z externích zdrojů (Event Hubs) nebo přijímání požadavků na čtení z fronty služby Azure.

1. **Dávkování**: Batch dat odesílaných do stejné databáze a tabulky umožňuje optimalizovat propustnost příjmu.

1. **Ověření**: Předběžné ověření a formát převodu v případě potřeby.

1. **Manipulace s daty**: Odpovídající schéma, uspořádání, indexování, kódování a komprese dat.

1. **Trvalost bodu ve službě flow ingestování**: Správa zatížení příjmu na modulu a zpracovat opakování při přechodném selhání.

1. **Potvrzení ingestovat data**: Zpřístupní data pro dotaz.

## <a name="ingestion-methods"></a>Ingestování metody

Průzkumník služby Azure Data podporuje několik metod pro ingestování, každý s vlastní cílové scénáře, výhody a nevýhody. Průzkumník dat Azure nabízí kanály a konektorů k běžným službám, programové ingestování pomocí sady SDK a přímý přístup k modulu pro účely zkoumání.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Ingestování pomocí kanálů, konektory a moduly plug-in

Průzkumník dat Azure aktuálně podporuje:

* Event Grid kanál, který je možné spravovat pomocí Průvodce správou na webu Azure Portal. Další informace najdete v tématu [Ingestování objektů BLOB Azure do Průzkumníku dat Azure](ingest-data-event-grid.md).

* Kanál centra událostí, který je možné spravovat pomocí Průvodce správou na webu Azure Portal. Další informace najdete v tématu [Ingestovat data z centra událostí do Průzkumníku dat Azure](ingest-data-event-hub.md).

* Modul plug-in pro Logstash, naleznete v tématu [Ingestovat data z Logstash do Průzkumníku dat Azure](ingest-data-logstash.md).

* Konektor Kafka, naleznete v tématu [Ingestovat data z platformy Kafka do Průzkumníku dat Azure](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Ingestování pomocí integračních služeb

* Azure Data Factory (ADF), služba pro integraci plně spravovaná data pro analytické úlohy v Azure ke zkopírování dat do a z Průzkumníku dat Azure. Další informace najdete v tématu [kopírování dat do nebo z Průzkumníku dat Azure pomocí Azure Data Factory](/azure/data-factory/connector-azure-data-explorer).

### <a name="programmatic-ingestion"></a>Ingestování prostřednictvím kódu programu

Průzkumník služby Azure Data poskytuje sady SDK, které lze použít pro příjem dotazy a data. Programové ingestování je optimalizována pro snížení nákladů na ingestování (náklady na prodané zboží), minimalizací transakce služby storage během a po ingestování procesu.

**Dostupné sady SDK a open source projektů**:

Kusto nabízí klientské sady SDK, který slouží k ingestování a dotazování dat pomocí služby:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programové ingestování techniky**:

* Příjem dat prostřednictvím služby správy dat Průzkumník dat Azure (vysokou propustnost a spolehlivé ingestování):

    [**Batch ingestování** ](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (k dispozici prostřednictvím sady SDK): klient odešle data do úložiště objektů Blob v Azure (určené služba pro správu dat Průzkumník dat Azure) a odešle oznámení do fronty služby Azure. Ingestování batch je doporučený postup pro velké objemy spolehlivé a levné ingestování.

* Příjem dat přímo do modulu Průzkumník dat Azure (nejvhodnější pro zkoumání a vytváření prototypů):

  * **Vložené ingestování**: příkaz ovládacího prvku (.ingest vložené) obsahující data integrovaná je určen pro účely testování ad hoc.

  * **Ingestování z dotazu**: příkaz ovládacího prvku (.set, set nebo připojit, set nebo nahradit), který odkazuje na výsledky dotazu se používá pro generování sestav nebo malé dočasné tabulky.

  * **Ingestování ze služby storage**: umožňuje efektivní hromadné ingestování dat příkazu ovládacího prvku (.ingest do) se data uložených externě (například Azure Blob Storage).

**Latence různých metod**:

| Metoda | Latence |
| --- | --- |
| **Vložené příjmu** | Okamžitý |
| **Ingestování z dotazu** | Doba dotazu + doba zpracování |
| **Ingestování ze služby storage** | Doba stahování + doba zpracování |
| **Ingestování zařazených do fronty** | Dávkování čas + doba zpracování |
| |

Doba zpracování závisí na množství dat kratší než několik sekund. Dávkování čas výchozí hodnota je 5 minut.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Výběr nejvhodnější ingestování – metoda

Než začnete ingestovat data, by měl sami odpovědět na tyto otázky.

* Kde data nacházejí? 
* Co je formát data a můžete ho změnit? 
* Co jsou povinná pole, aby se dalo dotazovat? 
* Co je očekávaný datový svazek a rychlost? 
* Kolik typy událostí se očekává, že (projeví jako několik tabulek)? 
* Jak často se očekává schématu události změnit? 
* Kolik uzlů bude generovat data? 
* Co je zdrojového operačního systému? 
* Jaké jsou požadavky na latenci? 
* Jeden z existujícího spravovaného ingestování kanály slouží? 

Pro organizace se stávající infrastrukturou, které jsou založené na službě zasílání zpráv, jako je Centrum událostí pomocí konektoru je pravděpodobně nejlepším řešením. Je vhodná pro velkých objemů dat ve frontě ingestování.

## <a name="supported-data-formats"></a>Podporované formáty dat

Za účelem ingestování datových všechny metody jiné než ingestování z dotazu, formát dat tak, aby můžete ji parsovat Průzkumník dat Azure. Formáty dat podporovaných jsou:

* CSV, TSV, PSV, SCSV, SOH
* Avro (oddělených čárkami řádku a více řádků), JSON
* ZIP a GZIP 

> [!NOTE]
> Když se data ingestují, datové typy jsou odvozeny podle sloupce cílové tabulky. Pokud záznam je neúplný nebo pole nelze analyzovat jako povinný datový typ, naplní se odpovídající sloupce tabulky s hodnotou null.

## <a name="ingestion-recommendations-and-limitations"></a>Příjem doporučení a omezení

* Zásady uchovávání informací efektivní přijatých dat je odvozen z zásady uchovávání informací databáze. Zobrazit [zásady uchovávání informací](/azure/kusto/concepts/retentionpolicy) podrobnosti. Příjem dat vyžaduje **tabulky přijímač** nebo **databáze přijímač** oprávnění.
* Ingestování podporuje maximální velikostí 5 GB. Doporučuje se ingestování souborů 100 MB až 1 GB.

## <a name="schema-mapping"></a>mapování schématu

Mapování schématu pomáhá vytvořit vazbu pole zdroje dat na sloupce cílové tabulky.

* [Mapování sdíleného svazku clusteru](/azure/kusto/management/mappings?branch=master#csv-mapping) (volitelné) funguje s všechny formáty podle pořadí. Je možné provádět, pomocí parametru příkazu ingestování nebo [předem vytvořit v tabulce](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) a odkazované z parametru příkazu ingestování.
* [Mapování JSON](/azure/kusto/management/mappings?branch=master#json-mapping) (povinné) a [Avro mapování](/azure/kusto/management/mappings?branch=master#avro-mapping) (povinné) je možné provádět pomocí parametr příkazu ingestování. Je také možné [předem vytvořit v tabulce](/azure/kusto/management/tables#create-ingestion-mapping) a odkazované z parametru příkazu ingestování.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ingestovat data z centra událostí do Průzkumníku dat Azure](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Příjem dat s využitím odběr služby Event Grid do Průzkumníku dat Azure](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Ingestování dat z Kafka do Průzkumníku dat Azure](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Ingestování dat pomocí knihovny Python Průzkumník dat Azure](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingestování dat pomocí knihovny Azure uzlu Průzkumníka dat](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Příjem dat s využitím dat Explorer .NET Standard SDK služby Azure (Preview)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingestování dat z Logstash do Průzkumníku dat Azure](ingest-data-logstash.md)
