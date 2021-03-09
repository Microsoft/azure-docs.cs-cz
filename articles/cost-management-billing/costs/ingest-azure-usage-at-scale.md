---
title: Opakované načítání datových sad s velkými náklady s exporty
description: Tento článek vám pomůže pravidelně exportovat velké objemy dat s využitím exportů z Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 465225341bdffc984ac6cbc82ba94eb656ad60df
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509666"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Opakované načítání datových sad s velkými náklady s exporty

Tento článek vám pomůže pravidelně exportovat velké objemy dat s využitím exportů z Azure Cost Management. Export je doporučený způsob načítání neagregovaných informací o nákladech. Platí to zejména v případě, že soubory s daty o využití jsou příliš rozsáhlé a nejde je spolehlivě volat a stahovat pomocí rozhraní API pro podrobnosti využití. Exportovaná data se uloží do účtu Azure Storage, který zvolíte. Odsud je můžete načítat do vlastních systémů a analyzovat podle vašich potřeb. Pokud chcete nakonfigurovat exporty na webu Azure Portal, přečtěte si téma věnované [exportu dat](tutorial-export-acm-data.md).

Pokud chcete automatizovat exporty pro různé rozsahy, vhodným výchozím bodem je ukázkový požadavek rozhraní API uvedený v další části. Můžete využít rozhraní API pro export a vytvořit automatické exporty jako součást obecné konfigurace prostředí. Automatické exporty vám pomohou zajistit, že máte potřebná data. Při rozšiřování využití Azure je můžete využít ve vlastních systémech vaší organizace.

## <a name="common-export-configurations"></a>Běžné konfigurace exportu

Před vytvořením prvního exportu se zamyslete nad scénářem a možnostmi konfigurace, které jsou pro něj potřeba. Zvažte následující možnosti exportu:

- **Opakování:** Určuje, jak často běží úlohy exportu a kdy se do účtu Azure Storage umístí výsledný soubor. Možnosti jsou každý den, každý týden nebo každý měsíc. Zkuste nakonfigurovat opakování tak, aby odpovídalo úlohám importu dat používaným interním systémem vaší organizace.
- **Období opakování:** Určuje, po jak dlouhou dobu zůstane export platný. Soubory se exportují jenom během období opakování.
- **Časový rámec:** Určuje množství dat vygenerovaných exportem při konkrétním spuštění. Běžné možnosti jsou od začátku měsíce a od začátku týdne.
- **Datum zahájení:** Určuje, odkdy má začít platit rozvrh pro export. Export se vytvoří v den zahájení a následně na základě určeného opakování.
- **Typ:** Existují tři typy exportu:
  - ActualCost: Celkové využití a náklady za zadané období tak, jak se vyúčtují a zobrazí na faktuře.
  - AmortizedCost: Celkové využití a náklady za zadané období. U nákladů na nákup rezervací se přitom použije odpovídající amortizace.
  - Usage: Typu Usage jsou všechny exporty vytvořené do 20. července 2020. Všechny naplánované exporty aktualizujte jako ActualCost nebo AmortizedCost.
- **Sloupce:** Definuje datová pole, která chcete zahrnout do souboru exportu. Odpovídají polím, která jsou k dispozici v rozhraní API pro podrobnosti využití. Další informace najdete v tématu věnovaném [rozhraní API pro podrobnosti využití](/rest/api/consumption/usagedetails/list).

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Vytvoření denního exportu od začátku měsíce pro předplatné

Adresa URL požadavku: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>Kopírování rozsáhlých objektů BLOB služby Azure Storage

Pomocí Cost Management můžete naplánovat export podrobností o využití Azure do vašich účtů Azure Storage jako objekty blob. Výsledné velikosti objektů BLOB by mohly být větší než gigabajty. Tým Azure Cost Management pracoval s týmem týmu Azure Storage k testování kopírování velkých objektů BLOB služby Azure Storage. Výsledky jsou popsány v následujících částech. Při kopírování objektů BLOB úložiště z jedné oblasti Azure do jiné můžete očekávat podobné výsledky.

K otestování jeho výkonu tým přenesl objekty BLOB z účtů úložiště v oblasti USA – západ do stejné a jiné oblasti. Tým měřené rychlosti v rozsahu od 2 GB za sekundu ve stejné oblasti až 150 MB za sekundu do účtů úložiště v oblasti jih Východní Asie.

### <a name="test-configuration"></a>Konfigurace testu

Pro měření rychlosti přenosu objektů BLOB tým vytvořil jednoduchou konzolovou aplikaci .NET, která odkazuje na nejnovější verzi (v 2.0.1) knihovny pro přesun dat Azure (DLM) přes NuGet. DLM je sada SDK poskytovaná Azure Storage týmem, která usnadňuje programový přístup ke svým službám přenosu. Pak vytvořili standardní účty úložiště v2 ve více oblastech a jako zdrojovou oblast používají Západní USA. Naplnily účty úložiště s kontejnery, kde každý z nich uchovává objekty blob bloku s 10 2 GB. Zkopírovaly kontejnery do jiných účtů úložiště pomocí metody _TransferManager. CopyDirectoryAsync ()_ DLM s možností _CopyMethod. ServiceSideSyncCopy_ . Testy se prováděly na počítači se systémem Windows 10 s 12 jádry a 1 GbE sítě.

Použitá nastavení aplikace:

