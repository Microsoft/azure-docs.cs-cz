---
title: Odolnost proti chybám aktivity kopírování ve službě Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přidat odolnost proti chybám aktivitě kopírování ve službě Azure Data Factory přeskočením nekompatibilních řádků.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: jingwang
ms.openlocfilehash: f1a40c09c2d08eddedd3b6b51d2a138ec403f6bc
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014909"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Odolnost proti chybám aktivity kopírování ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuální verze](copy-activity-fault-tolerance.md)

Aktivita kopírování ve službě Azure Data Factory nabízí dva způsoby, jak řešit nekompatibilních řádků při kopírování dat mezi úložišti dat zdroje a jímky:

- Můžete přerušit a předat při kopírování aktivity po nekompatibilní dat došlo k (výchozí chování).
- Kopírování všech dat přidáním odolnost proti chybám a přeskočí řádky nekompatibilní dat můžete pokračovat. Kromě toho můžete protokolování nekompatibilních řádků v Azure Blob storage nebo Azure Data Lake Store. Poté můžete prozkoumat v protokolu zjistěte příčinu selhání, opravte data ve zdroji dat a opakujte aktivitu kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Aktivitu kopírování, která podporuje tři scénáře pro zjištění, přeskočí a protokolování nekompatibilní data:

- **Nekompatibilita mezi typem zdroje dat a nativní typ jímky**. 

    Příklad: Kopírování dat ze souboru CSV ve službě Blob storage do SQL database pomocí definice schématu, která obsahuje tři sloupce typu INT. Řádků souboru CSV, které obsahují číselná data, jako je například 123,456,789 úspěšně zkopírovány do úložiště jímky. Ale řádky, které obsahují nečíselné hodnoty, jako je například 123,456, abc, jsou rozpoznány jako nekompatibilní a jsou vynechány.

- **Neshoda v počtu sloupců mezi zdroj a jímku**.

    Příklad: Kopírování dat ze souboru CSV ve službě Blob storage do SQL database pomocí definice schématu, který obsahuje šest sloupců. Řádků souboru CSV, které obsahují šest sloupců se úspěšně zkopírují do úložiště jímky. Řádků souboru CSV, které obsahují více nebo méně než šest sloupců, jsou rozpoznány jako nekompatibilní a jsou vynechány.

- **Primární klíče porušení, při zápisu do SQL serveru nebo Azure SQL Database nebo Azure Cosmos DB**.

    Příklad: Kopírování dat z SQL serveru do služby SQL database. Primární klíč je definován ve službě SQL database jímky, ale na zdrojovém serveru SQL není definován žádný takový primární klíč. Duplicitní řádky, které existují ve zdroji nelze zkopírovat do jímky. Aktivita kopírování kopíruje pouze první řádek dat zdroje do jímky. Následné zdrojové řádky, které obsahují duplicitní hodnoty primárního klíče, jsou rozpoznány jako nekompatibilní a jsou vynechány.

>[!NOTE]
>- Pro načítání dat do SQL Data Warehouse pomocí PolyBase, nakonfigurovat PolyBase pro nativní odolnost proti chybám nastavení tak, že určíte zásady na odmítnout prostřednictvím "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" v aktivitě kopírování. Stále můžete povolit přesměrování PolyBase nekompatibilních řádků do objektu Blob nebo ADLS normálním způsobem, jak je znázorněno níže.
>- Tato funkce se nevztahuje při aktivitě kopírování je nakonfigurován k vyvolání [uvolnění Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).


## <a name="configuration"></a>Konfigurace
Následující příklad uvádí definici JSON konfigurace přeskočení nekompatibilních řádků v aktivitě kopírování:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Určuje, jestli se má přeskočit nekompatibilních řádků během kopírování nebo ne. | True<br/>False (výchozí) | Ne
redirectIncompatibleRowSettings | Skupina vlastností, které může být zadaná, kdy budete chtít protokolování nekompatibilních řádků. | &nbsp; | Ne
linkedServiceName | Propojené služby [služby Azure Storage](connector-azure-blob-storage.md#linked-service-properties) nebo [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) ukládat protokol, který obsahuje přeskočených řádků. | Název `AzureStorage` nebo `AzureDataLakeStore` typ propojené služby, který se vztahuje na instance, kterou chcete použít k uložení souboru protokolu. | Ne
path | Cesta souboru protokolu, který obsahuje přeskočených řádků. | Zadejte cestu, kterou chcete použít k protokolování nekompatibilní datový. Pokud nezadáte cestu, služby kontejneru vytvoří za vás. | Ne

## <a name="monitor-skipped-rows"></a>Monitorování bylo přeskočeno řádků
Po spuštění aktivity kopírování dokončí, zobrazí se počet přeskočených řádků ve výstupu aktivity kopírování:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Při konfiguraci protokolování nekompatibilních řádků, můžete najít soubor protokolu na tuto cestu: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Soubory protokolů lze pouze soubory sdíleného svazku clusteru. Původní data se přeskakuje, se budou protokolovat s čárkou jako oddělovač sloupců v případě potřeby. Přidáme další dva sloupce "ErrorCode" a "Chybová zpráva" navíc k původní zdrojová data v souboru protokolu, kde se můžete podívat kořenové příčiny nekompatibility. Kód chyby a chybová zpráva bude uvedený v uvozovkách dvojitých uvozovkách. 

Příklad obsahu souboru protokolu je následujícím způsobem:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Další postup
Zobrazit další články o aktivitě kopírování:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Výkonem aktivity kopírování](copy-activity-performance.md)


