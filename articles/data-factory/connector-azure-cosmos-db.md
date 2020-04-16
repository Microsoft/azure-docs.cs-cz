---
title: Kopírování a transformace dat v Azure Cosmos DB (SQL API)
description: Zjistěte, jak kopírovat data do a z Azure Cosmos DB (SQL API) a transformovat data v Azure Cosmos DB (SQL API) pomocí Data Factory.
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
ms.openlocfilehash: f0aa70333454b327a0ca76beef2985062ce56715
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415384"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopírování a transformace dat v Azure Cosmos DB (SQL API) pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuální verze](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory kopírovat data do a ze služby Azure Cosmos DB (SQL API) a jak pomocí Toku dat transformovat data ve službě Azure Cosmos DB (SQL API). Další informace o Azure Data Factory najdete v [úvodním článku](introduction.md).



>[!NOTE]
>Tento konektor podporuje pouze rozhraní COSMOS DB SQL API. Rozhraní MongoDB API najdete [v odkazech konektor pro rozhraní API služby Azure Cosmos DB pro MongoDB](connector-azure-cosmos-db-mongodb-api.md). Jiné typy rozhraní API nejsou nyní podporovány.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Cosmos DB (SQL API) je podporovaný pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Pro aktivitu kopírování tento konektor Azure Cosmos DB (SQL API) podporuje:

- Kopírování dat z a do [rozhraní](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction)AZURE Cosmos DB SQL API .
- Zapište do Azure Cosmos DB jako **vložit** nebo **upsert**.
- Importaje a exportuje dokumenty JSON tak, jak jsou, nebo zkopírujte data z tabulkové datové sady nebo do ní. Příklady zahrnují databázi SQL a soubor CSV. Pokud chcete kopírovat dokumenty tak, jak jsou do nebo ze souborů JSON nebo do jiné kolekce Azure Cosmos DB nebo z jiné kolekce, přečtěte si článek [Import a export dokumentů JSON](#import-and-export-json-documents).

Data Factory integruje s [knihovnou hromadné vykonavatel Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) poskytovat nejlepší výkon při zápisu do Azure Cosmos DB.

> [!TIP]
> [Video migrace dat](https://youtu.be/5-SRNiC_qOU) vás provede kroky kopírování dat z úložiště objektů blob Azure do Azure Cosmos DB. Video také popisuje aspekty optimalizace výkonu pro ingestování dat do Azure Cosmos DB obecně.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které můžete použít k definování entit Datové továrny, které jsou specifické pro Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Azure Cosmos DB (SQL API) jsou podporované následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** musí být nastavena na **CosmosDb**. | Ano |
| připojovací řetězec |Zadejte informace, které jsou nutné pro připojení k databázi Azure Cosmos DB.<br />**Poznámka:** V připojovacím řetězci je nutné zadat informace o databázi, jak je znázorněno v následujících příkladech. <br/> Můžete také umístit klíč účtu v Azure `accountKey` Key Vault a vyžádat konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovacích údajích úložiště v](store-credentials-in-key-vault.md) článku Azure Key Vault. |Ano |
| connectVia | [Prostředí Integrace Runtime](concepts-integration-runtime.md) pro připojení k úložišti dat. Můžete použít Prostředí Azure Integration Runtime nebo runtime integrace s vlastním hostitelem (pokud je úložiště dat umístěné v privátní síti). Pokud tato vlastnost není zadána, použije se výchozí prostředí Azure Integration Runtime. |Ne |

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

**Příklad: klíč účtu úložiště v azure key vaultu**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Datové sady a propojené služby](concepts-datasets-linked-services.md).

Pro datovou sadu Azure Cosmos DB (SQL API) jsou podporované následující vlastnosti: 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** datové sady musí být nastavena na **CosmosDbSqlApiCollection**. |Ano |
| název kolekce |Název kolekce dokumentů Azure Cosmos DB. |Ano |

Pokud používáte datovou sadu typu DocumentDbCollection, je stále podporována jako-je pro zpětnou kompatibilitu pro kopírování a vyhledávání aktivity, není podporována pro tok dat. Doporučujeme použít nový model do budoucna.

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

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity

Tato část obsahuje seznam vlastností, které Azure Cosmos DB (SQL API) zdroj a jímky podporu. Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) jako zdroj

Chcete-li zkopírovat data z Azure Cosmos DB (SQL API), nastavte **typ zdroje** v kopírovat aktivitu na **DocumentDbCollectionSource**. 

