---
title: Mapování schématu v aktivitě kopírování | Dokumentace Microsoftu
description: Informace o způsobu mapování schémat a datové typy z datového zdroje do jímky dat při kopírování dat aktivita kopírování ve službě Azure Data Factory.
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
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 54c334aa9363ac5ca75cc4ad5b107524f502011e
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53810607"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapování schématu v aktivitě kopírování
Tento článek popisuje, jak funguje Azure Data Factory za kopírování schématu mapování a mapování datového typu z datového zdroje do jímky dat při spuštění kopírovat data.

## <a name="column-mapping"></a>Mapování sloupců

Mapování sloupců platí při kopírování dat mezi daty tabulkové ve tvaru. Ve výchozím nastavení, aktivita kopírování **mapování dat zdroje do jímky podle názvů sloupců**, není-li [mapování sloupců explicitní](#explicit-column-mapping) je nakonfigurované. Přesněji řečeno aktivita kopírování:

1. Číst data ze zdroje a určit schématu zdroje

    * Pro zdroje dat s předem definované schéma v souboru/úložiště formát dat, například databáze nebo soubory s metadaty (Avro/ORC/Parquet nebo Text s hlavičkou), je extrahován schématu zdroje z dotazu výsledek nebo soubor metadat.
    * Pro zdroje dat s flexibilním schématem, například tabulku nebo databázi Azure Cosmos schématu zdroje je odvozen z výsledku dotazu. Lze jej přepsat tím, že nakonfigurujete "struktura" v datové sadě.
    * Textový soubor bez záhlaví výchozí názvy sloupců jsou generovány pomocí vzoru "Prop_0", "Prop_1"... Lze jej přepsat tím, že nakonfigurujete "struktura" v datové sadě.
    * Pro zdroj Dynamics je nutné zadat informace o schématu v oddílu "struktura" datové sady.

2. Použijte explicitní sloupec mapování, pokud zadaný.

3. Zápis dat do jímky

    * Pro úložiště dat s předem definované schéma data zapisují do sloupce se stejným názvem.
    * Pro úložiště dat bez pevného schématu a formátů souborů názvy sloupců nebo metadata se vygeneruje na základě schématu zdroje.

### <a name="explicit-column-mapping"></a>Explicitní mapování sloupce

Můžete zadat **columnMappings** v **typeProperties** část aktivitu kopírování k mapování explicitní sloupce. V tomto scénáři je povinné pro vstupní a výstupní datové sady část "struktura". Podporuje mapování sloupce **mapování všech nebo podmnožinu sloupců v datové sadě zdroj "struktura" všech sloupců v datové sadě jímky "struktura"**. Následují chybových podmínek, za následek výjimku:

* Zdrojového úložiště dat dotazu, že výsledek nemá název sloupce, které je definováno v sekci "struktura" vstupní datové sady.
* Úložiště dat jímky (Pokud se předem definované schéma) nemá název sloupce, které je definováno v sekci "struktura" výstupní datovou sadu.
* Méně sloupců nebo více sloupců v "struktura" datová sada jímky než zadán v mapování.
* Duplicitní mapování.

#### <a name="explicit-column-mapping-example"></a>Příklad explicitního mapování sloupce

V této ukázce vstupní tabulka má strukturu a odkazuje na tabulku do místní databáze SQL.

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

V této ukázce výstupní tabulce obsahuje strukturu a odkazuje na tabulku ve službě Azure SQL Database.

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

Následující kód JSON určuje aktivitu kopírování v kanálu. Sloupce z namapované na sloupce v jímky zdroje (**columnMappings**) s použitím **translator** vlastnost.

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
            "columnMappings": 
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Pokud používáte syntaxe `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` zadat mapování sloupce, je stále podporovány jako-je.

**Mapování sloupců toku:**

![Tok mapování sloupce](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="schema-mapping"></a>mapování schématu

Mapování schématu platí při kopírování dat mezi hierarchické ve tvaru dat a tabulkové upravená data, například kopírování z MongoDB nebo rozhraní REST do textového souboru a kopírovat z SQL do rozhraní API MongoDB služby Azure Cosmos DB. Následující vlastnosti jsou podporovány v aktivitě kopírování `translator` části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type překladatele, aktivita kopírování musí být nastavená na: **TabularTranslator** | Ano |
| schemaMapping | Kolekce párů klíč hodnota, která představuje vztah mapování ze strany tabulkové hierarchické stranu.<br/>- **Klíč:** název sloupce tabulkových dat jako definovaný ve struktuře datové sady.<br/>- **Hodnota:** výraz cesty JSON pro každé pole extrahovat a mapování. U polí v kořenovém objektu začtěte s kořenem $, u polí uvnitř pole vybraného pomocí vlastnosti `collectionReference` začněte elementem pole.  | Ano |
| collectionReference | Pokud chcete iterovat a extrahovat data z objektů **uvnitř pole** pomocí stejného vzoru a převést na jeden řádek pro každý objekt, zadejte cestu JSON pole použít různé. Tato vlastnost je podporována pouze v případě, že je hierarchická data source. | Ne |

**Příklad: zkopírujte z MongoDB do SQL:**

Pokud například máte dokument MongoDB s následujícím obsahem: 

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

a chcete ho zkopírovat do tabulky Azure SQL v následujícím formátu data uvnitř pole linearizovat *(order_pd a order_price)* a křížové spojení se společnými kořenovými informacemi *(číslo, datum a města)* :

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Konfigurace pravidla mapování schématu jako podle následující ukázky JSON aktivity kopírování:

```json
{
    "name": "CopyFromMongoDBToSqlAzure",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number", 
                "orderDate": "$.date", 
                "order_pd": "prod", 
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapování datového typu

Aktivita kopírování provádí typy zdroje do jímky typy mapování s přístupem následující krok 2:

1. Převést z typů nativní zdroje Azure Data Factory dočasné datové typy
2. Převést na nativní jímky typu Azure Data Factory dočasné datové typy

Můžete najít mapování mezi nativní typ pro pomocný typ v části "Mapování datového typu" v tématu každý konektor.

### <a name="supported-data-types"></a>Podporované datové typy

Data Factory podporuje následující typy dočasné dat: Můžete zadat následující hodnoty při Konfigurace informací o typu v [struktury datové sady](concepts-datasets-linked-services.md#dataset-structure) konfigurace:

* Byte]
* Logická hodnota
* Datum a čas
* DateTimeOffset
* Desítkově
* Double
* Guid
* Int16
* Datový typ Int32
* Int64
* Jednoduchá
* Řetězec
* Časový interval

### <a name="explicit-data-type-conversion"></a>Převod explicitní datového typu

Při kopírování dat do data ukládá s pevné schéma, například SQL serveru/Oraclu, ve chvíli, kdy zdroje a jímky má jiný typ ve stejném sloupci, převod explicitní typ by měly být deklarovány na straně zdroje:

* Pro zdrojový soubor pro CSV nebo Avro, převod typu musí být deklarovány například prostřednictvím struktury zdrojových seznamem celý sloupec (na straně typ zdrojového sloupce název a datovou sadu jímky straně)
* Pro relační zdroj (například SQL/Oracle) by mělo být dosaženo převod typu pomocí přetypování explicitního typu v příkazu dotazu.

## <a name="when-to-specify-dataset-structure"></a>Zadání struktury"datové sady"

V následující scénáře, "struktura" v datové sadě se vyžaduje:

* Použití [převod explicitní datového typu](#explicit-data-type-conversion) pro souborové zdroje během kopírování (vstupní datová sada)
* Použití [mapování sloupců explicitní](#explicit-column-mapping) během kopírování (vstupní i výstupní datová sada)
* Kopírování z Dynamics 365 a CRM zdroje (vstupní datová sada)
* Kopírování do služby Cosmos DB jako vnořený objekt není zdrojem soubory JSON (výstupní datová sada)

V následující scénáře, doporučuje "struktura" v datové sadě:

* Kopírování z textového souboru bez hlavičky (vstupní datové sady). Můžete zadat názvy sloupců pro zarovnání se odpovídající sloupci jímky uložit z konfigurace mapování sloupců explicitní textového souboru.
* Kopírování z dat obchodů s aplikacemi s flexibilním schématem, například Azure tabulky/Cosmos DB (vstupní datová sada), zajistit očekávaná data (sloupce) namísto umožňují kopírování zkopíruje aktivity odvodit schéma založené na horní řádky během spuštění každé aktivity.


## <a name="next-steps"></a>Další postup
Zobrazit další články o aktivitě kopírování:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Zkopírovat aktivitu odolnost proti chybám](copy-activity-fault-tolerance.md)
- [Výkonem aktivity kopírování](copy-activity-performance.md)
