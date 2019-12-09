---
title: Kopírování a transformace dat v Azure Cosmos DB (SQL API)
description: Naučte se, jak kopírovat data do a z Azure Cosmos DB (SQL API) a transformovat data v Azure Cosmos DB (SQL API) pomocí Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/13/2019
ms.openlocfilehash: bf24c12e8f1e5b7ee5c529ebffa6c15dd8acbcfc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913408"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopírování a transformace dat v Azure Cosmos DB (SQL API) pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuální verze](connector-azure-cosmos-db.md)

V tomto článku se naučíte, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do Azure Cosmos DB (SQL API) a jak transformovat data v Azure Cosmos DB (SQL API) pomocí toku dat. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

>[!NOTE]
>Tento konektor podporuje pouze Cosmos DB rozhraní SQL API. MongoDB API najdete v tématu [konektor pro rozhraní api Azure Cosmos DB pro MongoDB](connector-azure-cosmos-db-mongodb-api.md). Jiné typy rozhraní API se teď nepodporují.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure Cosmos DB (SQL API) se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Pro aktivitu kopírování podporuje tato Azure Cosmos DBová konektorová rozhraní SQL API:

- Kopírování dat z a do služby Azure Cosmos DB [rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Zapsat do služby Azure Cosmos DB jako **vložit** nebo **upsert**.
- Import a export dokumentů JSON jako-je, nebo zkopírovat data z nebo do tabulkové datové sady. Mezi příklady patří SQL database a soubor CSV. Pokud chcete kopírovat dokumenty tak, jak jsou, nebo ze souborů JSON nebo do jiné kolekce Azure Cosmos DB, přečtěte si téma import nebo export dokumentů JSON.

Integruje do služby Data Factory [knihovny prováděcí modul služby Azure Cosmos DB hromadné](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) poskytovat nejlepší výkon při zápisu do služby Azure Cosmos DB.

> [!TIP]
> [Migrace dat videa](https://youtu.be/5-SRNiC_qOU) vás provede kroky pro kopírování dat z Azure Blob storage do služby Azure Cosmos DB. Video také popisuje rozhodnutí optimalizace výkonu pro příjem dat do služby Azure Cosmos DB obecně.

## <a name="get-started"></a>Začít

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit, které jsou specifické pro Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure Cosmos DB (SQL API) se podporují tyto vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **CosmosDb**. | Ano |
| connectionString |Zadejte informace potřebné k připojení k databázi Azure Cosmos DB.<br />**Poznámka:** : musíte zadat informace o databázi v připojovacím řetězci, jak je znázorněno v následující příklady. <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Můžete také umístit klíč účtu do Azure Key Vault a načíst `accountKey` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. |Ano |
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md).

Pro datovou sadu Azure Cosmos DB (SQL API) jsou podporovány následující vlastnosti: 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** datové sady musí být nastavená na **CosmosDbSqlApiCollection**. |Ano |
| collectionName |Název kolekce dokumentů Azure Cosmos DB. |Ano |

Pokud používáte datovou sadu typu "DocumentDbCollection", je stále podporovaná, protože je pro zpětnou kompatibilitu pro aktivity kopírování a vyhledávání podporována. tok dat není podporován. Navrhnete použití nového modelu.

**Příklad**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schéma službou Data Factory

Pro úložiště dat bez schématu jako je Azure Cosmos DB aktivita kopírování odvodí schéma v jednom ze způsobů, jak je popsáno v následujícím seznamu. Pokud nechcete [import a export dokumentů JSON jako-je](#import-or-export-json-documents), osvědčeným postupem je zadání struktury dat v **struktura** oddílu.

Data Factory respektuje mapování, které jste zadali u aktivity. Pokud řádek neobsahuje hodnotu pro sloupec, hodnoty null se poskytuje pro hodnotu sloupce.

Pokud mapování nezadáte, Služba Data Factory odvodí schéma pomocí prvního řádku v datech. Pokud první řádek neobsahuje celé schéma, v výsledku operace aktivity budou chybět některé sloupce.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností, které podporuje zdroj Azure Cosmos DB (SQL API) a podpora jímky.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) jako zdroj

Chcete-li kopírovat data z Azure Cosmos DB (SQL API), nastavte typ **zdroje** v aktivitě kopírování na **DocumentDbCollectionSource**. 

Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **CosmosDbSqlApiSource**. |Ano |
| query |Zadejte dotaz služby Azure Cosmos DB číst data.<br/><br/>Příklad:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadán, je proveden tento příkaz SQL: `select <columns defined in structure> from mycollection` |
| preferredRegions | Upřednostňovaný seznam oblastí, ke kterým se má připojit při načítání dat z Cosmos DB. | Ne |
| pageSize | Počet dokumentů na stránku výsledku dotazu. Výchozí hodnota je-1, což znamená, že na straně služby se používá dynamická velikost stránky až na 1000. | Ne |

Použijete-li zdroj typu "DocumentDbCollectionSource", je stále podporováno, protože je z důvodu zpětné kompatibility. Navrhnete použití nového modelu, který bude poskytovat rozsáhlejší možnosti kopírování dat z Cosmos DB.

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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
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
| type | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na **CosmosDbSqlApiSink**. |Ano |
| writeBehavior |Popisuje, jak zapsat data do služby Azure Cosmos DB. Povolené hodnoty: **vložit** a **upsert**.<br/><br/>Chování **upsert** nahrazuje dokumentu, pokud dokument se stejným ID už existuje; v opačném případě vložit dokument.<br /><br />**Poznámka:** : Data Factory automaticky vygeneruje ID dokumentu, pokud ID není zadán, buď v původním dokumentu nebo mapování sloupců. To znamená, že musíte zajistit, aby, pro **upsert** fungovat podle očekávání, váš dokument nemá identifikátor. |Ne<br />(výchozí hodnota je **vložit**) |
| writeBatchSize | Datová továrna používá [knihovny prováděcí modul služby Azure Cosmos DB hromadné](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) zapsat data do služby Azure Cosmos DB. Vlastnost **writeBatchSize** určuje velikost dokumentů, které ADF zajišťují do knihovny. Můžete zkusit zvýšit hodnotu **writeBatchSize** ke zlepšení výkonu a snížení hodnoty, pokud váš dokument je velká velikost – viz následující tipy. |Ne<br />(výchozí hodnota je **10 000**) |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je například Cosmos DB ru, pro optimalizaci výkonu a doporučení pro kopírování. Pokud máte obavy s tímto chováním, zadejte `true` pro jeho vypnutí. | Ne (výchozí hodnota je `false`) |

>[!TIP]
>Cosmos DB omezí velikost jednoho požadavku až 2MB. Vzorec je žádost o velikosti = velikost jednoho dokumentu * velikost dávky zápisu. Pokud dosáhnete o tom, že chyba **"Požadavku je příliš velký."** , **snížit `writeBatchSize` hodnotu** v konfiguraci jímky kopírování.

Použijete-li zdroj typu "DocumentDbCollectionSink", je stále podporováno, protože je z důvodu zpětné kompatibility. Navrhnete použití nového modelu, který bude poskytovat rozsáhlejší možnosti kopírování dat z Cosmos DB.

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
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Přečtěte si podrobnosti o [transformaci zdrojového kódu](data-flow-source.md) a [transformaci jímky](data-flow-sink.md) v mapování toku dat.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="import-or-export-json-documents"></a>Import a export dokumentů JSON

Pomocí tohoto konektoru Azure Cosmos DB (SQL API) můžete snadno:

* Importujte dokumenty JSON z různých zdrojů do služby Azure Cosmos DB, včetně z úložiště objektů Blob v Azure, Azure Data Lake Store a další souborové úložiště, které podporuje Azure Data Factory.
* Exportujte dokumentů JSON z kolekce Azure Cosmos DB do různých úložišť založená na souborech.
* Zkopírujte dokumenty mezi dvě kolekce Azure Cosmos DB jako-je.

K dosažení dogmaticky na schématu kopírování:

* Při použití nástroje pro kopírování dat vyberte **exportovat jako-soubory JSON nebo kolekce Cosmos DB** možnost.
* Pokud používáte vytváření aktivit, vyberte formát JSON s odpovídajícím úložištěm souborů pro zdroj nebo jímku.

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
