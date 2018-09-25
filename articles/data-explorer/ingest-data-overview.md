---
title: Příjem dat Azure Průzkumník dat
description: Seznamte se s různými způsoby, které dokáží ingestovat data (načíst) v Průzkumníku dat Azure
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 94f96d949f2a05f71e9565fdcbc7b48ed2c2a5c5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972654"
---
# <a name="azure-data-explorer-data-ingestion"></a>Příjem dat Azure Průzkumník dat

Příjem dat je proces používaný k načtení záznamů dat z jednoho nebo více zdrojů k vytvoření nebo aktualizaci tabulky v Průzkumníku dat Azure. Jakmile ingestuje, budou data k dispozici pro dotaz. Následující diagram znázorňuje tok začátku do konce pro práci v Průzkumníku dat Azure, včetně ingestování **(2)**.

![Celkové datový tok](media/ingest-data-overview/overall-data-flow.png)

Průzkumník dat Azure služba správy dat, která zodpovídá za příjem dat, poskytuje následující funkce:

1. **Načtení dat**: načítání dat z externích zdrojů (Event Hubs) nebo přijímání požadavků na čtení z fronty služby Azure.

1. **Dávkování**: dávky dat odesílaných do stejné databáze a tabulky umožňuje optimalizovat propustnost příjmu.

1. **Ověření**: předběžné ověření a formát převodu podle potřeby.

1. **Manipulace s daty**: odpovídající schéma, uspořádání, indexování, kódování a komprese dat.

1. **Trvalost bodu ve službě flow ingestování**: Správa zatížení příjmu na modul a popisovač opakování při přechodném selhání.

1. **Potvrzení ingestovat data**: zpřístupní data pro dotaz.

> [!NOTE]
> Zásady uchovávání informací efektivní přijatých dat je odvozen z zásady uchovávání informací databáze. Zobrazit [zásady uchovávání informací](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy) podrobnosti. Příjem dat vyžaduje **tabulky přijímač** nebo **databáze přijímač** oprávnění.

## <a name="ingestion-methods"></a>Ingestování metody

Průzkumník služby Azure Data podporuje několik metod pro ingestování, každý s vlastní cílové scénáře, výhody a nevýhody. Průzkumník dat Azure nabízí konektorů k běžným službám, programové ingestování pomocí sady SDK a přímý přístup k modulu pro účely zkoumání.

### <a name="ingestion-using-connectors"></a>Ingestování pomocí konektorů

Průzkumník dat Azure aktuálně podporuje konektoru centra událostí, které je možné spravovat pomocí Průvodce správou na webu Azure Portal. Další informace najdete v tématu [rychlý start: Ingestovat data z centra událostí do Průzkumníku dat Azure](ingest-data-event-hub.md).

### <a name="programmatic-ingestion"></a>Ingestování prostřednictvím kódu programu

Průzkumník služby Azure Data poskytuje sady SDK, které lze použít pro příjem dotazy a data. Programové ingestování je optimalizována pro snížení nákladů na ingestování (náklady na prodané zboží), minimalizací transakce služby storage během a po ingestování procesu.

**Dostupné sady SDK a open source projektů**:

Kusto nabízí klientské sady SDK, který slouží k ingestování a dotazování dat pomocí služby:

* [Python SDK](https://docs.microsoft.com/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](https://docs.microsoft.com/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](https://docs.microsoft.com/azure/kusto/api/java/kusto-java-client-library)

* [REST API](https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programové ingestování techniky**:

* Příjem dat přímo do modulu Průzkumník dat Azure (nejvhodnější pro zkoumání a vytváření prototypů):

  * **Vložené ingestování**: příkaz ovládacího prvku (.ingest vložené) obsahující data integrovaná je určen pro účely testování ad hoc.

  * **Ingestování z dotazu**: příkaz ovládacího prvku (.set, set nebo připojit, set nebo nahradit), který odkazuje na výsledky dotazu se používá pro generování sestav nebo malé dočasné tabulky.

  * **Ingestování ze služby storage**: umožňuje efektivní hromadné ingestování dat příkazu ovládacího prvku (.ingest do) se data uložených externě (například Azure Blob Storage).

* Příjem dat prostřednictvím služby správy dat Průzkumník dat Azure (vysokou propustnost a spolehlivé ingestování)

  * [**Batch ingestování** ](https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (k dispozici prostřednictvím sady SDK): klient odešle data do úložiště objektů Blob v Azure (určené služba pro správu dat Průzkumník dat Azure) a odešle oznámení do fronty služby Azure. Toto je doporučený postup pro velké objemy spolehlivé a levné ingestování.

**Latence různých metod**:

| Metoda | Latence |
| --- | --- |
| **Vložené příjmu** | Okamžitý |
| **Ingestování z dotazu** | Doba dotazu + doba zpracování |
| **Ingestování ze služby storage** | Doba stahování + doba zpracování |
| **Ingestování zařazených do fronty** | Dávkování čas + doba zpracování |
| |

Doba zpracování závisí na velikosti dat, obvykle méně než několik sekund. Dávkování čas výchozí hodnota je 5 minut.

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

Pro organizace s existující infrastruktuře založené na službě zasílání zpráv, jako je centra událostí pomocí konektoru se pravděpodobně nejvhodnější řešení. Je vhodná pro velkých objemů dat ve frontě ingestování.

## <a name="supported-data-formats"></a>Podporované formáty dat

Pro všechny ingestování příjímat metody jiné než z dotazu musí být naformátován data v jednom z formátů podporovaných data tak, aby můžete ji parsovat Průzkumník dat Azure.

* CSV, TSV, PSV, SCSV, SOH
* Avro (oddělených čárkami řádku a více řádků), JSON
* ZIP a GZIP 

> [!NOTE]
> Když se data ingestují, datové typy jsou odvozeny podle sloupce cílové tabulky. Pokud záznam je neúplný nebo pole nelze analyzovat jako povinný datový typ, naplní se odpovídající sloupce tabulky s hodnotou null.

## <a name="schema-mapping"></a>Mapování schématu

Mapování schématu pomáhá nedeterministicky vytvořit vazbu pole zdroje dat na sloupce cílové tabulky.

* [Mapování sdíleného svazku clusteru](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#csv-mapping) (volitelné) funguje u všech pořadí formáty a mohou být předány jako parametr příkazu ingestování nebo [předem vytvořit v tabulce](https://docs.microsoft.com/azure/kusto/management/tables?branch=master#create-ingestion-mapping) a odkazované z parametru příkazu ingestování.
* [Mapování JSON](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#json-mapping) (povinné) a [Avro mapování](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#avro-mapping) (povinné) lze předat jako parametr příkazu ingestování nebo [předem vytvořit v tabulce](https://docs.microsoft.com/azure/kusto/management/tables#create-ingestion-mapping) a odkazované z parametru příkazu ingestování.

## <a name="next-steps"></a>Další postup

[Rychlý start: Ingestovat data z centra událostí do Průzkumníku dat Azure](ingest-data-event-hub.md)

[Rychlý start: Ingestování dat pomocí knihovny Python Průzkumník dat Azure](python-ingest-data.md)

