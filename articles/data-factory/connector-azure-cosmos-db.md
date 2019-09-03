---
title: Kopírování dat do nebo ze služby Azure Cosmos DB (rozhraní SQL API) pomocí služby Data Factory | Dokumentace Microsoftu
description: Naučte se, jak kopírovat data z podporovaných zdrojových úložišť dat do nebo z Azure Cosmos DB (SQL API) na podporovaná úložiště jímky pomocí Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 3082c568b3ce3fa5199c7a7d0d082db36720d293
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233031"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopírování dat do nebo ze služby Azure Cosmos DB (rozhraní SQL API) pomocí služby Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuální verze](connector-azure-cosmos-db.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro kopírování dat z a do služby Azure Cosmos DB (rozhraní SQL API). Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

>[!NOTE]
>Tento konektor podporuje pouze kopírování dat do a z rozhraní SQL API služby Cosmos DB. MongoDB API najdete v tématu [konektor pro rozhraní api Azure Cosmos DB pro MongoDB](connector-azure-cosmos-db-mongodb-api.md). Jiné typy rozhraní API se teď nepodporují.

## <a name="supported-capabilities"></a>Podporované funkce

Data můžete kopírovat z Azure Cosmos DB (SQL API) do libovolného podporovaného úložiště dat jímky nebo kopírovat data z libovolného podporovaného zdrojového úložiště dat do Azure Cosmos DB (SQL API). Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

Konektor Azure Cosmos DB (SQL API) můžete použít k těmto akcím:

- Kopírování dat z a do služby Azure Cosmos DB [rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Zapsat do služby Azure Cosmos DB jako **vložit** nebo **upsert**.
- Import a export dokumentů JSON jako-je, nebo zkopírovat data z nebo do tabulkové datové sady. Mezi příklady patří SQL database a soubor CSV. Pokud chcete kopírovat dokumenty tak, jak jsou, nebo ze souborů JSON nebo do jiné kolekce Azure Cosmos DB, přečtěte si téma import nebo export dokumentů JSON.

Integruje do služby Data Factory [knihovny prováděcí modul služby Azure Cosmos DB hromadné](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) poskytovat nejlepší výkon při zápisu do služby Azure Cosmos DB.

> [!TIP]
> [Migrace dat videa](https://youtu.be/5-SRNiC_qOU) vás provede kroky pro kopírování dat z Azure Blob storage do služby Azure Cosmos DB. Video také popisuje rozhodnutí optimalizace výkonu pro příjem dat do služby Azure Cosmos DB obecně.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit, které jsou specifické pro Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure Cosmos DB (SQL API) se podporují tyto vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **CosmosDb**. | Ano |
| connectionString |Zadejte informace potřebné k připojení k databázi Azure Cosmos DB.<br />**Poznámka:** V připojovacím řetězci je nutné zadat informace o databázi, jak je znázorněno v následujících příkladech. <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Můžete také umístit klíč účtu do Azure Key Vault a `accountKey` získat konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. |Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace v Azure nebo místního prostředí integration runtime. Pokud není tato vlastnost určena, použije se výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
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

**Příklad: klíč účtu úložiště v Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

V této části najdete seznam vlastností, které datová sada pro Azure Cosmos DB (SQL API) podporuje. 

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z nebo do Azure Cosmos DB (SQL API), nastavte vlastnost **Type** datové sady na **DocumentDbCollection**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost datové sady **DocumentDbCollection**. |Ano |
| collectionName |Název kolekce dokumentů Azure Cosmos DB. |Ano |

**Příklad**

```json
{
    "name": "CosmosDbSQLAPIDataset",
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

Pro úložiště dat bez schématu jako je Azure Cosmos DB aktivita kopírování odvodí schéma v jednom ze způsobů, jak je popsáno v následujícím seznamu. Pokud nechcete [import a export dokumentů JSON jako-je](#import-or-export-json-documents), osvědčeným postupem je zadání struktury dat v **struktura** oddílu.

* Pokud určíte strukturu dat pomocí **struktura** vlastnost v definici datové sady Data Factory respektuje tato struktura jako schéma. 

    Pokud řádek neobsahuje hodnotu pro sloupec, hodnoty null se poskytuje pro hodnotu sloupce.
* Pokud nezadáte strukturu dat s použitím **struktura** vlastnost v definici datové sady, služba Data Factory odvodí schéma pomocí prvního řádku v datech. 

    Pokud první řádek neobsahuje úplného schématu, některé sloupce budou chybět ve výsledku operace kopírování.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností, které podporuje zdroj Azure Cosmos DB (SQL API) a podpora jímky.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) jako zdroj

Chcete-li kopírovat data z Azure Cosmos DB (SQL API), nastavte typ **zdroje** v aktivitě kopírování na **DocumentDbCollectionSource**. 

Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **DocumentDbCollectionSource**. |Ano |
| query |Zadejte dotaz služby Azure Cosmos DB číst data.<br/><br/>Příklad:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadán, je proveden tento příkaz SQL: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciální znak, který označuje, že je vnořená dokumentu a jak sloučit sadu výsledků dotazu.<br/><br/>Například, pokud dotaz služby Azure Cosmos DB vrátí vnořené výsledek `"Name": {"First": "John"}`, aktivitu kopírování, která identifikuje název sloupce jako `Name.First`, s hodnotou "John", když **nestedSeparator** hodnotu **.** (tečka). |Ne<br />(výchozí hodnota je **.** (tečka)) |

**Příklad**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) jako jímka

Chcete-li kopírovat data do Azure Cosmos DB (SQL API), nastavte typ **jímky** v aktivitě kopírování na **DocumentDbCollectionSink**. 

Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost jímky aktivity kopírování musí být nastavena na **DocumentDbCollectionSink**. |Ano |
| writeBehavior |Popisuje, jak zapsat data do služby Azure Cosmos DB. Povolené hodnoty: **vložit** a **upsert**.<br/><br/>Chování **upsert** nahrazuje dokumentu, pokud dokument se stejným ID už existuje; v opačném případě vložit dokument.<br /><br />**Poznámka:** Data Factory automaticky vygeneruje ID pro dokument, pokud ID není zadáno v původním dokumentu nebo mapování sloupce. To znamená, že musíte zajistit, aby, pro **upsert** fungovat podle očekávání, váš dokument nemá identifikátor. |Ne<br />(výchozí hodnota je **vložit**) |
| writeBatchSize | Datová továrna používá [knihovny prováděcí modul služby Azure Cosmos DB hromadné](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) zapsat data do služby Azure Cosmos DB. Vlastnost **writeBatchSize** určuje velikost dokumentů, které ADF zajišťují do knihovny. Můžete zkusit zvýšit hodnotu **writeBatchSize** ke zlepšení výkonu a snížení hodnoty, pokud váš dokument je velká velikost – viz následující tipy. |Ne<br />(výchozí hodnota je **10 000**) |
| nestingSeparator |Speciálního znaku v **zdroj** název sloupce, který označuje, že je potřeba vnořených dokumentů. <br/><br/>Například `Name.First` ve vstupní sadě struktura generuje následující strukturu JSON v Azure Cosmos DB dokumentů, kdy **nestedSeparator** je **.** (tečka): `"Name": {"First": "[value maps to this column from source]"}`  |Ne<br />(výchozí hodnota je **.** (tečka)) |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je například Cosmos DB ru, pro optimalizaci výkonu a doporučení pro kopírování. Pokud se s tímto chováním obáváte, `true` určete, jestli ho chcete vypnout. | Ne (výchozí nastavení `false`je) |

>[!TIP]
>Cosmos DB omezí velikost jednoho požadavku až 2MB. Vzorec je žádost o velikosti = velikost jednoho dokumentu * velikost dávky zápisu. Pokud dosáhnete o tom, že chyba **"Požadavku je příliš velký."** , **snížit `writeBatchSize` hodnotu** v konfiguraci jímky kopírování.

**Příklad**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
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
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-or-export-json-documents"></a>Import a export dokumentů JSON

Pomocí tohoto konektoru Azure Cosmos DB (SQL API) můžete snadno:

* Importujte dokumenty JSON z různých zdrojů do služby Azure Cosmos DB, včetně z úložiště objektů Blob v Azure, Azure Data Lake Store a další souborové úložiště, které podporuje Azure Data Factory.
* Exportujte dokumentů JSON z kolekce Azure Cosmos DB do různých úložišť založená na souborech.
* Zkopírujte dokumenty mezi dvě kolekce Azure Cosmos DB jako-je.

K dosažení dogmaticky na schématu kopírování:

* Při použití nástroje pro kopírování dat vyberte **exportovat jako-soubory JSON nebo kolekce Cosmos DB** možnost.
* Použijete-li vytváření aktivit, nezadávejte **struktura** (také nazývané *schématu*) část v datové sadě služby Azure Cosmos DB. Také, nezadávejte **nestingSeparator** vlastnosti v Azure Cosmos DB zdrojem nebo jímkou v aktivitě kopírování. Když importovat nebo exportovat soubory JSON v odpovídajícím souboru uložit datovou sadu, zadejte **formátu** zadejte jako **JsonFormat** a nakonfigurovat **filePattern** jako podle [formátu JSON](supported-file-formats-and-compression-codecs.md#json-format) oddílu. Potom nezadávejte **struktura** části a přeskočit zbývající část nastavení formátu.

## <a name="next-steps"></a>Další postup

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
