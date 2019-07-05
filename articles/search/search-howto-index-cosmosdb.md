---
title: Indexovat zdroje dat Azure Cosmos DB – Azure Search
description: Procházet zdroj dat služby Azure Cosmos DB a jejich ingestování v prohledávatelných fulltextového indexu ve službě Azure Search. Indexery můžete automatizovat příjem dat pro vybrané zdroje dat jako jsou služby Azure Cosmos DB.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 7f9df42725e41fb514370dbdb828ad5b1305ea78
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485455"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Jak indexovat Cosmos DB pomocí indexeru Azure Search


> [!Note]
> Podpora rozhraní MongoDB API je ve verzi preview a není určen pro použití v produkčním prostředí. [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) tuto funkci poskytuje. Není žádný portál nebo podpora .NET SDK v současnosti.
>
> Rozhraní SQL API je obecně dostupná.

V tomto článku se dozvíte, jak nakonfigurovat služby Azure Cosmos DB [indexer](search-indexer-overview.md) k extrahování obsahu a provést s možností vyhledávání ve službě Azure Search. Tento pracovní postup vytvoří index Azure Search a načte se existující textů získaných ze služby Azure Cosmos DB. 

Vzhledem k tomu, že terminologie může být matoucí, je vhodné poznamenat, který [indexování služby Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) a [indexování Azure Search](search-what-is-an-index.md) jsou různé operace, které jsou jedinečná pro každou službu. Než můžete začít s Azure Search indexování, databázi Azure Cosmos DB musí již existovat a obsahují data.

