---
title: Přidání odolnost proti chybám v Azure Data Factory kopie aktivity přeskočení nekompatibilní řádků | Microsoft Docs
description: Informace o postupu přidání odolnost proti chybám při aktivitě kopírování objektu pro vytváření dat Azure pomocí přeskočení nekompatibilní řádky během kopírování
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d0826db686b7fdea8389944b4b56f549ea03f751
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621043"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Přidání odolnost proti chybám v aktivitě kopírování přeskočení nekompatibilní řádků

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-copy-activity-fault-tolerance.md)
> * [Verze 2 – Preview](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [odolnost proti chybám při aktivitě kopírování objektu pro vytváření dat verze 2](../copy-activity-fault-tolerance.md).

Azure Data Factory [aktivity kopírování](data-factory-data-movement-activities.md) nabízí dva způsoby, jak zpracovat nekompatibilní řádků při kopírování dat mezi zdroj a jímka úložišti dat:

- Můžete přerušit a selhání kopie aktivity po nekompatibilní data došlo (výchozí nastavení).
- Kopírování všech dat přidáním odolnost proti chybám a přeskočení řádky nekompatibilní data můžete pokračovat. Kromě toho se můžete přihlásit nekompatibilní řádky úložiště objektů Blob v Azure. Poté můžete prozkoumat do protokolu a zjistěte příčinu selhání, opravte dat ve zdroji dat a zkuste to aktivitě kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Aktivita kopírování podporuje tři scénáře pro zjišťování, přeskočí a protokolování nekompatibilní data:

- **Nekompatibilita mezi zdrojového datového typu a nativní typ jímky**

    Příklad: kopírování dat ze souboru CSV v úložišti objektů Blob k databázi SQL s definici schématu, která obsahuje tři **INT** typ sloupce. Řádků souboru CSV, které obsahují číselné údaje, jako například `123,456,789` jsou úspěšně zkopírovat do úložiště jímky. Však řádky obsahující jiné než číselné hodnoty, například `123,456,abc` jsou rozpoznána jako nekompatibilní a se přeskočí.

- **Neshoda mezi počtem sloupců mezi zdroj a jímka**

    Příklad: kopírování dat ze souboru CSV v úložišti objektů Blob k databázi SQL s definici schématu, která obsahuje šest sloupce. Řádky soubor CSV, které obsahují šesti sloupce jsou úspěšně zkopírovat do úložiště jímky. Řádky soubor CSV, které obsahují více nebo méně než šest sloupce jsou rozpoznána jako nekompatibilní a se přeskočí.

- **Porušení primárního klíče při zápisu do SQL serveru nebo Azure SQL Database nebo Azure Cosmos DB**

    Příklad: kopírování dat z SQL serveru do databáze SQL. Ve službě SQL database podřízený je definovaný primární klíč, ale na zdrojovém serveru SQL je definován žádný primární klíč. Duplicitní řádky, na které existují ve zdroji nelze zkopírovat do jímky. Aktivita kopírování zkopíruje pouze první řádek zdrojová data do jímky. Další zdroje řádky, které obsahují duplicitní hodnotu primárního klíče jsou rozpoznána jako nekompatibilní a se přeskočí.

>[!NOTE]
>Tato funkce se nevztahuje aktivity kopírování je nakonfigurovaný k vyvolání externích dat načítání, včetně mechanismus [Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) nebo [Amazon Redshift uvolnění](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Pro načítání dat do SQL Data Warehouse pomocí PolyBase, použijte PolyBase na nativní odolnost proti chybám podporu zadáním "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" v aktivitě kopírování.

## <a name="configuration"></a>Konfigurace
Následující příklad uvádí definici JSON konfigurace přeskočení nekompatibilní řádky v aktivitě kopírování:

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
| **enableSkipIncompatibleRow** | Povolte přeskočení nekompatibilní řádků při kopírování nebo ne. | True<br/>NEPRAVDA (výchozí) | Ne |
| **redirectIncompatibleRowSettings** | Skupina vlastností, které může být zadán, pokud chcete protokolovat nekompatibilní řádky. | &nbsp; | Ne |
| **linkedServiceName** | Propojené služby Azure Storage k ukládání protokol, který obsahuje přeskočených řádků. | Název [azurestorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) nebo [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) propojené služby, která odkazuje na instanci úložiště, který chcete použít k uložení souboru protokolu. | Ne |
| **Cesta** | Cesta souboru protokolu, který obsahuje přeskočených řádků. | Zadejte cestu úložiště objektů Blob, které chcete používat k protokolování nekompatibilní data. Pokud nezadáte cestu, služby pro vás vytvoří kontejner. | Ne |

## <a name="monitoring"></a>Monitorování
Po dokončení kopírování aktivity při spuštění, zobrazí se počet přeskočených řádků v části monitorování:

![Monitorování přeskočen nekompatibilní řádků](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Pokud nakonfigurujete protokolu nekompatibilní řádky, můžete nějakého najít soubor protokolu v této cestě: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` v souboru protokolu, můžete zobrazit na řádky, které byly přeskočeny a hlavní příčinu nekompatibilita.

Původní data a odpovídající chyby jsou protokolovány v souboru. Příklad obsahu souboru protokolu je následující:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Další postup
Další informace o aktivitě kopírování objektu pro vytváření dat Azure, najdete v části [přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md).
