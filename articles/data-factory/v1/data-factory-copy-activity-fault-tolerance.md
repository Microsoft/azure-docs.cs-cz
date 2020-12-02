---
title: Přidání odolnosti proti chybám v Azure Data Factory aktivity kopírování přeskočením nekompatibilních řádků
description: Naučte se, jak přidat odolnost proti chybám v Azure Data Factory aktivity kopírování vynecháním nekompatibilních řádků během kopírování.
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
ms.openlocfilehash: 65584b2a6a3bdfbb863c26dac688b20279c4b54d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452282"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Přidání odolnosti proti chybám v aktivitě kopírování přeskočením nekompatibilních řádků

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-copy-activity-fault-tolerance.md)
> * [Verze 2 (aktuální verze)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si část odolnost [proti chybám v aktivitě kopírování Data Factory](../copy-activity-fault-tolerance.md).

Azure Data Factory [aktivita kopírování](data-factory-data-movement-activities.md) nabízí dva způsoby, jak zpracovávat nekompatibilní řádky při kopírování dat mezi zdrojovým úložištěm a úložišti dat jímky:

- Pokud dojde k nekompatibilním datům (výchozí chování), můžete aktivitu kopírování přerušit a selhat.
- Všechna data můžete dál kopírovat tak, že přidáte odolnost proti chybám a přeskočíte nekompatibilní řádky dat. Kromě toho můžete protokolovat nekompatibilní řádky v úložišti objektů BLOB v Azure. Pak můžete prostudovat protokol a zjistit příčinu selhání, opravit data ve zdroji dat a opakovat aktivitu kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Aktivita kopírování podporuje tři scénáře zjišťování, přeskočení a protokolování nekompatibilních dat:

- **Nekompatibilita mezi typem zdrojových dat a nativním typem jímky**

    Například: Zkopírujte data ze souboru CSV v úložišti objektů blob do databáze SQL s definicí schématu, která obsahuje tři sloupce typu **int** . Řádky souboru CSV, které obsahují číselná data, například, `123,456,789` se úspěšně zkopírují do úložiště jímky. Řádky, které obsahují jiné než číselné hodnoty, například `123,456,abc` jsou zjištěny jako nekompatibilní a jsou vynechány.

- **Neshoda v počtu sloupců mezi zdrojem a jímkou**

    Například: Zkopírujte data ze souboru CSV v úložišti objektů blob do databáze SQL s definicí schématu, která obsahuje šest sloupců. Řádky souboru CSV, které obsahují šest sloupců, se úspěšně zkopírují do úložiště jímky. Řádky souboru CSV, které obsahují více nebo méně než šest sloupců, se zjišťují jako nekompatibilní a přeskočí se.

- **Narušení primárního klíče při zápisu do SQL Serveru nebo služeb Azure SQL Database a Azure Cosmos DB**

    Příklad: kopírování dat z SQL serveru do databáze SQL. Primární klíč je definovaný v databázi SQL jímky, ale ve zdrojovém SQL serveru není definovaný žádný takový primární klíč. Duplicitní řádky, které existují ve zdroji, nelze zkopírovat do jímky. Aktivita kopírování kopíruje do jímky pouze první řádek zdrojových dat. Následné zdrojové řádky, které obsahují duplicitní hodnotu primárního klíče, jsou zjištěny jako nekompatibilní a jsou vynechány.

>[!NOTE]
>Tato funkce se nepoužije, když je aktivita kopírování nakonfigurovaná tak, aby vyvolala mechanismus načítání externích dat, včetně [základu služby Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) nebo zrušení [načtení služby Amazon RedShift](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Pokud chcete načíst data do Azure synapse Analytics pomocí základu, použijte pro aktivitu kopírování "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" nativní podporu odolné vůči chybám.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Povolit přeskočení nekompatibilních řádků během kopírování nebo ne. | Pravda<br/>False (výchozí) | No |
| **redirectIncompatibleRowSettings** | Skupina vlastností, které lze zadat, pokud chcete protokolovat nekompatibilní řádky. | &nbsp; | No |
| **linkedServiceName** | Propojená služba Azure Storage k uložení protokolu, který obsahuje vynechané řádky. | Název propojené služby [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) nebo [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) , která odkazuje na instanci úložiště, kterou chcete použít k uložení souboru protokolu. | No |
| **dílčí** | Cesta k souboru protokolu, který obsahuje vynechané řádky. | Zadejte cestu k úložišti objektů blob, kterou chcete použít k protokolování nekompatibilních dat. Pokud cestu nezadáte, služba vytvoří kontejner. | No |

## <a name="monitoring"></a>Sledování
Po dokončení aktivity kopírování můžete v části monitorování zobrazit počet vynechaných řádků:

![Monitorování přeskočilo nekompatibilní řádky.](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Pokud nakonfigurujete, aby protokoloval nekompatibilní řádky, můžete najít soubor protokolu v této cestě: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` v souboru protokolu můžete zobrazit řádky, které se přeskočily, a původní příčinu nekompatibility.

Původní data a odpovídající chyba jsou protokolovány v souboru. Příkladem obsahu souboru protokolu je následující:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Další kroky
Další informace o aktivitě kopírování Azure Data Factory najdete v tématu [přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md).
