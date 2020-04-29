---
title: Odolnost aktivity kopírování ve službě Azure Data Factory proti chybám
description: Přeskočí nekompatibilní řádky a dozvíte se, jak přidat odolnost proti chybám pro aktivitu kopírování v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: 766520fe44047eee76029adf8ee1683c7b8008a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417857"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Odolnost aktivity kopírování ve službě Azure Data Factory proti chybám
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuální verze](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita kopírování v Azure Data Factory nabízí dva způsoby, jak zpracovávat nekompatibilní řádky při kopírování dat mezi zdrojovým úložištěm a úložišti dat jímky:

- Pokud dojde k nekompatibilním datům (výchozí chování), můžete aktivitu kopírování přerušit a selhat.
- Všechna data můžete dál kopírovat tak, že přidáte odolnost proti chybám a přeskočíte nekompatibilní řádky dat. Kromě toho můžete protokolovat nekompatibilní řádky ve službě Azure Blob Storage nebo Azure Data Lake Store. Pak můžete prostudovat protokol a zjistit příčinu selhání, opravit data ve zdroji dat a opakovat aktivitu kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Aktivita kopírování podporuje tři scénáře zjišťování, přeskočení a protokolování nekompatibilních dat:

- **Nekompatibilita mezi zdrojovým datovým typem a nativním typem jímky**. 

    Například: Zkopírujte data ze souboru CSV v úložišti objektů blob do databáze SQL s definicí schématu, která obsahuje tři sloupce typu INT. Řádky souboru CSV, které obsahují číselná data, například 123 456 789, se úspěšně zkopírují do úložiště jímky. Řádky, které obsahují jiné než číselné hodnoty, jako například 123 456, ABC jsou však zjištěny jako nekompatibilní a jsou vynechány.

- **Neshoda v počtu sloupců mezi zdrojem a jímkou**.

    Například: Zkopírujte data ze souboru CSV v úložišti objektů blob do databáze SQL s definicí schématu, která obsahuje šest sloupců. Řádky souboru CSV, které obsahují šest sloupců, se úspěšně zkopírují do úložiště jímky. Řádky souboru CSV, které obsahují více než šest sloupců, se zjišťují jako nekompatibilní a přeskočí se.

- **Při zápisu do SQL Server/Azure SQL Database/Azure Cosmos DB došlo k porušení primárního klíče**.

    Příklad: kopírování dat z SQL serveru do databáze SQL. Primární klíč je definovaný v databázi SQL jímky, ale ve zdrojovém SQL serveru není definovaný žádný takový primární klíč. Duplicitní řádky, které existují ve zdroji, nelze zkopírovat do jímky. Aktivita kopírování kopíruje do jímky pouze první řádek zdrojových dat. Následné zdrojové řádky, které obsahují duplicitní hodnotu primárního klíče, jsou zjištěny jako nekompatibilní a jsou vynechány.

>[!NOTE]
>- Pokud chcete načíst data do SQL Data Warehouse s využitím základny, nakonfigurujte nativní nastavení odolnosti proti chybám, a to tak, že v aktivitě kopírování zadáte odmítnout zásady prostřednictvím "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)". Přesto můžete povolit přesměrování základních nekompatibilních řádků do objektů BLOB nebo ADLS jako normální, jak je znázorněno níže.
>- Tato funkce se nepoužije, když je aktivita kopírování nakonfigurovaná tak, aby vyvolala službu [Amazon RedShift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Tato funkce se nepoužije, když je aktivita kopírování nakonfigurovaná tak, aby vyvolala [uloženou proceduru z jímky SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

## <a name="configuration"></a>Konfigurace
Následující příklad poskytuje definici JSON pro konfiguraci přeskočení nekompatibilních řádků v aktivitě kopírování:

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
enableSkipIncompatibleRow | Určuje, zda se během kopírování mají přeskočit nekompatibilní řádky. | True<br/>False (výchozí) | Ne
redirectIncompatibleRowSettings | Skupina vlastností, které lze zadat, pokud chcete protokolovat nekompatibilní řádky. | &nbsp; | Ne
linkedServiceName | Propojená služba [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) nebo [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) k uložení protokolu, který obsahuje vynechané řádky. | Název propojené služby typu `AzureStorage` nebo `AzureDataLakeStore` , která odkazuje na instanci, kterou chcete použít k uložení souboru protokolu. | Ne
cesta | Cesta k souboru protokolu, který obsahuje vynechané řádky. | Zadejte cestu, kterou chcete použít k protokolování nekompatibilních dat. Pokud cestu nezadáte, služba vytvoří kontejner. | Ne

## <a name="monitor-skipped-rows"></a>Sledování vynechaných řádků
Po dokončení aktivity kopírování můžete zobrazit počet vynechaných řádků ve výstupu aktivity kopírování:

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
Pokud nakonfigurujete, aby protokoloval nekompatibilní řádky, můžete najít soubor protokolu v této cestě `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`:. 

Soubory protokolu můžou být jenom soubory CSV. Původní data, která se přeskočí, budou v případě potřeby protokolována čárkou jako oddělovač sloupců. Do původního zdrojového data v souboru protokolu přidáme další dva sloupce "ErrorCode" a "ErrorMessage", kde vidíte hlavní příčinu nekompatibility. Kód chyby a ErrorMessage se bude nabízet pomocí dvojitých uvozovek. 

Příkladem obsahu souboru protokolu je následující:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Výkon aktivity kopírování](copy-activity-performance.md)


