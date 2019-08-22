---
title: Index Azure Cosmos DB zdroj dat – Azure Search
description: Procházet Azure Cosmos DB zdroj dat a ingestovat data v fulltextovém indexu s možností vyhledávání v Azure Search. Indexery automatizují přijímání dat pro vybrané zdroje dat, jako je Azure Cosmos DB.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 802a4e9c6191d33051eb075543691845595bc9c3
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656695"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Postup indexování Cosmos DB pomocí indexeru Azure Search


> [!Note]
> Podpora rozhraní MongoDB API je ve verzi Preview a není určená pro použití v produkčním prostředí. Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.
>
> Rozhraní SQL API je všeobecně dostupné.

V tomto článku se dozvíte, jak nakonfigurovat [indexer](search-indexer-overview.md) Azure Cosmos DB pro extrakci obsahu a zpřístupnění jeho prohledávání v Azure Search. Tento pracovní postup vytvoří index Azure Search a načte ho s existujícím extrahovaným textem z Azure Cosmos DB. 

Vzhledem k tomu, že terminologie může být matoucí, je třeba poznamenat, že [Azure Cosmos DB indexování](https://docs.microsoft.com/azure/cosmos-db/index-overview) a [Azure Search indexování](search-what-is-an-index.md) jsou odlišné operace, které jsou pro každou službu jedinečné. Než začnete s indexováním Azure Search, databáze Azure Cosmos DB už musí existovat a obsahovat data.

K indexování Cosmos obsahu můžete použít [portál](#cosmos-indexer-portal), rozhraní REST API nebo sadu .NET SDK. Indexer Cosmos DB v Azure Search může procházet [položky Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) , ke kterým se přistupoval prostřednictvím těchto protokolů:

* [ROZHRANÍ SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [Rozhraní API pro MongoDB (Preview)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> Uživatelský hlas obsahuje existující položky pro další podporu rozhraní API. Můžete přetypovat hlasovat pro rozhraní Cosmos API, která chcete zobrazit, pokud chcete vidět podporované Azure Search: [Rozhraní API pro tabulky](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4) [rozhraní API pro Apache Cassandra](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Použití portálu

Nejjednodušší způsob indexování položek Azure Cosmos je použití Průvodce v [Azure Portal](https://portal.azure.com/). Pomocí vzorkování dat a čtení metadat v kontejneru může průvodce [**importem dat**](search-import-data-portal.md) v Azure Search vytvořit výchozí index, mapovat zdrojová pole na pole cílového indexu a načíst index v rámci jedné operace. V závislosti na velikosti a složitosti zdrojových dat můžete mít během několika minut provozní fulltextový index vyhledávání.

Pro Azure Search i Azure Cosmos DB doporučujeme použít stejné předplatné Azure, pokud je to ve stejné oblasti.

### <a name="1---prepare-source-data"></a>1\. Příprava zdrojových dat

Měli byste mít účet Cosmos, databázi Azure Cosmos namapovanou na rozhraní SQL API nebo rozhraní MongoDB API a kontejner dokumentů JSON. 

Ujistěte se, že vaše databáze Cosmos DB obsahuje data. [Průvodce importem dat](search-import-data-portal.md) načte metadata a provede vzorkování dat pro odvození schématu indexu, ale také načte data z Cosmos DB. Pokud data chybí, průvodce se zastaví s touto chybou "při zjišťování schématu indexu ze zdroje dat došlo k chybě: Nepovedlo se vytvořit index prototypu, protože DataSource prázdnécollection nevrátil žádná data.

### <a name="2---start-import-data-wizard"></a>2\. spuštění Průvodce importem dat

Průvodce můžete [Spustit](search-import-data-portal.md) z příkazového řádku na stránce Azure Search služby nebo kliknutím na **Přidat Azure Search** v části **Nastavení** v levém navigačním podokně svého účtu úložiště.

   ![Příkaz Importovat data na portálu](./media/search-import-data-portal/import-data-cmd2.png "Spuštění Průvodce importem dat")

### <a name="3---set-the-data-source"></a>3 – nastavení zdroje dat

> [!NOTE] 
> V současné době nemůžete vytvářet ani upravovat zdroje dat **MongoDB** pomocí Azure Portal nebo sady .NET SDK. Historii spuštění MongoDB indexerů ale **můžete** monitorovat na portálu.

Na stránce **zdroj dat** musí být zdroj **Cosmos DB**, a to s následujícími specifikacemi:

+ **Name** je název objektu zdroje dat. Po vytvoření si ho můžete vybrat pro jiné úlohy.

+ **Cosmos DB účet** by měl být primárním nebo sekundárním připojovacím řetězcem z `AccountEndpoint` Cosmos DB s `AccountKey`a. Účet určuje, jestli se data přetypování mají jako SQL API nebo Mongo DB API.

+ **Databáze** je existující databáze z účtu. 

+ **Kolekce** je kontejner dokumentů. Aby import proběhl úspěšně, musí dokumenty existovat. 

+ **Dotaz** může být prázdný, pokud chcete všechny dokumenty, jinak můžete zadat dotaz, který vybere podmnožinu dokumentu. 

   ![Cosmos DB definice zdroje dat](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB definice zdroje dat")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – přeskočí stránku přidat vyhledávání rozpoznávání v průvodci.

Přidání dovedností rozpoznávání není pro import dokumentu nutné. Pokud nemáte konkrétní nutnost [zahrnout rozhraní API služeb Cognitive Services a transformace](cognitive-search-concept-intro.md) do kanálu indexování, měli byste tento krok přeskočit.

Chcete-li tento krok přeskočit, nejprve přejděte na další stránku.

   ![Tlačítko Další stránka pro hledání rozpoznávání](media/search-get-started-portal/next-button-add-cog-search.png)

Z této stránky můžete přejít dopředu k přizpůsobení indexu.

   ![Vynechání kroku kognitivních dovedností](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5\. nastavení atributů indexu

Na stránce **index** byste měli vidět seznam polí s datovým typem a řadu zaškrtávacích políček pro nastavení atributů indexu. Průvodce může vygenerovat seznam polí založený na metadatech a vzorkováním zdrojových dat. 

Atributy můžete hromadně vybírat kliknutím na zaškrtávací políčko v horní části sloupce atributu. Vyberte možnost získatelné a prohledávatelné pro každé pole, které by se mělo vrátit do klientské aplikace a podléhá fulltextovým zpracování fulltextového vyhledávání. Všimnete si, že celá čísla nejsou fulltextová nebo přibližná prohledávání (čísla jsou vyhodnocována v doslovném znění a jsou často užitečná ve filtrech).

Další informace najdete v popisu [atributů indexu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) a [analyzátorů jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support) . 

Věnujte prosím chvíli kontrole vašich výběrů. Po spuštění Průvodce se vytvoří fyzické datové struktury a nebudete moct tato pole upravovat, aniž byste museli odstraňovat a znovu vytvářet všechny objekty.

   ![Definice indexu Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definice indexu Cosmos DB")

### <a name="6---create-indexer"></a>6\. vytvoření indexeru

V rámci vaší vyhledávací služby vytvoří průvodce tři odlišné objekty. Objekt zdroje dat a objekt indexu jsou uloženy ve službě Azure Search jako pojmenované prostředky. Poslední krok vytvoří objekt indexeru. Pojmenování indexeru umožňuje, aby existoval jako samostatný prostředek, který můžete naplánovat a spravovat nezávisle na objektu index a zdroj dat, který jste vytvořili ve stejné sekvenci průvodce.

Pokud nejste obeznámeni s indexery, *indexer* je prostředek v Azure Search, který prochází externím zdrojem dat pro prohledávatelný obsah. Výstupem průvodce **importem dat** je indexer, který prochází Cosmos DB zdroj dat, extrahuje prohledávatelný obsah a importuje ho do indexu v Azure Search.

Následující snímek obrazovky ukazuje výchozí konfiguraci indexeru. Pokud chcete indexer spustit jednorázově, můžete přepnout na jeden. Kliknutím na **Odeslat** spusťte průvodce a vytvořte všechny objekty. Indexování se okamžitě zahájí.

   ![Cosmos DB definice indexeru](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB definice indexeru")

Data importování můžete monitorovat na stránkách portálu. Oznámení o průběhu označují stav indexování a počet odeslaných dokumentů. 

Po dokončení indexování můžete pomocí [Průzkumníka služby Search Vyhledat](search-explorer.md) dotaz na svůj index.

> [!NOTE]
> Pokud nevidíte očekávaná data, možná budete muset nastavit další atributy pro více polí. Odstraňte index a indexer, který jste právě vytvořili, a projděte průvodce znovu a změňte si výběr pro atributy indexu v kroku 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Použití rozhraní REST API

Pomocí REST API můžete indexovat Azure Cosmos DB data, a to za pracovní postup tří částí, který je společný pro všechny Indexery v Azure Search: vytvořte zdroj dat, vytvořte index a vytvořte indexer. K extrakci dat z úložiště Cosmos dojde, když odešlete žádost o vytvoření indexeru. Po dokončení této žádosti budete mít Queryable index. 

Pokud vyhodnocujete MongoDB, musíte použít zbytek `api-version=2019-05-06-Preview` k vytvoření zdroje dat.

V účtu Cosmos DB můžete zvolit, zda chcete, aby kolekce automaticky indexoval všechny dokumenty. Ve výchozím nastavení jsou všechny dokumenty indexovány automaticky, ale můžete vypnout automatické indexování. Když je indexování vypnuto, k dokumentům lze přistupovat pouze prostřednictvím vlastních odkazů nebo dotazů pomocí ID dokumentu. Azure Search vyžaduje, aby bylo Cosmos DB automatické indexování zapnuté v kolekci, která bude indexována Azure Search. 

> [!WARNING]
> Azure Cosmos DB je další generace DocumentDB. Dřív s rozhraním API verze **2017-11-11** můžete použít `documentdb` syntaxi. To znamená, že můžete zadat typ zdroje dat jako `cosmosdb` nebo. `documentdb` Počínaje rozhraním API verze **2019-05-06** Azure Search rozhraní API i portál podporují `cosmosdb` pouze syntaxi podle pokynů v tomto článku. To znamená, že typ zdroje dat musí `cosmosdb` být v případě, že se chcete připojit k Cosmos DB koncovému bodu.

### <a name="1---assemble-inputs-for-the-request"></a>1\. sestavování vstupů pro požadavek

Pro každý požadavek musíte zadat název služby a klíč správce pro Azure Search (v hlavičce POST) a název účtu úložiště a klíč pro úložiště objektů BLOB. K odeslání požadavků HTTP do Azure Search můžete použít [metodu post](search-get-started-postman.md) .

Do poznámkového bloku zkopírujte následující čtyři hodnoty, abyste je mohli vložit do žádosti:

+ Název služby Azure Search
+ Klíč správce Azure Search
+ Připojovací řetězec Cosmos DB

Tyto hodnoty můžete najít na portálu:

1. Na stránkách portálu pro Azure Search zkopírujte adresu URL služby Search na stránce Přehled.

2. V levém navigačním podokně klikněte na **klíče** a zkopírujte buď primární nebo sekundární klíč (jsou ekvivalentní).

3. Přepněte na stránky portálu účtu úložiště Cosmos. V levém navigačním podokně v části **Nastavení**klikněte na **klíče**. Tato stránka poskytuje identifikátor URI, dvě sady připojovacích řetězců a dvě sady klíčů. Zkopírujte jeden z připojovacích řetězců do poznámkového bloku.

### <a name="2---create-a-data-source"></a>2\. vytvoření zdroje dat

**Zdroj dat** určuje data, která mají být indexována, pověření a zásady pro identifikaci změn v datech (například upravené nebo odstraněné dokumenty v kolekci). Zdroj dat je definován jako nezávislý prostředek, aby jej bylo možné použít více indexery.

Chcete-li vytvořit zdroj dat, formulujte požadavek POST:

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

Tělo požadavku obsahuje definici zdroje dat, která by měla obsahovat následující pole:

| Pole   | Popis |
|---------|-------------|
| **name** | Povinný parametr. Vyberte libovolný název, který bude představovat váš objekt zdroje dat. |
|**type**| Povinný parametr. Musí být `cosmosdb`. |
|**přihlašovací údaje** | Povinný parametr. Musí se jednat o Cosmos DB připojovací řetězec.<br/>V případě kolekcí SQL jsou připojovací řetězce v tomto formátu:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>Pro kolekce MongoDB přidejte **ApiKind = MongoDB** do připojovacího řetězce:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Vyhněte se číslům portů v adrese URL koncového bodu. Pokud zadáte číslo portu, Azure Search nebude moci indexovat databázi Azure Cosmos DB.|
| **container** | Obsahuje následující prvky: <br/>**název**: Povinný parametr. Zadejte ID kolekce databází, která se má indexovat.<br/>**dotaz**: Volitelný parametr. Můžete zadat dotaz pro sloučení libovolného dokumentu JSON do plochého schématu, které Azure Search může indexovat.<br/>Pro kolekce MongoDB se dotazy nepodporují. |
| **dataChangeDetectionPolicy** | Doporučil. Viz část [indexování změněných dokumentů](#DataChangeDetectionPolicy) .|
|**dataDeletionDetectionPolicy** | Volitelný parametr. Viz část [indexování odstraněných dokumentů](#DataDeletionDetectionPolicy) .|

### <a name="using-queries-to-shape-indexed-data"></a>Použití dotazů na indexovaná data obrazců
Můžete zadat dotaz SQL pro sloučení vnořených vlastností nebo polí, vlastností JSON projektu a filtrovat data, která mají být indexována. 

> [!WARNING]
> Vlastní dotazy nejsou podporovány pro kolekce **MongoDB** : `container.query` parametr musí být nastaven na hodnotu null nebo vynechán. Pokud potřebujete použít vlastní dotaz, dejte nám prosím na [uživatelském hlasu](https://feedback.azure.com/forums/263029-azure-search)informace.

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

Dotaz na sloučení:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Dotaz projekce:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Dotaz na sloučení pole:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3\. Vytvoření cílového vyhledávacího indexu 

Pokud ho ještě nemáte, [vytvořte cílový Azure Search index](/rest/api/searchservice/create-index) . Následující příklad vytvoří index s polem ID a popis:

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

Ujistěte se, že schéma cílového indexu je kompatibilní se schématem zdrojových dokumentů JSON nebo výstupem vlastního projekce dotazu.

> [!NOTE]
> U dělených kolekcí je výchozí klíč dokumentu Azure Cosmos DB `_rid` vlastnost, která Azure Search automaticky přejmenuje na `rid` , protože názvy polí nemohou začínat znakem undescore. Azure Cosmos DB `_rid` hodnoty také obsahují znaky, které jsou v Azure Search klíčes neplatné. Z `_rid` tohoto důvodu jsou hodnoty kódovány pomocí kódování Base64.
> 
> Pro kolekce MongoDB Azure Search automaticky přejmenuje `_id` vlastnost na. `doc_id`  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapování mezi datovými typy JSON a Azure Searchmi datovými typy
| Datový typ JSON | Kompatibilní typy polí cílového indexu |
| --- | --- |
| Bool |EDM. Boolean, Edm. String |
| Čísla, která vypadají jako celá čísla |Edm.Int32, Edm.Int64, Edm.String |
| Čísla, která vypadají jako plovoucí body |Edm.Double, Edm.String |
| Řetězec |Edm.String |
| Pole primitivních typů, například ["a", "b", "c"] |Collection(Edm.String) |
| Řetězce, které vypadají jako kalendářní data |Edm.DateTimeOffset, Edm.String |
| Objekty injson pro injson, například {"Type": "Point", "souřadnice": [Long, lat]} |Edm.GeographyPoint |
| Jiné objekty JSON |Není k dispozici |

### <a name="4---configure-and-run-the-indexer"></a>4\. konfigurace a spuštění indexeru

Po vytvoření indexu a zdroje dat jste připraveni vytvořit indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Tento indexer se spouští každé dvě hodiny (časový interval je nastaven na "PT2H"). Pokud chcete indexer spustit každých 30 minut, nastavte interval na "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je nepovinný – Pokud je vynechaný, indexer se při vytvoření spustí jenom jednou. Můžete ale kdykoli spustit indexer na vyžádání.   

Další informace o rozhraní API Create indexeru najdete v části [Vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Další informace o definování plánů indexerů najdete v tématu [postup plánování indexerů pro Azure Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Použití .NET

Obecně dostupná sada .NET SDK má úplnou paritu s všeobecně dostupnou REST API. Doporučujeme, abyste si přečtěte předchozí část REST API, kde se dozvíte o konceptech, pracovních postupech a požadavcích. Pak se můžete podívat na následující referenční dokumentaci rozhraní .NET API a implementovat indexer JSON ve spravovaném kódu.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexování změněných dokumentů

Účelem zásad detekce změn dat je efektivně identifikovat změněné datové položky. V současné době jedinou podporovanou zásadou `High Water Mark` je zásada `_ts` pomocí vlastnosti (časové razítko), kterou poskytuje Azure Cosmos DB, která je určena následujícím způsobem:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Použití této zásady se důrazně doporučuje, aby se zajistil dobrý výkon indexeru. 

Pokud používáte vlastní dotaz, ujistěte se, že `_ts` je vlastnost projekt provedená dotazem.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Přírůstkový průběh a vlastní dotazy

Přírůstkový průběh indexování zajišťuje, že pokud je provádění indexeru přerušeno přechodnými chybami nebo časovým limitem spuštění, může indexer vyzvednutí místa, kde se při příštím spuštění ponechá, místo aby bylo nutné znovu indexovat celou kolekci od začátku. To je obzvláště důležité při indexování velkých kolekcí. 

Pokud chcete povolit přírůstkový průběh při použití vlastního dotazu, ujistěte se, že dotaz seřadí výsledky `_ts` podle sloupce. To umožňuje pravidelné vracení se změnami, které Azure Search používá k poskytnutí přírůstkového postupu v případě výskytu selhání.   

V některých případech i v případě, že dotaz obsahuje `ORDER BY [collection alias]._ts` klauzuli, Azure Search nesmí odvodit, že dotaz je seřazen `_ts`podle. Azure Search, že výsledky jsou seřazené pomocí `assumeOrderByHighWaterMarkColumn` vlastnosti konfigurace, můžete určit. Chcete-li zadat tuto nápovědu, vytvořte nebo aktualizujte indexer následujícím způsobem: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexování odstraněných dokumentů

Pokud jsou řádky z kolekce odstraněny, obvykle je chcete odstranit také z indexu vyhledávání. Účelem zásad detekce odstranění dat je efektivně identifikovat odstraněné datové položky. V současné době je `Soft Delete` jedinou podporovanou zásadou zásada (odstranění je označeno příznakem nějakého řazení), které je zadáno následujícím způsobem:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Pokud používáte vlastní dotaz, ujistěte se, že vlastnost, na kterou odkazuje `softDeleteColumnName` , je projektovaná dotazem.

Následující příklad vytvoří zdroj dat se zásadami podmíněného odstranění:

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

Blahopřejeme! Zjistili jste, jak integrovat Azure Cosmos DB s Azure Search pomocí indexeru.

* Další informace o Azure Cosmos DB najdete na [stránce služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Další informace o Azure Search najdete na [stránce vyhledávací služby](https://azure.microsoft.com/services/search/).