- _TransferManager.Configurations. ParallelOperations_  =  _prostředí. ProcessorCount \* 32_. Tým nalezl nastavení, které má největší vliv na celkovou propustnost. Hodnota 32 krát počet jader, které poskytovaly nejlepší propustnost pro testovacího klienta.
- _Třída ServicePointManager. DefaultConnectionLimit = int. MaxValue_. Nastavení na maximální hodnotu efektivně předává úplnou kontrolu nad přenos paralelismu do výše uvedeného nastavení _ParallelOperations_ .
- _TransferManager.Configurations. Velikostí bloku = 4 194 304_. Mělo by to mít nějaký účinek na přenosové rychlosti se 4 MB, aby se prokázalo, že bude nejvhodnější pro testování.

Další informace a ukázku kódu naleznete v tématu odkazy v části [Další kroky](#next-steps) .

### <a name="test-results"></a>Výsledky testů

| **Testovací číslo** | **Do oblasti** | **Objekty blob** | **Čas (s)** | **MB/s** | **Komentáře** |
| --- | --- | --- | --- | --- | --- |
| 1 | WestUS | 2 GB × 10 | 10 | 2 000 |   |
| 2 | WestUS2 | 2 GB × 10 | 33 | 600 |   |
| 3 | EastUS | 2 GB × 10 | 67 | 300 |   |
| 4 | EastUS | 2 GB × 10 × 4 | 99 | 200 | 4 paralelní přenosy pomocí 8 účtů úložiště: průměrně 4 západní až 4 východní průměr za přenos |
| 6 | EastUS | 2 GB × 10 × 4 | 92 | 870 | 4 paralelní přenosy z 1 účtu úložiště do jiného |
| 5 | EastUS | 2G × 10 × 8 | 148 | 135 | 8 paralelních přenosů za použití 8 účtů úložiště: 4 západní až 4x2 východní průměr za přenos |
| 7 | JIHOVÝCHODNÍ Asie | 2 GB × 10 | 133 | 150 |   |
| 8 | JIHOVÝCHODNÍ Asie | 2 GB × 10 × 4 | 444 | 180 | 4 paralelní přenosy z 1 účtu úložiště do jiného |

### <a name="sync-transfer-characteristics"></a>Charakteristiky přenosu synchronizace

Tady jsou některé charakteristiky přenosu synchronizace na straně služby, které se používají s DML, které jsou relevantní pro jeho použití:

- DML může přenést jeden objekt BLOB nebo adresář. Pro přenos adresářů můžete použít vzor hledání, který se bude shodovat s prefixem objektu BLOB.
- Dojde k paralelnímu přenosu objektů blob bloku. Vše se dokončí na konec procesu přenosu. Jednotlivé bloky objektů BLOB jsou přenášeny paralelně.
- Přenos se spustí asynchronně na klientovi. Stav přenosu je pravidelně k dispozici prostřednictvím zpětného volání do metody, která může být definována v objektu _TransferContext_ .
- Během procesu přenosu se vytvoří kontrolní body a zpřístupní se objekt _TransferCheckpoint_ . Objekt představuje poslední kontrolní bod prostřednictvím objektu _TransferContext_ . Pokud se _TransferCheckpoint_ uloží před zrušením nebo přerušením přenosu, můžete přenos obnovit z kontrolního bodu po dobu až sedmi dnů. Přenos se dá obnovit z libovolného kontrolního bodu, nikoli jenom z posledního.
- Pokud je proces přenosu klienta ukončen a restartován bez implementace funkce kontrolního bodu.
  - Před dokončením všech přenosů objektů BLOB se přenos restartuje.
  - Po dokončení některých objektů BLOB se přenos restartuje jenom pro nedokončené objekty blob.
- Pozastavení spuštění klienta pozastaví přenos.
- Funkce přenosu objektů BLOB vyabstrakce klienta z přechodných selhání. Omezení účtu úložiště obvykle nezpůsobí selhání přenosu, ale zpomaluje přenos.
- Přenosy na straně služby mají nízké využití prostředků klientů pro PROCESORy a paměť, šířku pásma sítě a připojení.

### <a name="async-transfer-characteristics"></a>Charakteristiky asynchronního přenosu

Metodu _TransferManager. CopyDirectoryAsync ()_ lze vyvolat pomocí možnosti _CopyMethod. ServiceSideAsyncCopy_ . Funguje podobně jako mechanismus přenosu synchronizace z perspektivy klienta, ale s následujícími rozdíly v provozu:

- Přenosové rychlosti jsou mnohem pomalejší než ekvivalentní přenos synchronizace (obvykle 10 MB/s nebo méně).
- Přenos pokračuje i v případě, že se proces klienta ukončí.
- I když jsou podporované kontrolní body, obnovení přenosu pomocí _TransferCheckpoint_ nebude pokračovat v době kontrolního bodu, ale v aktuálním stavu přenosu.

### <a name="test-summary"></a>Souhrn testu

Azure Blob Storage podporuje vysoké sazby za globální přenos pomocí funkce pro přenos synchronizace na straně služby. Použití funkce v aplikacích .NET je jednoduché pomocí knihovny pro přesun dat. Je možné Cost Management exportovat a spolehlivě kopírovat stovky gigabajtů dat do účtu úložiště kdekoli za méně než hodinu.

## <a name="next-steps"></a>Další kroky

- Viz zdroj [knihovny pro přesun dat Microsoft Azure Storage](https://github.com/Azure/azure-storage-net-data-movement) .
- [Přeneste data pomocí knihovny pro přesun dat](../../storage/common/storage-use-data-movement-library.md).
- Podívejte se na ukázku zdrojového kódu [ukázkové aplikace AzureDmlBackup](https://github.com/markjbrown/AzureDmlBackup) .
- Přečtěte si [vysokou propustnost pomocí Azure Blob Storage](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage).