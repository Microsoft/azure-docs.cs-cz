---
title: Kopírování dat do nebo z Azure Cosmos DB (SQL API) pomocí Data Factory
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
ms.openlocfilehash: 74bec61cbb69b17afddeb5abc1df16d8c1140234
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681300"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Cosmos DB (SQL API) pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuální verze](connector-azure-cosmos-db.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do Azure Cosmos DB (SQL API). Článek se vytvoří na [aktivitě kopírování v Azure Data Factory](copy-activity-overview.md), která představuje obecný přehled aktivity kopírování.

>[!NOTE]
>Tento konektor podporuje pouze kopírování dat do a z Cosmos DB rozhraní SQL API. MongoDB API najdete v tématu [konektor pro rozhraní api Azure Cosmos DB pro MongoDB](connector-azure-cosmos-db-mongodb-api.md). Jiné typy rozhraní API se teď nepodporují.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Cosmos DB (SQL API) se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data můžete kopírovat z Azure Cosmos DB (SQL API) do libovolného podporovaného úložiště dat jímky nebo kopírovat data z libovolného podporovaného zdrojového úložiště dat do Azure Cosmos DB (SQL API). Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky, najdete v tématu [podporované úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

Konektor Azure Cosmos DB (SQL API) můžete použít k těmto akcím:

- Kopírovat data z a do Azure Cosmos DB [rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Zapsat do Azure Cosmos DB jako **INSERT** nebo **Upsert**.
- Importujte a exportujte dokumenty JSON tak, jak jsou, nebo zkopírujte data z tabulkové sady nebo z ní. Mezi příklady patří databáze SQL a soubor CSV. Pokud chcete kopírovat dokumenty tak, jak jsou, nebo ze souborů JSON nebo do jiné kolekce Azure Cosmos DB, přečtěte si téma import nebo export dokumentů JSON.

Data Factory se integruje s [knihovnou prováděcího modulu Azure Cosmos DB Bulk](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) , která poskytuje nejlepší výkon při psaní do Azure Cosmos DB.

> [!TIP]
> [Video k migraci dat](https://youtu.be/5-SRNiC_qOU) vás provede kroky kopírování dat z úložiště objektů BLOB v Azure do Azure Cosmos DB. Video také popisuje požadavky na optimalizaci výkonu pro ingestování dat Azure Cosmos DB obecně.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit, které jsou specifické pro Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure Cosmos DB (SQL API) se podporují tyto vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** musí být nastavená na **CosmosDb**. | Ano |
| Vlastnosti |Zadejte informace, které jsou požadovány pro připojení k databázi Azure Cosmos DB.<br />**Poznámka**: v připojovacím řetězci je nutné zadat informace o databázi, jak je znázorněno v následujících příkladech. <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Můžete také umístit klíč účtu do Azure Key Vault a načíst `accountKey` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. |Ano |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , který se má použít pro připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo místní prostředí Integration runtime (Pokud se vaše úložiště dat nachází v privátní síti). Pokud tato vlastnost není zadaná, použije se výchozí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z nebo do Azure Cosmos DB (SQL API), nastavte vlastnost **Type** datové sady na **DocumentDbCollection**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** datové sady musí být nastavená na **DocumentDbCollection**. |Ano |
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

### <a name="schema-by-data-factory"></a>Schéma podle Data Factory

Pro úložiště dat bez schématu, jako je například Azure Cosmos DB, aktivita kopírování odvozuje schéma v jednom ze způsobů popsaných v následujícím seznamu. Pokud nechcete [importovat nebo exportovat dokumenty JSON tak, jak jsou](#import-or-export-json-documents), nejlepším postupem je určit strukturu dat v části **Struktura** .

* Pokud určíte strukturu dat pomocí vlastnosti **Structure** v definici datové sady, Data Factory tuto strukturu respektovat jako schéma. 

    Pokud řádek neobsahuje hodnotu pro sloupec, je pro hodnotu sloupce zadána hodnota null.
* Pokud neurčíte strukturu dat pomocí vlastnosti **Structure** v definici datové sady, Služba Data Factory odvodí schéma pomocí prvního řádku v datech. 

    Pokud první řádek neobsahuje celé schéma, v výsledku operace kopírování budou chybět některé sloupce.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností, které podporuje zdroj Azure Cosmos DB (SQL API) a podpora jímky.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v tématu [kanály](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) jako zdroj

Chcete-li kopírovat data z Azure Cosmos DB (SQL API), nastavte typ **zdroje** v aktivitě kopírování na **DocumentDbCollectionSource**. 

V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **DocumentDbCollectionSource**. |Ano |
| query |Zadejte Azure Cosmos DB dotaz pro čtení dat.<br/><br/>Příklad:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadaný, spustí se tento příkaz SQL: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciální znak, který označuje, že je dokument vnořený a jak sloučit sadu výsledků.<br/><br/>Pokud například dotaz Azure Cosmos DB vrátí vnořený výsledek `"Name": {"First": "John"}`, aktivita kopírování identifikuje název sloupce jako `Name.First`s hodnotou "Jan", pokud je hodnota **nestedSeparator** **.** (tečka). |Ne<br />(výchozí hodnota je **.** (tečka)) |

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

V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na **DocumentDbCollectionSink**. |Ano |
| WriteBehavior |Popisuje, jak zapisovat data do Azure Cosmos DB. Povolené hodnoty: **INSERT** a **Upsert**.<br/><br/>Chování **Upsert** je nahradit dokument, pokud již existuje dokument se stejným ID; v opačném případě vložte dokument.<br /><br />**Poznámka**: Data Factory automaticky vygeneruje ID pro dokument, pokud ID není zadáno v původním dokumentu nebo mapování sloupce. To znamená, že musíte zajistit, aby **Upsert** v případě, že bude fungovat podle očekávání, měl váš dokument ID. |Ne<br />(výchozí hodnota je **INSERT**) |
| writeBatchSize | Data Factory používá [knihovnu Azure Cosmos DB Bulk prováděcích](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) modulů k zápisu dat do Azure Cosmos DB. Vlastnost **writeBatchSize** určuje velikost dokumentů, které ADF zajišťují do knihovny. Můžete zkusit zvýšit hodnotu pro **writeBatchSize** a zvýšit tak výkon a snížit hodnotu, pokud je velikost dokumentu Velká – viz Tipy níže. |Ne<br />(výchozí hodnota je **10 000**) |
| nestingSeparator |Speciální znak v názvu **zdrojového** sloupce, který označuje, že je nutné vnořený dokument. <br/><br/>Například `Name.First` ve struktuře výstupní datové sady generuje následující strukturu JSON v dokumentu Azure Cosmos DB, když je **nestedSeparator** **.** (tečka): `"Name": {"First": "[value maps to this column from source]"}`  |Ne<br />(výchozí hodnota je **.** (tečka)) |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je například Cosmos DB ru, pro optimalizaci výkonu a doporučení pro kopírování. Pokud máte obavy s tímto chováním, zadejte `true` pro jeho vypnutí. | Ne (výchozí hodnota je `false`) |

>[!TIP]
>Cosmos DB omezuje velikost jednoho požadavku na 2 MB. Vzorec je velikost požadavku = jedna velikost dokumentu * velikost dávky zápisu. Pokud se zobrazí chybová zpráva **"velikost požadavku je příliš velká."** , **snižte hodnotu `writeBatchSize`** v konfiguraci kopírování jímky.

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
## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="import-or-export-json-documents"></a>Import nebo export dokumentů JSON

Pomocí tohoto konektoru Azure Cosmos DB (SQL API) můžete snadno:

* Import dokumentů JSON z různých zdrojů do Azure Cosmos DB, včetně úložiště objektů BLOB v Azure, Azure Data Lake Store a dalších úložišť založených na souborech, které Azure Data Factory podporuje.
* Exportujte dokumenty JSON z kolekce Azure Cosmos DB do různých úložišť založených na souborech.
* Kopírovat dokumenty mezi dvěma kolekcemi Azure Cosmos DB tak, jak jsou.

Chcete-li dosáhnout schématu – nezávislá kopírování:

* Když použijete nástroj Kopírování dat, vyberte možnost **exportovat podle souborů JSON nebo Cosmos DB kolekce** .
* Pokud používáte vytváření aktivit, nezadávejte v Azure Cosmos DB datové sadě oddíl **struktury** (označuje se také jako *schéma*). Nezadávejte také vlastnost **nestingSeparator** ve zdroji Azure Cosmos DB nebo jímky v aktivitě kopírování. Při importu nebo exportu do souborů JSON zadejte v odpovídající datové sadě úložiště souborů typ **formátu** jako **JsonFormat** a nakonfigurujte **vzor** souboru, jak je popsáno v oddílu [formát JSON](supported-file-formats-and-compression-codecs.md#json-format) . Pak Nezadávejte oddíl **Structure** a přeskočte zbytek nastavení formátu.

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky v Azure Data Factory, najdete v tématu [podporovaná úložiště dat](copy-activity-overview.md##supported-data-stores-and-formats).