Můžete použít [portál](#cosmos-indexer-portal), rozhraní REST API nebo .NET SDK k indexování obsahu Cosmos. Indexer Cosmos DB ve službě Azure Search Procházet [položky Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) přistupovat prostřednictvím těchto protokolů:

* [ROZHRANÍ SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [Rozhraní MongoDB API (preview)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> User Voice má existující položky pro další podpora rozhraní API. Můžete přetypovat Hlasujte pro rozhraní API Cosmos chcete naleznete v tématu podporované ve službě Azure Search: [Table API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4), [rozhraní Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Použití portálu

Nejjednodušším způsobem pro indexování položek Azure Cosmos je použití Průvodce v [webu Azure portal](https://portal.azure.com/). Vzorkování dat a čtení metadat v kontejneru, [ **importovat data** ](search-import-data-portal.md) Průvodce ve službě Azure Search můžete vytvořit výchozí index, mapují pole zdroje na cíl pole indexu a načtení indexu v jednom operace. V závislosti na velikosti a složitosti zdroje dat může mít indexu provozní fulltextové vyhledávání v minutách.

Doporučujeme používat stejné předplatné Azure pro Azure Search a Azure Cosmos DB, pokud možno ve stejné oblasti.

### <a name="1---prepare-source-data"></a>1 – Příprava zdrojových dat

Měli byste účtu Cosmos, databáze Azure Cosmos namapované na rozhraní SQL API nebo rozhraní API MongoDB a kontejner dokumentů JSON. 

Ujistěte se, že vaše databáze Cosmos DB obsahuje data. [Průvodce importem dat](search-import-data-portal.md) načte metadata a provádí vzorkování dat k odvození schématu indexu, ale taky načítání dat ze služby Cosmos DB. Pokud chybí data, průvodce bude ukončen s touto chybou "Chyba rozpoznání schéma indexu ze zdroje dat: Nepodařilo se vytvořit prototypový index. protože zdroj dat "emptycollection" nevrátil žádná data".

### <a name="2---start-import-data-wizard"></a>2 – Spusťte Průvodce importem dat

Je možné [spusťte průvodce](search-import-data-portal.md) na panelu příkazů na stránce služby Azure Search, nebo kliknutím **přidat Azure Search** v **nastavení** levým části vašeho účtu úložiště Navigační podokno.

   ![Příkaz pro import dat na portálu](./media/search-import-data-portal/import-data-cmd2.png "spusťte Průvodce importem dat")

### <a name="3---set-the-data-source"></a>3 – nastavení zdroje dat

> [!NOTE] 
> V současné době nelze vytvořit nebo upravit **MongoDB** zdrojů dat pomocí .NET SDK nebo webu Azure portal. Však můžete **můžete** monitorování historie spuštění indexování MongoDB na portálu.

V **zdroj dat** stránku, musí být zdroj **Cosmos DB**, s následujícími specifikacemi:

+ **Název** je název objektu zdroje dat. Po vytvoření můžete u jiných úloh.

+ **Účet služby cosmos DB** by měl být primární nebo sekundární připojovací řetězec ze služby Cosmos DB, pomocí `AccountEndpoint` a `AccountKey`. Účet určuje, zda data je typovaná jako rozhraní SQL API nebo rozhraní Mongodb API

+ **Databáze** je existující databázi z účtu. 

+ **Kolekce** je kontejner dokumentů. Dokumenty musí existovat v pořadí pro import úspěšný. 

+ **Dotaz** může být prázdné Pokud chcete, aby všechny dokumenty, v opačném případě můžete zadat dotaz, který vybere podmnožinu dokumentů. 

   ![Definice zdroje dat služby cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "definici zdroje dat služby Cosmos DB")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – přeskočte stránku "Přidat kognitivní vyhledávání" v Průvodci

Přidat kognitivní dovednosti není nutné pro import dokumentu. Pokud nemáte specifickou potřebu [patří rozhraní API služeb Cognitive Services a transformace](cognitive-search-concept-intro.md) na váš kanál indexování by měl tento krok přeskočit.

Chcete-li přeskočit krok, nejdřív přejdete na další stránku.

   ![Tlačítko Další stránky pro kognitivního vyhledávání](media/search-get-started-portal/next-button-add-cog-search.png)

Z této stránky můžete přeskočit přímo k přizpůsobení indexu.

   ![Vynechání kroku kognitivních dovedností](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - atributy indexu set

V **Index** stránky, zobrazí se seznam polí s typem dat a řadu zaškrtávací políčka pro nastavení atributy indexu. Průvodce můžete vytvořit seznam polí na základě metadat a vzorkováním zdrojová data. 

Vám může hromadně výběru atributy kliknutím na zaškrtávací políčko v horní části sloupce atributu. Zvolte **Retrievable** a **Searchable** pro každé pole, která má být vrácen pro klientskou aplikaci a v souladu s zpracování hledání textu v plném znění. Všimněte si, že celá čísla nejsou textu v plném znění nebo přibližné prohledávatelná (čísla jsou vyhodnocovány znění a jsou často užitečné při filtry).

Zkontrolujte popis [atributy indexu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) a [jazykové analyzátory](https://docs.microsoft.com/rest/api/searchservice/language-support) Další informace. 

Za chvíli zkontrolujte zvolené položky. Po spuštění Průvodce fyzické datové struktury jsou vytvořeny a nebudou moct tato pole upravovat bez vyřadit a znovu vytvořit všechny objekty.

   ![Cosmos DB definici indexu](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "definici indexu služby Cosmos DB")

### <a name="6---create-indexer"></a>6 – Vytvoření indexeru

Plně zadaný, Průvodce vytvoří tři různé objekty ve vyhledávací službě. Objekt zdroje dat a indexu objektu se ukládají jako pojmenovaným prostředkům ve službě Azure Search. Poslední krok vytvoří objekt indexeru. Pojmenování indexer umožňuje existuje jako samostatný prostředek, který můžete naplánovat a spravovat bez ohledu na jejich rejstřík a data zdrojový objekt, vytvoří ve stejném pořadí průvodce.

Pokud nejste obeznámeni s indexery, *indexer* je prostředek ve službě Azure Search, která prochází externího zdroje dat pro prohledávatelný obsah. Výstup **importovat data** Průvodce indexer je výsledkem, který prochází zdroje dat služby Cosmos DB, extrahuje prohledávatelný obsah a naimportuje do indexu Azure Search.

Následující snímek obrazovky ukazuje výchozí konfigurace indexeru. Můžete přepnout na **jednou** Pokud chcete spustit indexer jednou. Klikněte na tlačítko **odeslat** ke spuštění průvodce a vytvoření všech objektů. Indexování začíná okamžitě.

   ![Definice indexeru cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "definice indexeru služby Cosmos DB")

Můžete monitorovat import dat do stránky portálu. Oznámení o průběhu označuje stav indexování a kolik dokumenty jsou odeslány. 

Při indexování hotový, můžete použít [Průzkumníka služby Search](search-explorer.md) k dotazování indexu.

> [!NOTE]
> Pokud nevidíte data, která jste očekávali, můžete potřebovat nastavit další atributy na více polí. Odstranění indexu a indexeru, kterou jste právě vytvořili a postupujte podle pokynů průvodce znovu, vyberte požadované možnosti pro atributy indexu v kroku 5 úpravy. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Použití rozhraní REST API

Můžete použít rozhraní REST API pro data indexu služby Azure Cosmos DB, následujícího pracovního postupu třemi částmi společné pro všechny indexery ve službě Azure Search: vytvoření zdroje dat, vytvoření indexu, vytvořením indexeru. Extrakce dat z úložiště Cosmos nastane, když odešlete žádost o vytvoření indexeru. Po dokončení této žádosti budete mít dotazovatelné indexu. 

Pokud hodnotíte MongoDB, je nutné použít ZBÝVAJÍCÍ `api-version=2019-05-06-Preview` vytvořit zdroj dat.

V účtu služby Cosmos DB můžete, zda chcete automaticky indexuje všechny dokumenty kolekci. Ve výchozím nastavení všechny dokumenty jsou automaticky indexovány, ale můžete vypnout automatické indexování. Když je vypnutý indexování, dokumentů je přístupný pouze prostřednictvím jejich odkazů na sebe sama nebo dotazy pomocí dokumentů ID. Služba Azure Search vyžaduje automatické indexování zapnuté v kolekci, které bude služba Azure Search indexovat Cosmos DB. 

> [!WARNING]
> Azure Cosmos DB je nová generace služby DocumentDB. Dříve se verze rozhraní API **2017-11-11** můžete použít `documentdb` syntaxe. To znamená, že můžete zadat váš typ zdroje dat jako `cosmosdb` nebo `documentdb`. Počínaje verzí rozhraní API **2019-05-06** rozhraní API služby Azure Search i portál podporuje pouze `cosmosdb` syntaxe podle pokynů v tomto článku. To znamená, že typ zdroje dat musí `cosmosdb` Pokud se chcete připojit ke koncovému bodu služby Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 - vstupy pro žádost o sestavení

Pro každý požadavek musíte zadat název služby a klíč správce pro Azure Search (v hlavičce POST) a název účtu úložiště a klíč pro úložiště objektů blob. Můžete použít [Postman](search-get-started-postman.md) k odesílání požadavků HTTP do služby Azure Search.

Zkopírujte následující čtyři hodnoty do poznámkového bloku tak, aby vložte je do požadavku:

+ Název služby Azure Search
+ Klíč správce služby Azure Search
+ Připojovací řetězec služby cosmos DB

Tyto hodnoty můžete najít na portálu:

1. Na stránkách portálu pro Azure Search zkopírujte adresu URL služby search na stránce Přehled.

2. V levém navigačním podokně klikněte na tlačítko **klíče** a poté zkopírujte primární nebo sekundární klíč (jsou ekvivalentní).

3. Přepnout na stránkách portálu pro váš účet úložiště Cosmos. V levém navigačním podokně v části **nastavení**, klikněte na tlačítko **klíče**. Tato stránka obsahuje identifikátor URI, dvě sady připojovací řetězce, a dvě sady klíčů. Zkopírujte jeden z připojovacích řetězců do poznámkového bloku.

### <a name="2---create-a-data-source"></a>2 – Vytvoření zdroje dat

A **zdroj dat** data určená k indexu, přihlašovacích údajů a zásady pro identifikaci změny v datech (jako jsou například dokumenty modified nebo deleted uvnitř vaší kolekce). Zdroj dat je definován jako nezávislý prostředek tak, aby ho můžete použít několik indexerů.

Chcete-li vytvořit zdroj dat, zformulujte podobnou žádost POST:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
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

| Pole   | Popis |
|---------|-------------|
| **name** | Povinná hodnota. Vyberte libovolný název a představují data zdrojový objekt. |
|**type**| Povinná hodnota. Musí být `cosmosdb`. |
|**Přihlašovací údaje** | Povinná hodnota. Musí být připojovací řetězec služby Cosmos DB.<br/>Pro kolekce SQL připojovací řetězce jsou v tomto formátu: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>Kolekce MongoDB, přidejte **ApiKind = MongoDb** na připojovací řetězec:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Vyhněte se čísla portů v adresu url koncového bodu. Pokud je číslo portu, nepůjde Azure Search k indexování databáze Azure Cosmos DB.|
| **container** | obsahuje následující prvky: <br/>**Název**: Povinná hodnota. Zadejte ID kolekce databáze, který se má indexovat.<br/>**dotaz**: Volitelné. Můžete zadat dotaz, který libovolný dokument JSON sloučit do ploché schéma, které Azure Search můžete indexovat.<br/>Dotazy nejsou podporovány pro kolekce MongoDB. |
| **dataChangeDetectionPolicy** | Doporučené. Zobrazit [indexování dokumentů změnit](#DataChangeDetectionPolicy) oddílu.|
|**dataDeletionDetectionPolicy** | Volitelné. Zobrazit [indexování dokumentů odstranit](#DataDeletionDetectionPolicy) oddílu.|

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
        "tags": ["azure", "cosmosdb", "search"]
    }

Dotaz filtru:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Sloučení dotazu:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projekce dotazů:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Pole sloučení dotazu:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3\. vytvoření cílovým indexem vyhledávání 

[Vytvoření indexu Azure Search cílové](/rest/api/searchservice/create-index) Pokud již nemáte. Následující příklad vytvoří index s ID a description pole:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
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
| Bool |Edm.Boolean, Edm.String |
| Čísla, které vypadají jako celá čísla |Edm.Int32, Edm.Int64, Edm.String |
| Čísla tento vzhled jako číslo s plovoucí čárkou body |Edm.Double, Edm.String |
| String |Edm.String |
| Pole jednoduchých typů, třeba ["a", "b", "c"] |Collection(Edm.String) |
| Řetězce, které vypadají, jako jsou kalendářní data |Edm.DateTimeOffset, Edm.String |
| Objekty GeoJSON, třeba {"type": "Point", "coordinates": [dlouhý, lat]} |Edm.GeographyPoint |
| Jiné objekty JSON |neuvedeno |

### <a name="4---configure-and-run-the-indexer"></a>4 – konfigurace a spuštění indexeru

Po vytvoření index a zdroj dat jste připraveni vytvořit indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Indexer spouští každé dvě hodiny (interval plánování je nastavena na "PT2H"). Pokud chcete spustit indexer každých 30 minut, nastavte interval, který "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je volitelné - li tento parametr vynechán, indexer se spustí pouze jednou, když je vytvořena. Však můžete kdykoli spustit indexer na vyžádání.   

Podrobné informace o rozhraní API pro vytvoření indexeru, projděte si [vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Další informace o definování indexeru plánů najdete v části [naplánování indexerů Azure Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Použití .NET

Obecně dostupnou sadou .NET SDK má úplnou paritu s obecně k dispozici rozhraní REST API. Doporučujeme, abyste si předchozí části rozhraní REST API, další koncepty, pracovních postupů a požadavků. Poté můžete odkázat na následující referenční dokumentace rozhraní API .NET k implementaci JSON indexer ve spravovaném kódu.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

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

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
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
