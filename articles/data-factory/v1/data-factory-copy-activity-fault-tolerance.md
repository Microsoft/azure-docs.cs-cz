---
title: Přidat odolnost proti chybám přeskočením nekompatibilních řádků v aktivitě kopírování objekt pro vytváření dat Azure | Dokumentace Microsoftu
description: Zjistěte, jak přidat odolnost proti chybám přeskočením nekompatibilních řádků během kopírování v aktivitě kopírování objekt pro vytváření dat Azure
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3a255b21e8bfd7d78954603e9aa6e5ca39cee95b
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321989"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Přidat odolnost proti chybám v aktivitě kopírování přeskočením nekompatibilních řádků

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-copy-activity-fault-tolerance.md)
> * [Verze 2 (aktuální verze)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [odolnost proti chybám v aktivitě kopírování objektu pro vytváření dat](../copy-activity-fault-tolerance.md).

Azure Data Factory [aktivity kopírování](data-factory-data-movement-activities.md) nabízí dva způsoby, jak řešit nekompatibilních řádků při kopírování dat mezi úložišti dat zdroje a jímky:

- Můžete přerušit a předat při kopírování aktivity po nekompatibilní dat došlo k (výchozí chování).
- Kopírování všech dat přidáním odolnost proti chybám a přeskočí řádky nekompatibilní dat můžete pokračovat. Kromě toho můžete protokolování nekompatibilních řádků ve službě Azure Blob storage. Poté můžete prozkoumat v protokolu zjistěte příčinu selhání, opravte data ve zdroji dat a opakujte aktivitu kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Aktivitu kopírování, která podporuje tři scénáře pro zjištění, přeskočí a protokolování nekompatibilní data:

- **Nekompatibilita mezi typem zdroje dat a nativní typ jímky**

    Příklad: Kopírování dat ze souboru CSV ve službě Blob storage do SQL database pomocí definice schématu, která obsahuje tři **INT** sloupce. Řádky soubor CSV, které obsahují číselná data, jako například `123,456,789` úspěšně zkopírovány do úložiště jímky. Však řádky, které obsahují nečíselné hodnoty, jako `123,456,abc` , jsou rozpoznány jako nekompatibilní a jsou vynechány.

- **Neshoda v počtu sloupců mezi zdroj a jímka**

    Příklad: Kopírování dat ze souboru CSV ve službě Blob storage do SQL database pomocí definice schématu, který obsahuje šest sloupců. Řádků souboru CSV, které obsahují šest sloupců se úspěšně zkopírují do úložiště jímky. Řádků souboru CSV, které obsahují více nebo méně než šest sloupců, jsou rozpoznány jako nekompatibilní a jsou vynechány.

- **Primární klíče porušení, při zápisu do SQL serveru nebo Azure SQL Database nebo Azure Cosmos DB**

    Příklad: Kopírování dat z SQL serveru do služby SQL database. Primární klíč je definován ve službě SQL database jímky, ale na zdrojovém serveru SQL není definován žádný takový primární klíč. Duplicitní řádky, které existují ve zdroji nelze zkopírovat do jímky. Aktivita kopírování kopíruje pouze první řádek dat zdroje do jímky. Následné zdrojové řádky, které obsahují duplicitní hodnoty primárního klíče, jsou rozpoznány jako nekompatibilní a jsou vynechány.

>[!NOTE]
>Tato funkce se nevztahuje při aktivitě kopírování je nakonfigurován k vyvolání načítání mechanismus včetně externích dat [Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) nebo [uvolnění Amazon Redshift](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Načítání dat do SQL Data Warehouse pomocí PolyBase, pomocí PolyBase pro nativní odolnost proti chybám podporu tak, že zadáte "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" v aktivitě kopírování.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Povolte přeskočení nekompatibilních řádků během kopírování nebo ne. | True<br/>False (výchozí) | Ne |
| **redirectIncompatibleRowSettings** | Skupina vlastností, které může být zadaná, kdy budete chtít protokolování nekompatibilních řádků. | &nbsp; | Ne |
| **linkedServiceName** | Propojená služba Azure Storage k ukládání protokolů, který obsahuje přeskočených řádků. | Název [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) nebo [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) propojenou službu, která odkazuje na instanci úložiště, který chcete použít k uložení souboru protokolu. | Ne |
| **Cesta** | Cesta souboru protokolu, který obsahuje přeskočených řádků. | Zadejte cestu úložiště objektů Blob, který chcete použít protokolování dat o nekompatibilní. Pokud nezadáte cestu, služby kontejneru vytvoří za vás. | Ne |

## <a name="monitoring"></a>Monitorování
Po spuštění aktivity kopírování dokončí, zobrazí se počet přeskočených řádků v části monitorování:

![Monitorování přeskočeno nekompatibilních řádků](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Při konfiguraci protokolování nekompatibilních řádků, můžete najít soubor protokolu v tomto umístění: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` V souboru protokolu můžete zobrazit řádky, které byly přeskočeny a hlavní příčinou nekompatibilita.

Původní data a odpovídající chyby jsou protokolovány v souboru. Příklad obsahu souboru protokolu je následujícím způsobem:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Další postup
Další informace o aktivitě kopírování objekt pro vytváření dat Azure najdete v tématu [přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md).
