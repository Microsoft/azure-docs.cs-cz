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
ms.date: 12/11/2019
ms.openlocfilehash: bb9f2673eb080ee2919297fcbb5199f99d176bce
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635843"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopírování a transformace dat v Azure Cosmos DB (SQL API) pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuální verze](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory kopírovat data do a ze služby Azure Cosmos DB (SQL API) a jak pomocí Toku dat transformovat data ve službě Azure Cosmos DB (SQL API). Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).



>[!NOTE]
>Tento konektor podporuje pouze Cosmos DB rozhraní SQL API. MongoDB API najdete v tématu [konektor pro rozhraní api Azure Cosmos DB pro MongoDB](connector-azure-cosmos-db-mongodb-api.md). Jiné typy rozhraní API se teď nepodporují.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Cosmos DB (SQL API) se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Pro aktivitu kopírování podporuje tato Azure Cosmos DBová konektorová rozhraní SQL API:

- Kopírovat data z a do Azure Cosmos DB [rozhraní SQL API](../cosmos-db/introduction.md).
- Zapsat do Azure Cosmos DB jako **INSERT** nebo **Upsert** .
- Importujte a exportujte dokumenty JSON tak, jak jsou, nebo zkopírujte data z tabulkové sady nebo z ní. Mezi příklady patří databáze SQL a soubor CSV. Pokud chcete kopírovat dokumenty tak, jak jsou, nebo ze souborů JSON nebo do jiné kolekce Azure Cosmos DB, přečtěte si téma [Import a export dokumentů JSON](#import-and-export-json-documents).

Data Factory se integruje s [knihovnou prováděcího modulu Azure Cosmos DB Bulk](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) , která poskytuje nejlepší výkon při psaní do Azure Cosmos DB.

> [!TIP]
> [Video k migraci dat](https://youtu.be/5-SRNiC_qOU) vás provede kroky kopírování dat z úložiště objektů BLOB v Azure do Azure Cosmos DB. Video také popisuje požadavky na optimalizaci výkonu pro ingestování dat Azure Cosmos DB obecně.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit, které jsou specifické pro Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure Cosmos DB (SQL API) se podporují tyto vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **CosmosDb** . | Ano |
| připojovací řetězec |Zadejte informace, které jsou požadovány pro připojení k databázi Azure Cosmos DB.<br />**Poznámka** : v připojovacím řetězci je nutné zadat informace o databázi, jak je znázorněno v následujících příkladech. <br/> Můžete také umístit klíč účtu do Azure Key Vault a získat konfiguraci z `accountKey` připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. |Ano |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , který se má použít pro připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo místní prostředí Integration runtime (Pokud se vaše úložiště dat nachází v privátní síti). Pokud tato vlastnost není zadaná, použije se výchozí Azure Integration Runtime. |Ne |

**Příklad**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
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
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md).

Pro datovou sadu Azure Cosmos DB (SQL API) jsou podporovány následující vlastnosti: 

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** datové sady musí být nastavená na **CosmosDbSqlApiCollection** . |Ano |
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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností, které podporuje zdroj Azure Cosmos DB (SQL API) a podpora jímky. Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v tématu [kanály](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) jako zdroj

Chcete-li kopírovat data z Azure Cosmos DB (SQL API), nastavte typ **zdroje** v aktivitě kopírování na **DocumentDbCollectionSource** . 

V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **CosmosDbSqlApiSource** . |Ano |
| query |Zadejte Azure Cosmos DB dotaz pro čtení dat.<br/><br/>Příklad:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadán, je proveden příkaz SQL: `select <columns defined in structure> from mycollection` |
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

Při kopírování dat z Cosmos DB, pokud nechcete [exportovat dokumenty JSON tak, jak je](#import-and-export-json-documents), nejlepším postupem je určit mapování v aktivitě kopírování. Data Factory respektuje mapování zadané u aktivity – Pokud řádek neobsahuje hodnotu pro sloupec, je pro hodnotu sloupce zadána hodnota null. Pokud mapování nezadáte, Data Factory odvodí schéma pomocí prvního řádku v datech. Pokud první řádek neobsahuje celé schéma, v výsledku operace aktivity budou chybět některé sloupce.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) jako jímka

Chcete-li kopírovat data do Azure Cosmos DB (SQL API), nastavte typ **jímky** v aktivitě kopírování na **DocumentDbCollectionSink** . 

V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na **CosmosDbSqlApiSink** . |Ano |
| writeBehavior |Popisuje, jak zapisovat data do Azure Cosmos DB. Povolené hodnoty: **INSERT** a **Upsert** .<br/><br/>Chování **Upsert** je nahradit dokument, pokud již existuje dokument se stejným ID; v opačném případě vložte dokument.<br /><br />**Poznámka** : Data Factory automaticky vygeneruje ID pro dokument, pokud ID není zadáno v původním dokumentu nebo mapování sloupce. To znamená, že musíte zajistit, aby **Upsert** v případě, že bude fungovat podle očekávání, měl váš dokument ID. |Ne<br />(výchozí hodnota je **INSERT** ) |
| writeBatchSize | Data Factory používá [knihovnu Azure Cosmos DB Bulk prováděcích](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) modulů k zápisu dat do Azure Cosmos DB. Vlastnost **writeBatchSize** určuje velikost dokumentů, které ADF poskytuje do knihovny. Můžete zkusit zvýšit hodnotu pro **writeBatchSize** a zvýšit tak výkon a snížit hodnotu, pokud je velikost dokumentu Velká – viz Tipy níže. |Ne<br />(výchozí hodnota je **10 000** ) |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je například Cosmos DB ru, pro optimalizaci výkonu a doporučení pro kopírování. Pokud se s tímto chováním obáváte, určete, jestli `true` ho chcete vypnout. | Ne (výchozí nastavení je `false` ) |

>[!TIP]
>Pokud chcete importovat dokumenty JSON tak, jak jsou, přečtěte si část [Import nebo export dokumentů JSON](#import-and-export-json-documents) . Chcete-li kopírovat z tabulkových dat, přečtěte si téma [migrace z relační databáze na Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB omezuje velikost jednoho požadavku na 2 MB. Vzorec je velikost požadavku = jedna velikost dokumentu * velikost dávky zápisu. Pokud se zobrazí chybová zpráva **"velikost požadavku je příliš velká."** , **omezte `writeBatchSize` hodnotu** v konfiguraci kopie jímky.

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

### <a name="schema-mapping"></a>Mapování schématu

Chcete-li kopírovat data z Azure Cosmos DB do tabulkové jímky nebo obráceně, přečtěte si téma [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku mapování dat můžete číst a zapisovat do kolekcí v Cosmos DB. Další informace najdete v tématu transformace [zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v tématu mapování toků dat.

### <a name="source-transformation"></a>Transformace zdroje

Nastavení specifická pro Azure Cosmos DB jsou k dispozici na kartě **Možnosti zdroje** ve zdrojové transformaci. 

**Zahrnout systémové sloupce:** Pokud jsou hodnoty true, ```id``` , ```_ts``` a další systémové sloupce zahrnuty do metadat toku dat z CosmosDB. Při aktualizaci kolekcí je důležité zahrnout to, abyste mohli existující ID řádku vzít.

**Velikost stránky:** Počet dokumentů na stránku výsledku dotazu. Výchozí hodnota je-1, která dynamickou stránku služby používá až 1000.

**Propustnost:** Nastavte volitelnou hodnotu pro počet ru, který chcete použít pro kolekci CosmosDB pro každé spuštění tohoto toku dat během operace čtení. Minimum je 400.

**Preferované oblasti:** Vyberte preferované oblasti čtení pro tento proces.

#### <a name="json-settings"></a>Nastavení v souboru JSON

**Jeden dokument:** Tuto možnost vyberte, pokud má ADF zacházet s celým souborem jako s jedním dokumentem JSON.

**Názvy sloupců bez uvozovek:** Tuto možnost vyberte, pokud názvy sloupců ve formátu JSON nejsou v uvozovkách.

**Má komentáře:** Tento výběr použijte, pokud dokumenty JSON obsahují komentáře v datech.

**Jednoduché v uvozovkách:** Tato možnost by měla být vybrána, pokud jsou sloupce a hodnoty v dokumentu citovány s jednoduchými uvozovkami.

**Zpětné lomítko bylo uvozeno:** Pokud používáte zpětná lomítka k řídicím znakům ve formátu JSON, vyberte tuto možnost.

### <a name="sink-transformation"></a>Transformace jímky

Nastavení specifická pro Azure Cosmos DB jsou k dispozici na kartě **Nastavení** transformace jímky.

**Metoda aktualizace:** Určuje, jaké operace jsou pro cíl databáze povolené. Ve výchozím nastavení je povolen pouze vkládání. Chcete-li aktualizovat, Upsert nebo odstranit řádky, je pro označení řádků pro tyto akce nutná transformace ALTER Row. V případě aktualizací upsertuje a DELETE musí být klíčový sloupec nebo sloupce nastaveny k určení, který řádek má být změněn.

**Akce kolekce:** Určuje, zda má být před zápisem znovu vytvořena cílová kolekce.
* Žádné: v kolekci se neprovede žádná akce.
* Znovu vytvořit: kolekce se vynechá a znovu vytvoří.

**Velikost dávky** : Určuje, kolik řádků je v jednotlivých intervalech zapisováno. Větší velikosti dávek zlepšují kompresi a optimalizaci paměti, ale při ukládání dat do mezipaměti riskuje výjimky z paměti.

**Klíč oddílu:** Zadejte řetězec, který představuje klíč oddílu pro kolekci. Příklad: ```/movies/title```

**Propustnost:** Nastavte volitelnou hodnotu pro počet ru, který chcete použít pro kolekci CosmosDB pro každé spuštění tohoto toku dat. Minimum je 400.

**Rozpočet propustnosti zápisu:** Celé číslo představující počet ru, který chcete přidělit úloze Spark pro hromadnou příjem. Toto číslo je mimo celkovou propustnost přidělenou kolekci.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Import a export dokumentů JSON

Pomocí tohoto konektoru Azure Cosmos DB (SQL API) můžete snadno:

* Kopírovat dokumenty mezi dvěma kolekcemi Azure Cosmos DB tak, jak jsou.
* Import dokumentů JSON z různých zdrojů do Azure Cosmos DB, včetně úložiště objektů BLOB v Azure, Azure Data Lake Store a dalších úložišť založených na souborech, které Azure Data Factory podporuje.
* Exportujte dokumenty JSON z kolekce Azure Cosmos DB do různých úložišť založených na souborech.

Chcete-li dosáhnout schématu – nezávislá kopírování:

* Když použijete nástroj Kopírování dat, vyberte možnost **exportovat podle souborů JSON nebo Cosmos DB kolekce** .
* Pokud používáte vytváření aktivit, vyberte formát JSON s odpovídajícím úložištěm souborů pro zdroj nebo jímku.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrace z relační databáze na Cosmos DB

Při migraci z relační databáze, například SQL Server do Azure Cosmos DB, může aktivita kopírování snadno mapovat tabulková data ze zdroje na sloučení dokumentů JSON v Cosmos DB. V některých případech můžete chtít změnit návrh datového modelu na jeho optimalizaci pro NoSQL případy použití v závislosti na [modelování dat v Azure Cosmos DB](../cosmos-db/modeling-data.md), například pro denormalizaci dat vložením všech souvisejících dílčích položek v jednom dokumentu JSON. V takovém případě si přečtěte [Tento článek](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) s návodem, jak ho dosáhnout pomocí Azure Data Factory aktivity kopírování.

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky v Azure Data Factory, najdete v tématu [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).