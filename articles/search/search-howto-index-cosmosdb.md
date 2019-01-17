---
title: Indexovat zdroje dat Azure Cosmos DB – Azure Search
description: Procházet zdroj dat služby Azure Cosmos DB a jejich ingestování v prohledávatelných fulltextového indexu ve službě Azure Search. Indexery můžete automatizovat příjem dat pro vybrané zdroje dat jako jsou služby Azure Cosmos DB.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
robot: noindex
ms.custom: seodec2018
ms.openlocfilehash: a55652c8d19866b717cbafec4629030a7708bb50
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359489"
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Připojení služby Cosmos DB pomocí Azure Search pomocí indexerů

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Konfigurace [indexeru Azure Search](search-indexer-overview.md) , která používá jako zdroj dat kolekci Azure Cosmos DB.
> * Vytvořte vyhledávací index s datovými typy kompatibilní s JSON.
> * Konfigurace indexeru na vyžádání a opakované indexování.
> * Přírůstkově aktualizujte index založený na změnách v podkladových datech.

> [!NOTE]
> Azure Cosmos DB je nová generace služby DocumentDB. I když se změní název produktu, `documentdb` syntaxe v indexerech Azure Search stále existuje pro zpětné kompatibility v rozhraní API služby Azure Search a stránky portálu. Při konfiguraci indexery, nezapomeňte zadat `documentdb` syntaxe podle pokynů v tomto článku.

V následujícím videu Azure Cosmos DB programový manažer Andrew Liu ukazuje, jak přidat index Azure Search pro kontejner služby Azure Cosmos DB.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

<a name="supportedAPIs"></a>
## <a name="supported-api-types"></a>Podporované typy rozhraní API

I když službu Azure Cosmos DB podporuje širokou škálu datových modelů a rozhraní API, podpora produkční indexeru Azure Search rozšiřuje rozhraní SQL API. Podpora pro Azure Cosmos DB API pro MongoDB je aktuálně ve verzi public preview.  

Podpora pro další rozhraní API je připravovaný. Pokud chcete nám pomohou určit prioritu ty, které chcete podporovat nejprve, přetypujte hlasování na webu User Voice:

