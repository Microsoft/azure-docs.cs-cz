---
title: Mapování schématu v aktivitě kopírování | Dokumentace Microsoftu
description: Informace o způsobu mapování schémat a datové typy z datového zdroje do jímky dat při kopírování dat aktivita kopírování ve službě Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9108f83e854b51720c64c5a74a828543cc5e7688
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64875805"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapování schématu v aktivitě kopírování

Tento článek popisuje, jak funguje Azure Data Factory za kopírování schématu mapování a mapování datového typu z datového zdroje do jímky dat při spuštění kopírovat data.

## <a name="schema-mapping"></a>mapování schématu

Mapování sloupců platí při kopírování dat ze zdroje do jímky. Ve výchozím nastavení, aktivita kopírování **mapování dat zdroje do jímky podle názvů sloupců**. Můžete zadat [explicitního mapování](#explicit-mapping) přizpůsobit podle svých potřeb mapování sloupců. Přesněji řečeno aktivita kopírování:

1. Číst data ze zdroje a určit schématu zdroje
2. Použít výchozí mapování sloupců pro mapování sloupců podle názvu nebo použít explicitní sloupec mapování, pokud zadaný.
3. Zápis dat do jímky

### <a name="explicit-mapping"></a>Explicitní mapování

Můžete určit sloupce, které chcete mapování v aktivitě kopírování -> `translator`  ->  `mappings` vlastnost. Následující příklad definuje aktivitu kopírování v kanálu pro kopírování dat z obsahující text oddělený znaky do služby Azure SQL Database.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

Následující vlastnosti jsou podporovány v rámci `translator`  ->  `mappings` -> objekt s `source` a `sink`:

| Vlastnost | Popis                                                  | Požaduje se |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Název sloupce zdrojem nebo jímkou.                           | Ano      |
| ordinal  | Index sloupce. Začněte s 1. <br>Použít a vyžaduje při použití oddělený text bez záhlaví řádku. | Ne       |
| path     | Výraz cesty JSON pro každé pole pro rozbalení nebo mapy. Platí pro hierarchických dat třeba MongoDB nebo rozhraní REST.<br>U polí v kořenovém objektu cesta JSON začíná s kořenem $; u polí uvnitř pole vybraného pomocí `collectionReference` vlastnost, cesta JSON začíná od prvku pole. | Ne       |
| type     | Data Factory dočasné datový typ sloupce zdrojem nebo jímkou. | Ne       |
| culture  | Jazyková verze, ve sloupci zdrojem nebo jímkou. <br>Použít, když je typ `Datetime` nebo `Datetimeoffset`. Výchozí formát je `en-us`. | Ne       |
| format   | Formátovací řetězec se použije, když je typ `Datetime` nebo `Datetimeoffset`. Odkazovat na [vlastní data a řetězce formátu časových](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) o tom, jak formátovat datum a čas. | Ne       |

Následující vlastnosti jsou podporovány v rámci `translator`  ->  `mappings` kromě objekt s `source` a `sink`:

| Vlastnost            | Popis                                                  | Požaduje se |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Podporováno, pouze pokud je hierarchická data třeba MongoDB nebo rozhraní REST zdroje.<br>Pokud chcete iterovat a extrahovat data z objektů **uvnitř pole** pomocí stejného vzoru a převést na jeden řádek pro každý objekt, zadejte cestu JSON pole použít různé. | Ne       |

### <a name="alternative-column-mapping"></a>Mapování alternativních sloupce

Můžete zadat kopírování aktivita -> `translator`  ->  `columnMappings` k mapování mezi daty tabulkové ve tvaru. V tomto případě je požadované pro vstupní a výstupní datové sady část "struktura". Podporuje mapování sloupce **mapování všech nebo podmnožinu sloupců v datové sadě zdroj "struktura" všech sloupců v datové sadě jímky "struktura"** . Následují chybových podmínek, za následek výjimku:

* Zdrojového úložiště dat dotazu, že výsledek nemá název sloupce, které je definováno v sekci "struktura" vstupní datové sady.
* Úložiště dat jímky (Pokud se předem definované schéma) nemá název sloupce, které je definováno v sekci "struktura" výstupní datovou sadu.
* Méně sloupců nebo více sloupců v "struktura" datová sada jímky než zadán v mapování.
* Duplicitní mapování.

V následujícím příkladu se vstupní datová sada obsahuje strukturu a odkazuje na tabulku v databázi Oracle místní.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

V této ukázce obsahuje strukturu výstupní datová sada a odkazuje na tabulku v Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Následující kód JSON určuje aktivitu kopírování v kanálu. Sloupce z namapované na sloupce v jímky pomocí zdroje **translator** -> **columnMappings** vlastnost.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
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

### <a name="alternative-schema-mapping"></a>Mapování alternativních schématu

Můžete zadat kopírování aktivita -> `translator`  ->  `schemaMapping` pro mapování mezi hierarchické ve tvaru dat a tabulkové upravená data, například kopírování z MongoDB nebo rozhraní REST do textového souboru a kopírovat z Oraclu na rozhraní API služby Azure Cosmos DB pro MongoDB. Následující vlastnosti jsou podporovány v aktivitě kopírování `translator` části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type překladatele, aktivita kopírování musí být nastavená na: **TabularTranslator** | Ano |
| schemaMapping | Kolekce párů klíč hodnota, která představuje vztah mapování **ze strany zdroje do jímky straně**.<br/>- **Klíč:** představuje zdroj. Pro **tabulkové zdroje**, zadejte název sloupce, jak je definováno ve struktuře datové sady, pro **hierarchické zdroj**, zadejte výraz cesty JSON pro každé pole extrahovat a mapování.<br>- **Hodnota:** představuje jímky. Pro **tabulky jímky**, zadejte název sloupce, jak je definováno ve struktuře datové sady, pro **hierarchické jímky**, zadejte výraz cesty JSON pro každé pole extrahovat a mapování. <br>V případě hierarchických dat, u polí v kořenovém objektu JSON cesta začínající znakem $ kořenové; u polí uvnitř pole vybraného pomocí `collectionReference` vlastnost, cesta JSON začíná od prvku pole.  | Ano |
| collectionReference | Pokud chcete iterovat a extrahovat data z objektů **uvnitř pole** pomocí stejného vzoru a převést na jeden řádek pro každý objekt, zadejte cestu JSON pole použít různé. Tato vlastnost je podporována pouze v případě, že je hierarchická data source. | Ne |

**Příklad: zkopírujte z MongoDB do Oracle:**

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
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
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

Data Factory podporuje následující typy dočasné dat: Můžete zadat následující hodnoty při Konfigurace informací o typu v [struktury datové sady](concepts-datasets-linked-services.md#dataset-structure-or-schema) konfigurace:

* Byte[]
* Boolean
* Datetime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

## <a name="next-steps"></a>Další postup
Zobrazit další články o aktivitě kopírování:

- [Přehled aktivit kopírování](copy-activity-overview.md)
