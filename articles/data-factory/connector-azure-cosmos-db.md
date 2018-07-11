---
title: Kopírování dat do a ze služby Azure Cosmos DB pomocí služby Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z podporované zdrojové úložiště dat do služby Azure Cosmos DB (nebo) ze služby Cosmos DB do jímky podporované úložišť pomocí služby Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
ms.openlocfilehash: 92b45c1038fd099926360dc80802ababf0e8ee93
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37052762"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Kopírování dat do nebo ze služby Azure Cosmos DB pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuální verze](connector-azure-cosmos-db.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z a do služby Azure Cosmos DB (rozhraní SQL API). Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Můžete kopírovat data ze služby Azure Cosmos DB do jakékoli podporovaného úložiště dat jímky nebo kopírování dat ze všech podporovaných zdrojů úložišť dat do služby Azure Cosmos DB. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor služby Azure Cosmos DB podporuje:

- Cosmos DB [rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Import/export dokumentů JSON jako-je nebo kopírování dat z/do tabulkové datové sady, například SQL databáze, soubory CSV atd.

Zkopírujte dokumenty s kódováním – je do a z souborů JSON nebo jiné kolekce Cosmos DB najdete v článku [Import/Export dokumentů JSON](#importexport-json-documents).

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní ke službě Azure Cosmos DB.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro služby Azure Cosmos DB, která je propojená se podporují následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **CosmosDb**. | Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k databázi Azure Cosmos DB. Všimněte si, že budete muset zadat informace o databázi v připojovacím řetězci podle následující ukázky. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje datové sady Azure Cosmos DB.

Pro kopírování dat z/do služby Azure Cosmos DB, nastavte vlastnost typ datové sady na **DocumentDbCollection**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ datové sady, musí být nastavena na: **DocumentDbCollection** |Ano |
| CollectionName |Název kolekce dokumentu Cosmos DB. |Ano |

**Příklad:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schéma službou Data Factory

Pro data bez schémat úložišť, jako je Azure Cosmos DB aktivita kopírování odvodí schéma v jednom z následujících způsobů. Proto pokud nechcete [import/export dokumentů JSON jako-je](#importexport-json-documents), osvědčeným postupem je zadání struktury dat v **struktura** oddílu.

*. Pokud určíte strukturu dat pomocí **struktura** vlastnost v definici datové sady, služba Data Factory respektuje tato struktura jako schéma. V takovém případě pokud řádku neobsahuje hodnotu pro sloupec, hodnotu null, poskytneme vám pro něj.
*. Pokud nezadáte strukturu dat s použitím **struktura** vlastnost v definici datové sady, služba Data Factory odvodí schéma pomocí prvního řádku v datech. V takovém případě pokud první řádek obsahuje úplného schématu, některé sloupce bude chybět ve výsledku operace kopírování.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastnosti podporované službou Azure Cosmos DB zdroje a jímky.

### <a name="azure-cosmos-db-as-source"></a>Azure Cosmos DB jako zdroje

Pro kopírování dat ze služby Azure Cosmos DB, nastavte typ zdroje v aktivitě kopírování do **DocumentDbCollectionSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na: **DocumentDbCollectionSource** |Ano |
| query |Zadejte dotaz služby Cosmos DB číst data.<br/><br/>Příklad: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadán, příkaz SQL, která se spustí: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciální znakem k označení, že je vnořená dokumentu a jak chcete flattern nastavte výsledek.<br/><br/>Například, pokud dotaz Cosmos DB vrátí vnořené výsledek `"Name": {"First": "John"}`, aktivitu kopírování, která bude určovat název sloupce jako "Name.First" s hodnotou "John" při nestedSeparator je tečka. |Ne (výchozí hodnota je tečka `.`) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>Azure Cosmos DB jako jímka

Ke zkopírování dat do služby Azure Cosmos DB, nastavte typ jímky v aktivitě kopírování do **DocumentDbCollectionSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování: **DocumentDbCollectionSink** |Ano |
| nestingSeparator |Je potřeba speciálního znaku v název zdrojového sloupce označíte daný vnořený dokument. <br/><br/>Například `Name.First` ve vstupní sadě struktura generuje následující strukturu JSON v dokumentu databáze Cosmos DB:`"Name": {"First": "[value maps to this column from source]"}` po nestedSeparator tečkou. |Ne (výchozí hodnota je tečka `.`) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Import/Export dokumentů JSON

Pomocí tohoto konektoru služby Cosmos DB, můžete snadno

* Importujte dokumentů JSON z různých zdrojů do služby Cosmos DB, včetně objektů Blob v Azure, Azure Data Lake Store a další souborové úložiště podporovaných službou Azure Data Factory.
* Exportujte dokumentů JSON do různých úložišť založená na souborech z collecton Cosmos DB.
* Zkopírujte dokumenty mezi dvě kolekce Cosmos DB jako-je.

Abyste dosáhli takové dogmaticky na schématu kopírování:

* Při použití nástroje pro kopírování dat, zkontrolujte **"exportovat jako-soubory JSON nebo kolekce Cosmos DB"** možnost.
* Při použití vytváření aktivit, nezadávejte oddílu "struktura" (neboli schéma) v Cosmos DB publikovaných ani vlastnost "nestingSeparator" ve službě Cosmos DB zdroje/jímky v aktivitě kopírování. Při importu z / export do souborů JSON v odpovídající datové úložiště souborů, zadejte typ formátu jako "JsonFormat" a konfigurace "filePattern" správně (viz [formátu JSON](supported-file-formats-and-compression-codecs.md#json-format) podrobné informace), potom nezadávejte struktura" "(neboli schéma) části a přeskočit zbývající nastavení formátu.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
