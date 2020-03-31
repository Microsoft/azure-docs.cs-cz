---
title: Mapování schématu v aktivitě kopírování
description: Zjistěte, jak aktivita kopírování v Azure Data Factory mapuje schémata a datové typy ze zdrojových dat na data jímky při kopírování dat.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 9ae07e2a471cc417b467092a2616a5a0cdafb1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260810"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapování schématu v aktivitě kopírování

Tento článek popisuje, jak aktivita kopírování Azure Data Factory provádí mapování schématu a mapování datového typu ze zdrojových dat do jímky dat při provádění kopírování dat.

## <a name="schema-mapping"></a>Mapování schématu

Mapování sloupců platí při kopírování dat ze zdroje do jímky. Ve výchozím nastavení zkopírujte zdrojová data mapování aktivit **do jímky podle názvů sloupců**. Můžete zadat [explicitní mapování](#explicit-mapping) pro přizpůsobení mapování sloupců na základě vašich potřeb. Přesněji řečeno, kopírování aktivity:

1. Čtení dat ze zdroje a určení zdrojového schématu
2. Použijte výchozí mapování sloupců k mapování sloupců podle názvu nebo použijte explicitní mapování sloupců, pokud je zadáno.
3. Zapsat data do jímky

### <a name="explicit-mapping"></a>Explicitní mapování

Sloupce, které chcete mapovat, můžete `translator`  ->  `mappings` určit ve vlastnosti aktivity ->. Následující příklad definuje aktivitu kopírování v kanálu ke kopírování dat z odděleného textu do Azure SQL Database.

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

Následující vlastnosti jsou `translator`  ->  `mappings` podporovány `source` pod `sink`objektem -> s a :

| Vlastnost | Popis                                                  | Požaduje se |
| -------- | ------------------------------------------------------------ | -------- |
| jméno     | Název zdrojového nebo jímanového sloupce.                           | Ano      |
| Pořadové  | Index sloupce. Začněte s 1. <br>Použít a vyžadovat při použití odděleného textu bez řádku záhlaví. | Ne       |
| cesta     | Výraz cesty JSON pro každé pole, které chcete extrahovat nebo mapovat. Požádejte o hierarchická data, např.<br>Pro pole pod kořenovým objektem začíná cesta JSON kořenem $; pro pole uvnitř pole `collectionReference` zvoleného vlastností začíná cesta JSON od prvku pole. | Ne       |
| type     | Dočasný datový typ zdroje nebo jímky datové hotova. | Ne       |
| jazyková verze  | Jazyková verze zdrojového nebo jímanového sloupce. <br>Použít, pokud `Datetime` `Datetimeoffset`je typ nebo . Výchozí formát je `en-us`. | Ne       |
| formát   | Formátovací řetězec, který `Datetime` se `Datetimeoffset`má použít při použití typu nebo . Informace o formátování data a času naleznete v [podrobnostech](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) o formátování data. | Ne       |

Následující vlastnosti jsou `translator`  ->  `mappings` podporovány pod `source` `sink`kromě objektu s a :

| Vlastnost            | Popis                                                  | Požaduje se |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Podporováno pouze v případě, že jsou zdrojem hierarchická data, např.<br>Pokud chcete iterát a extrahovat data z objektů **uvnitř pole pole** se stejným vzorkem a převést na řádek na objekt, zadejte cestu JSON tohoto pole, které chcete provést křížové použití. | Ne       |

### <a name="alternative-column-mapping"></a>Alternativní mapování sloupců

Můžete určit aktivitu `translator`  ->  `columnMappings` kopírování -> mapovat mezi daty ve tvaru tabulkového tvaru . V tomto případě je požadována část "struktura" pro vstupní i výstupní datové sady. Mapování sloupců podporuje **mapování všech nebo podmnožiny sloupců ve zdrojové datové sadě "struktura" na všechny sloupce v datové sadě jímky "struktura"**. Následují chybové stavy, které vedou k výjimce:

* Výsledek dotazu na úložiště zdrojových dat nemá název sloupce, který je určen v části "struktura" vstupní datové sady.
* Úložiště dat jímky (pokud s předdefinovaným schématem) nemá název sloupce, který je zadán v části "struktura" výstupní datové sady.
* Buď méně sloupců nebo více sloupců v "struktuře" datové sady jímky, než je uvedeno v mapování.
* Duplicitní mapování.

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

V této ukázce má výstupní datová sada strukturu a ukazuje na tabulku v salesfoce.

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

Následující JSON definuje aktivitu kopírování v kanálu. Sloupce ze zdroje mapovány na sloupce v jímce pomocí **translator** -> **columnMappings** vlastnost.

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

Pokud používáte syntaxi `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` k určení mapování sloupců, je stále podporována tak, jak je.

### <a name="alternative-schema-mapping"></a>Mapování alternativního schématu

Můžete zadat aktivitu `translator`  ->  `schemaMapping` kopírování -> mapovat mezi hierarchickými daty a tabulkovými daty, například kopírování z MongoDB/REST do textového souboru a kopírování z Oracle do rozhraní API Azure Cosmos DB pro MongoDB. V části aktivity `translator` kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type překladače aktivity kopírování musí být nastavena **na: TabularTranslator** | Ano |
| mapování schématu | Kolekce párů klíč hodnota, která představuje vztah mapování **ze strany zdroje do jímky .**<br/>- **Klíč:** představuje zdroj. Pro **tabulkový zdroj**zadejte název sloupce, jak je definován ve struktuře datové sady; pro **hierarchický zdroj**zadejte výraz cesty JSON pro každé pole, které chcete extrahovat a mapovat.<br>- **Hodnota:** představuje jímky. Pro **tabulkový jímku**zadejte název sloupce, jak je definován ve struktuře datové sady; pro **hierarchický jímka**zadejte výraz cesty JSON pro každé pole, které chcete extrahovat a mapovat. <br>V případě hierarchických dat pro pole pod kořenovým objektem začíná cesta JSON kořenovou $; pro pole uvnitř pole `collectionReference` zvoleného vlastností začíná cesta JSON od prvku pole.  | Ano |
| collectionReference | Pokud chcete iterát a extrahovat data z objektů **uvnitř pole pole** se stejným vzorkem a převést na řádek na objekt, zadejte cestu JSON tohoto pole, které chcete provést křížové použití. Tato vlastnost je podporována pouze v případě, že je zdrojem hierarchická data. | Ne |

**Příklad: kopírování z MongoDB do společnosti Oracle:**

Pokud máte například dokument MongoDB s následujícím obsahem:

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

a chcete ji zkopírovat do tabulky Azure SQL v následujícím formátu sloučením dat uvnitř pole *(order_pd a order_price)* a křížovým spojením se společnými kořenovými informacemi *(číslo, datum a město):*

| Ordernumber | Datumobjednávky | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Nakonfigurujte pravidlo mapování schématu jako následující ukázku aktivity kopírování JSON:

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
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapování datových typů

Aktivita kopírování provádí typy zdrojů pro mapování typů jímek s následujícím dvoustupňovým přístupem:

1. Převod z nativních typů zdrojů na dočasné datové typy Azure Data Factory
2. Převést z dočasných datových typů Azure Data Factory na nativní typ jímky

Mapování mezi nativním typem a dočasným typem najdete v části "Mapování datového typu" v každém tématu konektoru.

### <a name="supported-data-types"></a>Podporované datové typy

Data Factory podporuje následující dočasné datové typy: Při konfiguraci informací o typu v konfiguraci [struktury datové sady](concepts-datasets-linked-services.md#dataset-structure-or-schema) můžete zadat níže uvedené hodnoty:

* Bajt[]
* Logická hodnota
* Datum a čas
* Datetimeoffset
* Desetinné číslo
* Double
* Identifikátor GUID
* Int16
* Int32
* Int64
* Single
* Řetězec
* Časový interval

## <a name="next-steps"></a>Další kroky
Podívejte se na další články aktivity kopírování:

- [Kopírovat přehled aktivit](copy-activity-overview.md)