* [Podpora zdroje dat rozhraní API tabulky](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Podporu pro zdroje dat rozhraní Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Podporu pro zdroje dat rozhraní Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>Požadavky

Kromě účtu Cosmos DB, je potřeba mít [služby Azure Search](search-create-service-portal.md). 

V účtu služby Cosmos DB můžete, zda chcete automaticky indexuje všechny dokumenty kolekci. Ve výchozím nastavení všechny dokumenty jsou automaticky indexovány, ale můžete vypnout automatické indexování. Když je vypnutý indexování, dokumentů je přístupný pouze prostřednictvím jejich odkazů na sebe sama nebo dotazy pomocí dokumentů ID. Služba Azure Search vyžaduje automatické indexování zapnuté v kolekci, které bude služba Azure Search indexovat Cosmos DB. 

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Koncepty indexeru Azure Search

A **zdroj dat** data určená k indexu, přihlašovacích údajů a zásady pro identifikaci změny v datech (jako jsou například dokumenty modified nebo deleted uvnitř vaší kolekce). Zdroj dat je definován jako nezávislý prostředek tak, aby ho můžete použít několik indexerů.

**Indexer** popisuje tok dat ze zdroje dat do cílového indexu vyhledávání. Indexer umožňuje:

* Proveďte jednorázové kopii dat k naplnění indexu.
* Synchronizujte indexu se změnami ve zdroji dat podle plánu.
* Volání na vyžádání aktualizace indexu podle potřeby.

Nastavení služby Azure Cosmos DB indexer, budete muset vytvořit index, zdroj dat a nakonec indexeru. Můžete vytvořit tyto objekty pomocí [portál](search-import-data-portal.md), [sady .NET SDK](/dotnet/api/microsoft.azure.search), nebo [rozhraní REST API](/rest/api/searchservice/). 

Tento článek ukazuje, jak používat rozhraní REST API. Pokud se rozhodnete pro tento portál [Průvodce importem dat](search-import-data-portal.md) provede vás procesem vytvoření všechny tyto prostředky, včetně index.

> [!TIP]
> Z řídicího panelu služby Azure Cosmos DB můžete spustit průvodce **Importem dat** a zjednodušit tak indexování zdroje dat. Začněte tak, že v levém navigačním panelu přejdete do **Collections** (Kolekce) > **Add Azure Search** (Přidat službu Azure Search).

> [!NOTE] 
> Teď nejde vytvořit nebo upravit **MongoDB** zdrojů dat pomocí .NET SDK nebo webu Azure Portal. Však můžete **můžete** monitorování historie spuštění indexování MongoDB na portálu.  

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat
Chcete-li vytvořit zdroj dat, proveďte příspěvek:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Text žádosti obsahuje definici zdroje dat, která by měla obsahovat následující pole:

* **Název**: Vyberte libovolný název a reprezentaci vaší databáze.
* **Typ**: Musí být `documentdb`.
* **přihlašovací údaje**:
  
  * **connectionString**: Povinná hodnota. Zadejte informace o připojení k vaší databázi Azure Cosmos DB v následujícím formátu: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>` Kolekce MongoDB, přidejte **ApiKind = MongoDb** na připojovací řetězec: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`
  Vyhněte se čísla portů v adresu url koncového bodu. Pokud je číslo portu, nepůjde Azure Search k indexování databáze Azure Cosmos DB.
* **container**:
  
  * **Název**: Povinná hodnota. Zadejte id kolekce databáze, který se má indexovat.
  * **dotaz**: Volitelné. Můžete zadat dotaz, který libovolný dokument JSON sloučit do ploché schéma, které Azure Search můžete indexovat. Dotazy nejsou podporovány pro kolekce MongoDB. 
* **dataChangeDetectionPolicy**: Doporučené. Zobrazit [indexování dokumentů změnit](#DataChangeDetectionPolicy) oddílu.
* **dataDeletionDetectionPolicy**: Volitelné. Zobrazit [indexování dokumentů odstranit](#DataDeletionDetectionPolicy) oddílu.

### <a name="using-queries-to-shape-indexed-data"></a>Pomocí dotazů na obrazec indexovat data
Můžete zadat dotaz SQL pro sloučení vnořené vlastnosti nebo pole, vlastnosti projektu JSON a filtrovat data, která mají být indexovány. 

> [!WARNING]
> Vlastní dotazy nejsou podporovány pro **MongoDB** kolekcí: `container.query` parametr musí být nastaven na hodnotu null nebo není uveden. Pokud potřebujete použít vlastní dotaz, dejte nám prosím vědět o [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Ukázkový dokument:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

Dotaz filtru:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Sloučení dotazu:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projekce dotazů:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Pole sloučení dotazu:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Pokud již nemáte, vytvořte cílový index Azure Search. Můžete vytvořit index pomocí [uživatelského rozhraní webu Azure portal](search-create-index-portal.md), [vytvořit Index rozhraní REST API služby](/rest/api/searchservice/create-index) nebo [Index – třída](/dotnet/api/microsoft.azure.search.models.index).

Následující příklad vytvoří index s id a description pole:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Ujistěte se, že schéma cílový index je kompatibilní s schématu zdroje dokumentů JSON nebo výstupu vaše vlastní dotaz projekce.

> [!NOTE]
> Používejte u dělených kolekcí, je výchozí klíč dokumentu služby Azure Cosmos DB `_rid` vlastnost, která Azure Search automaticky přejmenuje na `rid` vzhledem k tomu, že názvy polí nemůže začínat znakem undescore. Kromě toho služby Azure Cosmos DB `_rid` hodnoty obsahují znaky, které jsou v Azure Search klíče neplatné. Z tohoto důvodu `_rid` hodnoty jsou kódování Base64.
> 
> Pro kolekce MongoDB, Azure Search automaticky přejmenuje `_id` vlastnost `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapování mezi JSON datové typy a typy dat Azure Search
| JSON datového typu | Typy polí kompatibilní cílový index |
| --- | --- |
| BOOL |Edm.Boolean, Edm.String |
| Čísla, které vypadají jako celá čísla |Edm.Int32, Edm.Int64, Edm.String |
| Čísla tento vzhled jako číslo s plovoucí čárkou body |Edm.Double, Edm.String |
| Řetězec |Edm.String |
| Pole jednoduchých typů, třeba ["a", "b", "c"] |Collection(Edm.String) |
| Řetězce, které vypadají, jako jsou kalendářní data |Edm.DateTimeOffset, Edm.String |
| Objekty GeoJSON, třeba {"type": "Point", "coordinates": [dlouhý, lat]} |Edm.GeographyPoint |
| Jiné objekty JSON |neuvedeno |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>Krok 3: Vytvoření indexeru

Po vytvoření index a zdroj dat jste připraveni vytvořit indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Indexer spouští každé dvě hodiny (interval plánování je nastavena na "PT2H"). Pokud chcete spustit indexer každých 30 minut, nastavte interval, který "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je volitelné - li tento parametr vynechán, indexer se spustí pouze jednou, když je vytvořena. Však můžete kdykoli spustit indexer na vyžádání.   

Podrobné informace o rozhraní API pro vytvoření indexeru, projděte si [vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Spuštění indexeru na vyžádání
Kromě spuštění pravidelně podle plánu, může být indexer vyvoláno také na vyžádání:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2017-11-11
    api-key: [Search service admin key]

> [!NOTE]
> Při spuštění rozhraní API vrátí úspěšně, vyvolání indexeru je naplánovaná, ale vlastní zpracování probíhá asynchronně. 

Můžete monitorovat stav indexeru na portálu nebo pomocí získat Indexer stav rozhraní API, které popisujeme dále. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Získání stavu indexeru
Můžete načíst historii stavu a spuštění indexeru:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2017-11-11
    api-key: [Search service admin key]

Odpověď obsahuje celkový stav indexer, indexer poslední (nebo probíhající) volání a historii posledních vyvolání indexeru.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Historie provádění obsahuje až 50 poslední dokončené spuštění, které jsou řazeny zpětného chronologicky (aby poslední spuštění je dodávána první v odpovědi).

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Indexování změněné dokumenty
Účelem zásada zjišťování změn dat je efektivně identifikovat změny datové položky. V současné době je jedinou podporovanou zásad `High Water Mark` pomocí zásad `_ts` (časové razítko) vlastnost poskytované služby Azure Cosmos DB, která je určena následujícím způsobem:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Pomocí této zásady je důrazně doporučujeme pro zajištění výkonu dobré indexeru. 

Pokud používáte vlastní dotaz, ujistěte se, že `_ts` vlastnost je plánované v dotazu.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Přírůstkového pokroku a vlastních dotazů
Přírůstkového pokroku během indexování zajistí, že pokud se spuštění indexeru se přerušila přechodná selhání nebo omezení doby provádění, indexeru můžete pokračovat tam, kde skončila při příštím spuštění, namísto nutnosti přeindexování celou kolekci úplně od začátku. To je obzvláště důležité, názvy při indexování rozsáhlých kolekcí. 

Pokud chcete povolit přírůstkového pokroku při použití vlastního dotazu, ujistěte se, že váš dotaz řadí výsledky podle `_ts` sloupce. Díky tomu pravidelné bodového využívající Azure Search k zajištění přírůstkového pokroku v případě chyb.   

V některých případech i v případě, že váš dotaz obsahuje `ORDER BY [collection alias]._ts` klauzule, služba Azure Search nelze odvodit, že dotaz je uspořádaný v `_ts`. Poznáte Azure Search, že výsledky jsou seřazeny podle použití `assumeOrderByHighWaterMarkColumn` vlastnost konfigurace. Pokud chcete nastavit tento pomocný parametr, vytvořit nebo aktualizovat indexer následujícím způsobem: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Indexování dokumentů odstraněn
Při odstranění řádků z kolekce, obvykle chcete odstranit řádky z vyhledávacího indexu. Účelem zásad detekce odstranění dat je efektivně identifikovat odstraněná data položky. V současné době je jedinou podporovanou zásad `Soft Delete` zásad (odstranění je označena příznakem s nějakým), která je určena následujícím způsobem:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Pokud používáte vlastní dotaz, ujistěte se, že vlastnost odkazuje `softDeleteColumnName` je plánované v dotazu.

Následující příklad vytvoří zdroj dat s zásadu obnovitelného odstranění:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Další kroky
Blahopřejeme! Jste se naučili, jak integrovat službu Azure Cosmos DB pomocí indexeru Azure Search.

* Další informace o službě Azure Cosmos DB najdete v tématu [stránku služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Další informace o službě Azure Search najdete v tématu [stránku vyhledávací služby](https://azure.microsoft.com/services/search/).
