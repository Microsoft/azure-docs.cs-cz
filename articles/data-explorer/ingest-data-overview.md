---
title: Příjem dat Azure Průzkumník dat
description: Seznamte se s různými způsoby, které dokáží ingestovat data (načíst) v Průzkumníku dat Azure
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2019
ms.openlocfilehash: 8d5fc1c579fd09f1a71d63dce4d1673ef5a8652b
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354616"
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

### <a name="ingestion-using-pipelines"></a>Ingestování pomocí kanálů

Průzkumník dat Azure aktuálně podporuje kanálu centra událostí, které je možné spravovat pomocí Průvodce správou na webu Azure Portal. Další informace najdete v tématu [rychlý start: Ingestovat data z centra událostí do Průzkumníku dat Azure](ingest-data-event-hub.md).

### <a name="ingestion-using-connectors-and-plugins"></a>Ingestování pomocí konektorů a moduly plug-in

* Průzkumník služby Azure Data podporuje modul plug-in. Další informace najdete v tématu [modul plug-in pro Logstash výstup pro Průzkumníka služby Azure Data](https://github.com/Azure/logstash-output-kusto/blob/master/README.md).

* Průzkumník služby Azure Data podporuje konektor systému Kafka. Další informace najdete v tématu [rychlý start: Ingestování dat z Kafka do Průzkumníku dat Azure](ingest-data-kafka.md)

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
* [Mapování JSON](/azure/kusto/management/mappings?branch=master#json-mapping) (povinné) a [Avro mapování](/azure/kusto/management/mappings?branch=master#avro-mapping) (povinné) je možné provádět pomocí ingestování parametr příkazu nebo [předem vytvořit v tabulce](/azure/kusto/management/tables#create-ingestion-mapping) a odkazované z příkazu ingestu parametr.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Ingestovat data z centra událostí do Průzkumníku dat Azure](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Ingestování dat z Kafka do Průzkumníku dat Azure](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Ingestování dat pomocí knihovny Python Průzkumník dat Azure](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Ingestování dat pomocí knihovny Azure uzlu Průzkumníka dat](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Příjem dat s využitím dat Explorer .NET Standard SDK služby Azure (Preview)](net-standard-ingest-data.md)
