---
title: Schéma mapování v aktivitě kopírování | Microsoft Docs
description: Informace o způsobu aktivitu kopírování v Azure Data Factory mapování schémat a datové typy na jímky dat při kopírování dat ze zdrojových dat.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: dbfbafccc1bc735927535a5ee0f8d232be355dca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618619"
---
# <a name="schema-mapping-in-copy-activity"></a>Schéma mapování v aktivitě kopírování
Tento článek popisuje, jak funguje Azure Data Factory aktivity kopírování schéma mapování a mapování datového typu od zdrojových dat za účelem jímky dat při kopírování dat provádění.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-introduction.md).


## <a name="column-mapping"></a>Mapování sloupce

Ve výchozím nastavení, aktivity kopírování **mapování zdroje dat do jímky podle názvů sloupců**, pokud [mapování explicitní sloupce](#explicit-column-mapping) je nakonfigurovaný. Přesněji řečeno aktivity kopírování:

1. Čtení dat ze zdroje a určení schématu zdroje

    * Pro zdroje dat se předem definované schéma v souboru/úložiště formát dat, například databáze nebo soubory s metadaty (Avro/ORC/Parquet nebo Text s hlavičkou), je schématu zdroje extrahovat z dotazu výsledek nebo soubor metadat.
    * Pro zdroje dat se flexibilní schématu, například Azure Table nebo Cosmos databáze, je schématu zdroje odvodit z výsledku dotazu. Lze jej přepsat zadáním "struktura" v datové sadě.
    * Textový soubor bez hlavičky jsou generovány výchozí názvy sloupců pomocí vzoru "Prop_0", "Prop_1"... Lze jej přepsat zadáním "struktura" v datové sadě.
    * Pro zdroj Dynamics je nutné zadat informace o schématu v části "struktura" datovou sadu.

2. Použít mapování explicitní sloupce, pokud zadaný.

3. Zapsat data do jímky

    * Pro úložiště dat s předem definované schéma budou data zapsána na sloupce se stejným názvem.
    * Pro úložiště dat bez pevného schématu a formátů souborů názvy sloupců nebo metadata generované na základě schématu zdroje.

### <a name="explicit-column-mapping"></a>Mapování explicitní sloupce

Můžete zadat **columnMappings** v **rámci typeProperties** části aktivitě kopírování udělat mapování explicitní sloupce. V tomto scénáři část "struktura" se vyžaduje pro vstupní a výstupní datové sady. Podporuje mapování sloupců **mapování všechny nebo podmnožinu sloupců v datové sadě zdroje "struktura" na všechny sloupce v datové sadě podřízený "struktura"**. Tady jsou chybové stavy, které mít za následek výjimku:

* Úložiště zdroje dat dotazu, výsledek nemá název sloupce, který je uveden v části "struktura" vstupní datové sady.
* Úložiště dat podřízený (Pokud se předem definované schéma) neobsahuje název sloupce, který je uveden v části "struktura" výstupní datovou sadu.
* Méně sloupců nebo více sloupců v "struktuře" datovou sadu podřízený než zadaná v mapování.
* Duplicitní mapování.

#### <a name="explicit-column-mapping-example"></a>Příklad mapování explicitní sloupce

V této ukázce vstupní tabulka obsahuje strukturu a odkazuje na tabulku v místní databázi.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

V této ukázce výstupní tabulka obsahuje strukturu a odkazuje na tabulku v databázi SQL Azure.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Následující kód JSON určuje aktivitu kopírování v kanálu. Sloupce ze zdroje mapovat na sloupce ve podřízený (**columnMappings**) pomocí **překladač** vlastnost.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    }
}
```

**Tok mapování sloupců:**

![Sloupec mapování toku](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="data-type-mapping"></a>Mapování datového typu

Aktivita kopírování provádí typy zdrojů do jímky typy mapování s následující postup krok 2:

1. Převod typů nativní zdroj na Azure Data Factory dočasné datové typy
2. Převést na typ jímky nativní Azure Data Factory dočasné datové typy

Můžete najít mapování mezi nativní typu dočasné typ v části "Mapování datového typu" v tématu každý konektor.

### <a name="supported-data-types"></a>Podporované datové typy

Objekt pro vytváření dat podporuje následující typy průběžných dat: můžete zadat následující hodnoty plynoucí z poskytování informací o typu v [strukturu datové sady](concepts-datasets-linked-services.md#dataset-structure) konfigurace:

* Byte]
* Logická hodnota
* Datum a čas
* Datový typ DateTimeOffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Jednoduchá
* Řetězec
* Časový interval

### <a name="explicit-data-type-conversion"></a>Explicitní datový typ – převod

Při kopírování dat do data ukládá s pevného schématu, například SQL Server nebo Oracle, pokud zdroj a jímka má jiný typ ve stejném sloupci, by měly být na straně zdroje deklarovány převodu explicitního typu:

* Pro zdroj souborů například CSV nebo Avro, převod typů se prohlásí prostřednictvím struktura zdroje s seznamu úplné sloupců (straně sloupec název a jímka straně typ zdroje)
* Pro relačního zdroje (například SQL nebo Oracle) by mělo být dosaženo převod typu přetypování explicitního typu příkazu dotazu.

## <a name="when-to-specify-dataset-structure"></a>Pokud chcete zadat datovou sadu "struktura"

V následující scénáře, "struktura" v datové sadě se vyžaduje:

* Použití [explicitní datový typ – převod](#explicit-data-type-conversion) pro souborové zdroje během kopírování (vstupní datové sady)
* Použití [mapování explicitní sloupce](#explicit-column-mapping) během kopírování (vstupní i výstupní datovou sadu)
* Kopírování ze zdroje nebo CRM Dynamics 365 (vstupní datové sady)
* Kopírování do Cosmos DB jako vnořeného objektu není zdrojem soubory JSON (výstupní datové sady)

V následující scénáře, "struktura" v datové sadě navržený:

* Kopírování z textového souboru bez hlavičky (vstupní datové sady). Můžete zadat názvy sloupců pro textový soubor volbě vhodného jímka na odpovídající sloupce pro uložení v poskytování mapování explicitní sloupce.
* Kopírování z dat obchodů s flexibilní schématu, například Azure Table nebo Cosmos DB (vstupní datové sady), zaručit očekávaná data (sloupce) kopírovány přes místo umožňují kopie aktivity odvození schématu na nejvyšší řádky při každé aktivity při spuštění.


## <a name="next-steps"></a>Další postup
Najdete v aktivitě kopírování článcích:

- [Kopie aktivity – přehled](copy-activity-overview.md)
- [Zkopírujte aktivity odolnost proti chybám](copy-activity-fault-tolerance.md)
- [Výkonu kopie aktivity](copy-activity-performance.md)
