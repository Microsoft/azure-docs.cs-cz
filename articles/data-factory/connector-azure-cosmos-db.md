---
title: Kopírování dat do nebo z databáze Cosmos Azure pomocí služby Data Factory | Microsoft Docs
description: Zjistěte, jak zkopírovat data z podporované zdrojové úložiště dat k databázi Cosmos Azure (nebo) z databáze Cosmos na podporované podřízený úložiště pomocí služby Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 58e1c88629c21940e09efd6832d536c0b2b47ace
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Kopírovat data z databáze Cosmos Azure pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-azure-documentdb-connector.md)
> * [Verze 2 – Preview](connector-azure-cosmos-db.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z a do Azure Cosmos databáze (SQL API). Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [connnector Azure Cosmos DB v V1](v1/data-factory-azure-documentdb-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Můžete zkopírovat data z Azure Cosmos DB do úložiště dat žádné podporované podřízený nebo zkopírování dat z jakékoli úložiště podporované zdroje dat do Azure Cosmos DB. Seznam úložišť dat jako zdroje nebo jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Azure Cosmos DB podporuje:

- Cosmos DB [rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Import/export dokumentů JSON jako-je nebo kopírování dat z/do tabulkové datové sady například databáze SQL, souborů CSV atd.

Zkopírujte dokumenty jako-je do nebo ze soubory JSON nebo jiné Cosmos DB kolekce najdete v části [dokumentů JSON importu a exportu](#importexport-json-documents).

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které se používají k definování konkrétní entity služby Data Factory pro Azure Cosmos DB.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Azure DB Cosmos propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **CosmosDb**. | Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k databázi Azure Cosmos DB. Všimněte si, že budete muset zadat informace o databázi v připojovacím řetězci jako následující ukázka. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje Azure Cosmos DB datovou sadu.

Ke zkopírování dat z/do Azure Cosmos DB, nastavte vlastnost typu datové sady, která **DocumentDbCollection**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **DocumentDbCollection** |Ano |
| Název_kolekce |Název kolekce dokumentů Cosmos DB. |Ano |

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

Aktivita kopírování pro data bez schémat úložiště, jako je například Azure Cosmos DB, odvodí schéma v jednom z následujících způsobů. Proto pokud nechcete [importu a exportu dokumentů JSON jako-je](#importexport-json-documents), osvědčeným postupem je určit strukturu dat v **struktura** části.

1. Pokud zadáte strukturu dat pomocí **struktura** vlastnost v definici datové sady, služba Data Factory ctí tato struktura jako schéma. V takovém případě Pokud řádek neobsahuje hodnotu pro sloupec, bude poskytnuta hodnota null pro ni.
2. Pokud nezadáte strukturu dat pomocí **struktura** vlastnost v definici datové sady, služba Data Factory odvodí schématu pomocí prvního řádku v datech. V takovém případě pokud první řádek neobsahuje úplnou schéma, některé sloupce budou chybět ve výsledku operace kopírování.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje Azure Cosmos DB zdroj a jímka.

### <a name="azure-cosmos-db-as-source"></a>Azure DB Cosmos jako zdroj

Ke zkopírování dat z Azure Cosmos DB, nastavte typ zdroje v aktivitě kopírování do **DocumentDbCollectionSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **DocumentDbCollectionSource** |Ano |
| query |Zadejte dotaz Cosmos DB číst data.<br/><br/>Příklad: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadaný příkaz jazyka SQL, který se spustí: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciální znak indikující, že dokument je vnořený a jak chcete flattern nastavte výsledek.<br/><br/>Například, pokud Cosmos DB dotaz vrací vnořené výsledek `"Name": {"First": "John"}`, aktivity kopírování bude určovat název sloupce jako "Name.First" s hodnotou "Jan" při nestedSeparator je tečkou. |Ne (výchozí hodnota je tečkou `.`) |

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

### <a name="azure-cosmos-db-as-sink"></a>Azure DB Cosmos jako jímku

Ke zkopírování dat do Azure Cosmos DB, nastavte typ jímky v aktivitě kopírování do **DocumentDbCollectionSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ jímky kopie aktivity musí nastavena: **DocumentDbCollectionSink** |Ano |
| nestingSeparator |Budete potřebovat speciální znak v názvu sloupce zdroj označíte, že vnořených dokumentů. <br/><br/>Například `Name.First` ve výstupní datovou sadu struktura generuje následující strukturu JSON v dokumentu Cosmos DB:`"Name": {"First": "[value maps to this column from source]"}` po nestedSeparator tečkou. |Ne (výchozí hodnota je tečkou `.`) |

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

## <a name="importexport-json-documents"></a>Dokumenty JSON importu a exportu

Pomocí tohoto konektoru Cosmos DB, můžete snadno

* Importujte dokumenty JSON z různých zdrojů do Cosmos databáze, včetně objektů Blob v Azure, Azure Data Lake Store a jiných úložišť na základě souborů podporovaných službou Azure Data Factory.
* Exportujte dokumenty JSON ze collecton Cosmos databáze do různých úložišť na základě souborů.
* Zkopírujte dokumenty mezi dvě kolekce Cosmos DB jako-je.

K dosažení taková vázané na schéma kopie:

- V databázi Cosmos datových sad, nezadávejte v části "struktura"; a v aktivitě kopírování Cosmos DB zdroj/jímka, nezadávejte vlastnost "nestingSeparator".
- Při importu ze / export do formátu JSON souborů v sadě odpovídající soubor úložiště dat, zadejte typ formátu jako "JsonFormat" a konfigurace "filePattern" správně (najdete v části [formátu JSON](supported-file-formats-and-compression-codecs.md#json-format) podrobnosti), pak nezadávejte strukturu" "tématu a přeskočit nastavení formátu rest.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
