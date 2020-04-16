---
title: Odolnost aktivity kopírování ve službě Azure Data Factory proti chybám
description: Přečtěte si, jak přidat odolnost proti chybám ke kopírování aktivity v Azure Data Factory přeskočením nekompatibilnířádky.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417857"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Odolnost aktivity kopírování ve službě Azure Data Factory proti chybám
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuální verze](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita kopírování v Azure Data Factory nabízí dva způsoby zpracování nekompatibilních řádků při kopírování dat mezi zdrojovými a jímanami úložišť dat:

- Můžete přerušit a selhání aktivity kopírování při výskytu nekompatibilních dat (výchozí chování).
- Můžete pokračovat v kopírování všech dat přidáním odolnosti proti chybám a přeskočením nekompatibilních řádků dat. Kromě toho můžete protokolovat nekompatibilní řádky v úložišti objektů Blob Azure nebo Azure Data Lake Store. Potom můžete zkontrolovat protokolu zjistit příčinu selhání, opravit data na zdroj dat a opakujte aktivitu kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Aktivita kopírování podporuje tři scénáře pro zjišťování, přeskakování a protokolování nekompatibilních dat:

- **Nekompatibilita mezi zdrojovým datovým typem a nativním typem jímky**. 

    Příklad: Kopírování dat ze souboru CSV v úložišti objektů Blob do databáze SQL s definicí schématu, která obsahuje tři sloupce typu INT. Řádky souboru CSV, které obsahují číselná data, například 123 456 789 jsou úspěšně zkopírovány do úložiště jímky. Řádky, které obsahují nečíselné hodnoty, například 123 456, abc jsou však zjištěny jako nekompatibilní a jsou přeskočeny.

- **Neshoda v počtu sloupců mezi zdrojem a jímkou**.

    Příklad: Kopírování dat ze souboru CSV v úložišti objektů Blob do databáze SQL s definicí schématu, která obsahuje šest sloupců. Řádky souboru CSV, které obsahují šest sloupců jsou úspěšně zkopírovány do úložiště jímky. Řádky souboru CSV, které obsahují více než šest sloupců jsou označeny jako nekompatibilní a jsou přeskočeny.

- **Narušení primárního klíče při zápisu do SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Příklad: Kopírování dat ze serveru SQL do databáze SQL. Primární klíč je definován v databázi databáze dřezu SQL, ale žádný takový primární klíč je definován ve zdrojovém serveru SQL. Duplicitní řádky, které existují ve zdroji nelze zkopírovat do jímky. Kopírovat aktivita zkopíruje pouze první řádek zdrojových dat do jímky. Následující zdrojové řádky, které obsahují duplicitní hodnotu primárního klíče, jsou rozpoznány jako nekompatibilní a jsou přeskočeny.

>[!NOTE]
>- Pro načítání dat do datového skladu SQL pomocí PolyBase nakonfigurujte nativní nastavení odolnosti proti chybám polyBase zadáním zásad odmítnutí pomocí "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" v aktivitě kopírování. Stále můžete povolit přesměrování PolyBase nekompatibilní řádky na blob nebo ADLS jako normální, jak je znázorněno níže.
>- Tato funkce se nepoužije, pokud je aktivita kopírování nakonfigurována tak, aby vyvolala [aplikaci Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Tato funkce se nepoužije, pokud je aktivita kopírování nakonfigurována tak, aby vyvolala [uloženou proceduru z jímky SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

## <a name="configuration"></a>Konfigurace
Následující příklad obsahuje definici JSON pro konfiguraci přeskočení nekompatibilních řádků v aktivitě kopírování:

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
enableSkipIncompatibleRow | Určuje, zda mají být během kopírování přeskočte nekompatibilní řádky. | True<br/>False (výchozí) | Ne
redirectIncompatibleRowSettings | Skupina vlastností, které lze zadat, pokud chcete protokolovat nekompatibilní řádky. | &nbsp; | Ne
linkedServiceName | Propojená služba [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) nebo Azure Data Lake [Store](connector-azure-data-lake-store.md#linked-service-properties) pro uložení protokolu, který obsahuje přeskočené řádky. | Název `AzureStorage` propojené služby nebo `AzureDataLakeStore` typu, který odkazuje na instanci, kterou chcete použít k uložení souboru protokolu. | Ne
cesta | Cesta k souboru protokolu, který obsahuje přeskočené řádky. | Zadejte cestu, kterou chcete použít k protokolování nekompatibilních dat. Pokud nezadáte cestu, služba vytvoří kontejner pro vás. | Ne

## <a name="monitor-skipped-rows"></a>Monitor přeskočených řádků
Po dokončení spuštění aktivity kopírování uvidíte počet přeskočených řádků ve výstupu aktivity kopírování:

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
Pokud nakonfigurujete protokolování nekompatibilních řádků, `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`můžete soubor protokolu najít na této cestě: . 

Soubory protokolu mohou být pouze soubory csv. Původní přeskočená data budou v případě potřeby zaznamenána čárkou jako oddělovačem sloupců. Přidáme další dva sloupce "ErrorCode" a "ErrorMessage" navíc k původním zdrojovým datům v souboru protokolu, kde můžete vidět hlavní příčinu nekompatibility. ErrorCode a ErrorMessage budou citovány dvojitými uvozovkami. 

Příklad obsahu souboru protokolu je následující:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Další kroky
Podívejte se na další články aktivity kopírování:

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Výkon aktivity kopírování](copy-activity-performance.md)


