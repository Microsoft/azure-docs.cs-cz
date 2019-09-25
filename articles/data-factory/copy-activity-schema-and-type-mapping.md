---
title: Mapování schématu v aktivitě kopírování | Microsoft Docs
description: Přečtěte si, jak aktivita kopírování v Azure Data Factory mapuje schémata a datové typy ze zdrojových dat na data jímky při kopírování dat.
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
ms.openlocfilehash: b705123dc6492466c30b3c1ddaf4b330b0d684a1
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272267"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapování schématu v aktivitě kopírování

Tento článek popisuje, jak Azure Data Factory aktivita kopírování provádí mapování schématu a mapování typů dat ze zdrojových dat na data jímky při spouštění kopie dat.

## <a name="schema-mapping"></a>Mapování schématu

Mapování sloupce platí při kopírování dat ze zdroje do jímky. Ve výchozím nastavení jsou **zdrojová data mapování aktivit zkopírována do jímky podle názvů sloupců**. Můžete zadat [explicitní mapování](#explicit-mapping) pro přizpůsobení mapování sloupce podle vašich potřeb. Konkrétně aktivita kopírování:

1. Přečtěte si data ze zdroje a určete zdrojové schéma.
2. Použijte výchozí mapování sloupce pro mapování sloupců podle názvu nebo použijte explicitní mapování sloupce, pokud je zadáno.
3. Zápis dat do jímky

### <a name="explicit-mapping"></a>Explicitní mapování

Můžete zadat sloupce, které mají být namapovány na vlastnost `translator` aktivita kopírování – >  ->  `mappings` . Následující příklad definuje aktivitu kopírování v kanálu pro kopírování dat z textu s oddělovači do Azure SQL Database.

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

Následující vlastnosti jsou `translator` podporovány v  ->  `mappings` > objekt s `source` a `sink`:

| Vlastnost | Popis                                                  | Požadováno |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Název zdroje nebo sloupce jímky.                           | Ano      |
| ordinal  | Index sloupce. Začněte s 1. <br>Použít a vyžádat při použití oddělovače textu bez řádku záhlaví. | Ne       |
| path     | Výraz cesty JSON pro každé pole k extrakci nebo mapování Platí pro hierarchická data, např. MongoDB/REST.<br>Pro pole v rámci kořenového objektu začíná cesta JSON kořenem $;. pro pole uvnitř pole, které je `collectionReference` zvoleno vlastností, začíná cesta JSON od elementu pole. | Ne       |
| type     | Data Factory pomocný datový typ sloupce zdroj nebo jímka. | Ne       |
| culture  | Jazyková verze zdroje nebo sloupce jímky. <br>Použijte, pokud je `Datetime` typ `Datetimeoffset`nebo. Výchozí formát je `en-us`. | Ne       |
| format   | Řetězec formátu, který se má použít, `Datetime` Pokud `Datetimeoffset`je typ nebo. Informace o formátování hodnoty DateTime naleznete v tématu [Vlastní řetězce formátu data a času](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) . | Ne       |

Následující vlastnosti `translator` jsou podporovány  ->  `source` kromě objektu s a`sink`: `mappings`

| Vlastnost            | Popis                                                  | Požadováno |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Podporováno pouze v případě, že je zdrojem hierarchických dat například MongoDB/REST.<br>Pokud chcete iterovat a extrahovat data z objektů **uvnitř pole** pole se stejným vzorem a převést je na každý řádek na objekt, zadejte cestu JSON tohoto pole, aby se provedlo křížové použití. | Ne       |

### <a name="alternative-column-mapping"></a>Alternativní mapování sloupce

Můžete určit aktivitu kopírování – > `translator`  ->  `columnMappings` k mapování mezi daty tabelárních ve tvaru. V tomto případě je oddíl "Structure" vyžadován pro vstupní i výstupní datové sady. Mapování sloupce podporuje **mapování všech nebo dílčích sloupců ve struktuře zdrojové datové sady na všechny sloupce ve struktuře datové sady jímky**. Následující jsou chybové stavy, jejichž výsledkem je výjimka:

* Výsledek dotazu na zdrojové úložiště dat nemá název sloupce, který je zadaný v části struktura vstupní datové sady.
* Úložiště dat jímky (Pokud s předem definovaným schématem) nemá název sloupce, který je zadaný v části struktura výstupní datové sady.
* Buď méně sloupců, nebo více sloupců ve struktuře datové sady jímky, než je uvedeno v mapování.
* Duplicitní mapování

V následujícím příkladu má vstupní datová sada strukturu a odkazuje na tabulku v místní databázi Oracle.

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

V této ukázce má výstupní datová sada strukturu a odkazuje na tabulku v Salesfoce.

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

Následující JSON definuje aktivitu kopírování v kanálu. Sloupce ze zdroje mapované na sloupce v jímky pomocí vlastnosti **Translator** -> **ColumnMappings** .

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

Pokud používáte syntaxi `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` pro k určení mapování sloupce, je stále podporováno tak, jak je.

### <a name="alternative-schema-mapping"></a>Alternativní mapování schématu

Můžete určit aktivitu kopírování – > `translator`  ->  `schemaMapping` k mapování mezi hierarchicky naformátovaná data a tabulková data, například kopírování z MongoDB/REST do textového souboru a kopírování z Oracle do Azure Cosmos DB rozhraní API pro MongoDB. V části aktivita `translator` kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type pro překladatele aktivity kopírování musí být nastavená na: **TabularTranslator** | Ano |
| schemaMapping | Kolekce párů klíč-hodnota, která představuje vztah mapování **ze strany zdroje na stranu jímky**.<br/>- **Key:** představuje zdroj. V poli **tabelární zdroj**zadejte název sloupce definovaný ve struktuře datové sady. u **hierarchického zdroje**zadejte výraz cesty JSON pro každé pole, které se má extrahovat a mapovat.<br>- **Value:** představuje jímky. Pro **tabulkovou jímku**zadejte název sloupce definovaný ve struktuře datové sady. u **hierarchické jímky**zadejte výraz cesty JSON pro každé pole, které chcete extrahovat a mapovat. <br>V případě hierarchických dat pro pole v části kořenový objekt začíná cesta JSON kořenem $;. pro pole uvnitř pole, které je `collectionReference` zvoleno vlastností, začíná cesta JSON od elementu pole.  | Ano |
| collectionReference | Pokud chcete iterovat a extrahovat data z objektů **uvnitř pole** pole se stejným vzorem a převést je na každý řádek na objekt, zadejte cestu JSON tohoto pole, aby se provedlo křížové použití. Tato vlastnost je podporována pouze v případě, že jsou hierarchická data zdrojem. | Ne |

**Příklad: kopírování z MongoDB do Oracle:**

Například pokud máte MongoDB dokument s následujícím obsahem:

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

a chcete ho zkopírovat do tabulky Azure SQL v následujícím formátu, a to tak, že sloučíte data uvnitř pole *(order_pd a order_price)* a spojíte se společnými kořenovými informacemi *(číslo, datum a město)* :

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Nakonfigurujte pravidlo mapování schématu jako následující ukázku JSON aktivity kopírování:

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

## <a name="data-type-mapping"></a>Mapování datových typů

Aktivita kopírování provádí typy zdrojů pro mapování typů jímky pomocí následujícího přístupu ke dvěma krokům:

1. Převést z nativních typů zdroje na Azure Data Factory dočasné datové typy
2. Převést z Azure Data Factory dočasných datových typů na nativní typ jímky

Mapování mezi nativním typem a dočasným typem najdete v části mapování datových typů v každém konektoru.

### <a name="supported-data-types"></a>Podporované datové typy

Data Factory podporuje následující dočasné datové typy: Při konfiguraci informací o typu v konfiguraci [struktury datové sady](concepts-datasets-linked-services.md#dataset-structure-or-schema) můžete zadat následující hodnoty:

* Byte[]
* Logická hodnota
* Datetime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* Řetězec
* Timespan

## <a name="next-steps"></a>Další kroky
Zobrazit další články o aktivitě kopírování:

- [Přehled aktivit kopírování](copy-activity-overview.md)
