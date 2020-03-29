---
title: Přidání odolnosti proti chybám v aktivitě kopírování Azure Data Factory přeskočením nekompatibilních řádků
description: Zjistěte, jak přidat odolnost proti chybám v aktivitě kopírování Azure Data Factory přeskočením nekompatibilních řádků během kopírování
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8ffaee75154fd5fe025bdb683c89f16799d6e86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926154"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Přidání odolnosti proti chybám v aktivitě kopírování přeskočením nekompatibilních řádků

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-copy-activity-fault-tolerance.md)
> * [Verze 2 (aktuální verze)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, viz [odolnost proti chybám v aktivitě kopírování data factory](../copy-activity-fault-tolerance.md).

[Aktivita kopírování](data-factory-data-movement-activities.md) Azure Data Factory nabízí dva způsoby zpracování nekompatibilních řádků při kopírování dat mezi zdrojovými daty a úložišti dat jímky:

- Můžete přerušit a selhání aktivity kopírování při výskytu nekompatibilních dat (výchozí chování).
- Můžete pokračovat v kopírování všech dat přidáním odolnosti proti chybám a přeskočením nekompatibilních řádků dat. Kromě toho můžete protokolovat nekompatibilní řádky v úložišti objektů Blob Azure. Potom můžete zkontrolovat protokolu zjistit příčinu selhání, opravit data na zdroj dat a opakujte aktivitu kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Aktivita kopírování podporuje tři scénáře pro zjišťování, přeskakování a protokolování nekompatibilních dat:

- **Nekompatibilita mezi typem zdrojových dat a nativním typem jímky**

    Příklad: Kopírování dat ze souboru CSV v úložišti objektů Blob do databáze SQL s definicí schématu, která obsahuje tři sloupce typu **INT.** Řádky souboru CSV, které obsahují `123,456,789` číselná data, například jsou úspěšně zkopírovány do úložiště jímky. Řádky, které obsahují nečíselné hodnoty, `123,456,abc` například jsou zjištěny jako nekompatibilní a jsou přeskočeny.

- **Neshoda v počtu sloupců mezi zdrojem a jímkou**

    Příklad: Kopírování dat ze souboru CSV v úložišti objektů Blob do databáze SQL s definicí schématu, která obsahuje šest sloupců. Řádky souboru CSV, které obsahují šest sloupců jsou úspěšně zkopírovány do úložiště jímky. Řádky souboru CSV, které obsahují více nebo méně než šest sloupců jsou označeny jako nekompatibilní a jsou přeskočeny.

- **Narušení primárního klíče při zápisu do SQL Serveru nebo služeb Azure SQL Database a Azure Cosmos DB**

    Příklad: Kopírování dat ze serveru SQL do databáze SQL. Primární klíč je definován v databázi databáze dřezu SQL, ale žádný takový primární klíč je definován ve zdrojovém serveru SQL. Duplicitní řádky, které existují ve zdroji nelze zkopírovat do jímky. Kopírovat aktivita zkopíruje pouze první řádek zdrojových dat do jímky. Následující zdrojové řádky, které obsahují duplicitní hodnotu primárního klíče, jsou rozpoznány jako nekompatibilní a jsou přeskočeny.

>[!NOTE]
>Tato funkce se nepoužije, když je aktivita kopírování nakonfigurována tak, aby vyvolával mechanismus načítání externích dat, včetně [Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) nebo Amazon [Redshift Unload](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Pro načítání dat do datového skladu SQL pomocí PolyBase použijte podporu nativní odolnosti proti chybám polyBase zadáním "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" v aktivitě kopírování.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Povolte přeskočení nekompatibilních řádků během kopírování nebo ne. | True<br/>False (výchozí) | Ne |
| **redirectIncompatibleRowSettings** | Skupina vlastností, které lze zadat, pokud chcete protokolovat nekompatibilní řádky. | &nbsp; | Ne |
| **linkedServiceName** | Propojená služba Azure Storage pro uložení protokolu, který obsahuje přeskočené řádky. | Název propojené služby [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) nebo [AzureStorageSas,](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) která odkazuje na instanci úložiště, kterou chcete použít k uložení souboru protokolu. | Ne |
| **Cestu** | Cesta k souboru protokolu, který obsahuje přeskočené řádky. | Zadejte cestu úložiště objektů blob, kterou chcete použít k protokolování nekompatibilních dat. Pokud nezadáte cestu, služba vytvoří kontejner pro vás. | Ne |

## <a name="monitoring"></a>Monitorování
Po dokončení spuštění aktivity kopírování uvidíte počet přeskočených řádků v části monitorování:

![Monitor přeskočené nekompatibilní řádky](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Pokud nakonfigurujete protokolovat nekompatibilní řádky, můžete `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` najít soubor protokolu na této cestě: V souboru protokolu, můžete vidět řádky, které byly přeskočeny a hlavní příčinou nekompatibility.

Do souboru jsou zaznamenána původní data i odpovídající chyba. Příklad obsahu souboru protokolu je následující:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Další kroky
Další informace o aktivitě kopírování Azure Data Factory najdete v tématu [Přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md).