V části Zdroj **kopírování aktivity** jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** zdroje aktivity kopírování musí být nastavena na **CosmosDbSqlApiSource**. |Ano |
| query |Zadejte dotaz Azure Cosmos DB pro čtení dat.<br/><br/>Příklad:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadán, je tento příkaz SQL proveden:`select <columns defined in structure> from mycollection` |
| preferredRegions | Upřednostňovaný seznam oblastí, ke kterému se má připojit při načítání dat z Cosmos DB. | Ne |
| Pagesize | Počet dokumentů na stránku výsledku dotazu. Výchozí hodnota je "-1", což znamená, že používá dynamickou velikost stránky na straně služby až do 1000. | Ne |

Pokud používáte zdroj typu "DocumentDbCollectionSource", je stále podporován jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model do budoucna, které poskytují bohatší možnosti pro kopírování dat z Cosmos DB.

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

Při kopírování dat z Cosmos DB, pokud chcete [exportovat dokumenty JSON tak, jak je](#import-and-export-json-documents), je osvědčeným postupem určit mapování v aktivitě kopírování. Data Factory respektuje mapování, které jste zadali na aktivitu - pokud řádek neobsahuje hodnotu pro sloupec, je pro hodnotu sloupce k dispozici hodnota null. Pokud nezadáte mapování, Data Factory odvodí schéma pomocí prvního řádku v datech. Pokud první řádek neobsahuje úplné schéma, některé sloupce budou ve výsledku operace aktivity chybět.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) jako jímky

Chcete-li zkopírovat data do Azure Cosmos DB (SQL API), nastavte typ **jímky** v kopírovat aktivitu do **DocumentDbCollectionSink**. 

V části Zdroj **kopírování aktivity** jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** jímky aktivity kopírování musí být nastavena na **CosmosDbSqlApiSink**. |Ano |
| writeBehavior |Popisuje, jak zapisovat data do Azure Cosmos DB. Povolené hodnoty: **insert** a **upsert**.<br/><br/>Chování **upsert** je nahradit dokument, pokud dokument se stejným ID již existuje; v opačném případě vložte dokument.<br /><br />**Poznámka:** Data Factory automaticky vygeneruje ID dokumentu, pokud ID není zadáno v původním dokumentu nebo pomocí mapování sloupců. To znamená, že je nutné zajistit, aby **upsert** fungoval podle očekávání, že váš dokument má ID. |Ne<br />(výchozí hodnota je **vložit**) |
| writeBatchSize | Data Factory používá [knihovnu hromadného vykořistovatele Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) k zápisu dat do Azure Cosmos DB. Vlastnost **writeBatchSize** určuje velikost dokumentů, které adf poskytuje knihovně. Můžete zkusit zvýšit hodnotu pro **writeBatchSize** ke zlepšení výkonu a snížení hodnoty, pokud je velikost dokumentu velká - viz níže uvedené tipy. |Ne<br />(výchozí hodnota je **10 000**) |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je Cosmos DB RU pro optimalizaci výkonu kopírování a doporučení. Pokud se obáváte tohoto `true` chování, zadejte jej vypnout. | Ne (výchozí `false`hodnota je ) |

>[!TIP]
>Chcete-li importovat dokumenty JSON tak, jak jsou, podívejte se do části [Import nebo export dokumentů JSON;](#import-and-export-json-documents) chcete-li kopírovat z tabulkových dat, podívejte se na [migrovat z relační databáze do cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB omezuje velikost jednoho požadavku na 2 MB. Vzorec je Velikost požadavku = Velikost jednoho dokumentu * Velikost dávky zápisu. Pokud se zobrazí chyba **s vyslovením "Velikost požadavku je příliš velká."** **, snižte hodnotu `writeBatchSize` ** v konfiguraci jímky copy.

Pokud používáte zdroj typu "DocumentDbCollectionSink", je stále podporován jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model do budoucna, které poskytují bohatší možnosti pro kopírování dat z Cosmos DB.

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

Chcete-li zkopírovat data z Azure Cosmos DB do tabulkového jímky nebo obráceně, naleznete mapování [schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku dat mapování, můžete číst a zapisovat do kolekcí v Cosmos DB. Další informace naleznete v [transformaci zdroje](data-flow-source.md) a [jímce transformace](data-flow-sink.md) v mapování datových toků.

### <a name="source-transformation"></a>Transformace zdroje

Nastavení specifická pro Azure Cosmos DB jsou k dispozici na kartě **Možnosti zdroje** zdrojové transformace. 

**Zahrnout systémové sloupce:** Pokud ```id```true, ```_ts```, a další systémové sloupce budou zahrnuty do metadat toku dat z CosmosDB. Při aktualizaci kolekcí je důležité zahrnout to, abyste mohli uchopit existující id řádku.

**Velikost stránky:** Počet dokumentů na stránku výsledku dotazu. Výchozí hodnota je "-1", která používá dynamickou stránku služby až do 1000.

**Propustnost:** Nastavte volitelnou hodnotu pro počet ru, které chcete použít pro kolekci CosmosDB pro každé spuštění tohoto toku dat během operace čtení. Minimum je 400.

**Preferované oblasti:** Zvolte upřednostňované oblasti čtení pro tento proces.

#### <a name="json-settings"></a>Nastavení v souboru JSON

**Jeden dokument:** Tuto možnost vyberte, pokud má podavač ADF považovat celý soubor za jeden dokument JSON.

**Názvy nekotovaných sloupců:** Tuto možnost vyberte, pokud názvy sloupců v json jako nekótované.

**Má komentáře:** Tento výběr použijte, pokud vaše dokumenty JSON mají v datech komentáře.

**Jeden citovaný:** Tato možnost by měla být vybrána, pokud jsou sloupce a hodnoty v dokumentu uvedeny s jednoduchými uvozovkami.

**Zpětné lomítko uvozeno:** Pokud používáte zpětná lomítka k úniku znaků v json, zvolte tuto možnost.

### <a name="sink-transformation"></a>Transformace jímky

Nastavení specifická pro Azure Cosmos DB jsou k dispozici na kartě **Nastavení** transformace jímky.

**Metoda aktualizace:** Určuje, jaké operace jsou povoleny v cílové masce databáze. Ve výchozím nastavení je povolit pouze vložení. Chcete-li aktualizovat, upsert nebo odstranit řádky, je nutné změnit řádek transformace označit řádky pro tyto akce. Pro aktualizace, upserts a odstraní, klíčový sloupec nebo sloupce musí být nastavena k určení, který řádek změnit.

**Akce kolekce:** Určuje, zda má být znovu vytvořit cílovou kolekci před zápisem.
* Žádné: Kolekce nebude provedena žádná akce.
* Znovu vytvořit: Kolekce bude vynechána a znovu vytvořena

**Velikost dávky**: Určuje, kolik řádků se zapisuje v každém bloku. Větší dávkové velikosti zlepšují optimalizaci komprese a paměti, ale při ukládání dat do mezipaměti riskují výjimky z paměti.

**Klíč oddílu:** Zadejte řetězec, který představuje klíč oddílu pro vaši kolekci. Příklad: ```/movies/title```

**Propustnost:** Nastavte volitelnou hodnotu pro počet ru, které chcete použít pro kolekci CosmosDB pro každé spuštění tohoto toku dat. Minimum je 400.

**Zapsat rozpočet propustnost:** Celé číslo představující počet ru, které chcete přidělit úlohě Spark hromadného ingestování. Toto číslo je mimo celkovou propustnost přidělenou kolekci.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Import a export dokumentů JSON

Pomocí tohoto konektoru Azure Cosmos DB (SQL API) můžete snadno:

* Zkopírujte dokumenty mezi dvěma kolekcemi Azure Cosmos DB tak, jak jsou.
* Importujte dokumenty JSON z různých zdrojů do Azure Cosmos DB, včetně z úložiště objektů Blob Azure, Azure Data Lake Store a dalších úložišť založených na souborech, které Azure Data Factory podporuje.
* Exportujte dokumenty JSON z kolekce Azure Cosmos DB do různých úložišť založených na souborech.

Chcete-li dosáhnout nezávislá kopie schématu:

* Při použití nástroje Kopírovat data vyberte volbu **Exportovat jako je do souborů JSON nebo kolekce Cosmos DB.**
* Při použití vytváření aktivit zvolte formát JSON s odpovídajícím úložištěm souborů pro zdroj nebo jímku.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrace z relační databáze do Cosmos DB

Při migraci z relační databáze, například SQL Server do Azure Cosmos DB, aktivita kopírování můžete snadno mapovat tabulková data ze zdroje do sloučení dokumentů JSON v Cosmos DB. V některých případech můžete chtít přepracovat datový model tak, aby byl optimalizován pro případy použití NoSQL podle [modelování dat v Azure Cosmos DB](../cosmos-db/modeling-data.md), například k denormalizovat data vložením všech souvisejících dílčích položek do jednoho dokumentu JSON. V takovém případě najdete v [tomto článku](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) s návodem, jak toho dosáhnout pomocí aktivity kopírování Azure Data Factory.

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která podporují aktivity kopírování jako zdroje a propady v Azure Data Factory, najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
