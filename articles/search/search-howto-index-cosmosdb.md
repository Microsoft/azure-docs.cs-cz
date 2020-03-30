---
title: Hledání přes data Azure Cosmos DB
titleSuffix: Azure Cognitive Search
description: Importujte data z Azure Cosmos DB do prohledávatelného indexu v Azure Cognitive Search. Indexery automatizují ingestování dat pro vybrané zdroje dat, jako je Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283001"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Indexování dat Cosmos DB pomocí indexeru ve službě Azure Cognitive Search 

> [!IMPORTANT] 
> Rozhraní SQL API je obecně k dispozici.
> MongoDB API, Gremlin API a Cassandra API podpory jsou aktuálně ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Přístup k náhledům můžete požádat vyplněním [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview). [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce náhledu. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

> [!WARNING]
> Azure Cognitive Search podporuje jenom kolekce Cosmos DB se [zásadou indexování](https://docs.microsoft.com/azure/cosmos-db/index-policy) nastavenou na [konzistentní.](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) Indexování kolekce s opožděné indexování zásady se nedoporučuje a může mít za následek chybějící data. Kolekce se zakázaným indexováním nejsou podporovány.

Tento článek ukazuje, jak nakonfigurovat [indexer](search-indexer-overview.md) Azure Cosmos DB extrahovat obsah a učinit jej prohledávatelný v Azure Cognitive Search. Tento pracovní postup vytvoří index Azure Cognitive Search a načte ho s existujícím textem extrahovaným z Azure Cosmos DB. 

Vzhledem k tomu, že terminologie může být matoucí, stojí za zmínku, že [indexování Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) a [indexování Azure Cognitive Search](search-what-is-an-index.md) jsou odlišné operace, které jsou jedinečné pro každou službu. Než začnete indexování Azure Cognitive Search, musí databáze Azure Cosmos DB už existovat a obsahovat data.

Indexer Cosmos DB v Azure Cognitive Search může procházet [položky Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) přístupné prostřednictvím různých protokolů. 

+ Pro [rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), které je obecně dostupné, můžete k vytvoření zdroje dat a indexeru použít [portál](#cosmos-indexer-portal), rozhraní [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)nebo [sdk .NET.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

+ Pro [rozhraní MongoDB API (preview)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)můžete k vytvoření zdroje dat a indexeru použít [portál](#cosmos-indexer-portal) nebo rozhraní REST API [verze 2019-05-06-Preview.](search-api-preview.md)

+ Pro [rozhraní Cassandra API (preview)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) a [Gremlin API (preview)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)můžete k vytvoření zdroje dat a indexeru použít pouze [rozhraní REST API verze 2019-05-06-Preview.](search-api-preview.md)


> [!Note]
> Můžete hlasovat o hlas uživatele pro [table API,](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) pokud chcete vidět, že je podporovánv Azure Cognitive Search.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Použití portálu

> [!Note]
> Portál aktuálně podporuje rozhraní SQL API a MongoDB API (preview).

Nejjednodušší metodou pro indexování položek Azure Cosmos DB je použití průvodce na [webu Azure Portal](https://portal.azure.com/). Vzorkováním dat a čtením metadat v kontejneru může Průvodce [**importem dat**](search-import-data-portal.md) v Azure Cognitive Search vytvořit výchozí index, namapovat zdrojová pole na cílová pole indexu a načíst index v jedné operaci. V závislosti na velikosti a složitosti zdrojových dat můžete mít provozní fulltextový vyhledávací index během několika minut.

Doporučujeme používat stejnou oblast nebo umístění pro Azure Cognitive Search a Azure Cosmos DB pro nižší latenci a vyhnout se poplatkům za šířku pásma.

### <a name="1---prepare-source-data"></a>1 - Příprava zdrojových dat

Měli byste mít účet Cosmos DB, databázi Azure Cosmos DB namapovanou na rozhraní SQL API, rozhraní API MongoDB (náhled) nebo gremlinské rozhraní API (preview) a obsah v databázi.

Ujistěte se, že databáze Cosmos DB obsahuje data. [Průvodce importem dat](search-import-data-portal.md) čte metadata a provádí vzorkování dat k odvodit schéma indexu, ale také načte data z Cosmos DB. Pokud data chybí, průvodce se zastaví s touto chybou "Chyba při zjišťování schématu indexu ze zdroje dat: Nelze vytvořit index prototypu, protože zdroj dat "emptycollection" nevrátil žádná data.

### <a name="2---start-import-data-wizard"></a>2 - Průvodce spuštěním importu dat

Průvodce můžete [spustit](search-import-data-portal.md) z panelu příkazů na stránce služby Azure Cognitive Search nebo pokud se připojujete k rozhraní SQL API Cosmos DB, můžete kliknout na **Přidat Azure Cognitive Search** v části **Nastavení** v levém navigačním podokně účtu Cosmos DB.

   ![Příkaz Importovat data na portálu](./media/search-import-data-portal/import-data-cmd2.png "Spuštění Průvodce importem dat")

### <a name="3---set-the-data-source"></a>3 - Nastavení zdroje dat

Na stránce **zdroje dat** musí být zdrojem **Cosmos DB**s následujícími specifikacemi:

+ **Název** je název objektu zdroje dat. Po vytvoření si ji můžete vybrat pro jiné úlohy.

+ **Účet Cosmos DB** by měl být primární nebo sekundární připojovací řetězec z Cosmos DB, s `AccountEndpoint` a . `AccountKey` Pro kolekce MongoDB přidejte **ApiKind=MongoDb** na konec připojovacího řetězce a oddělte ho od připojovacího řetězce středníkem. Pro rozhraní Gremlin API a Cassandra API, použijte pokyny pro [rozhraní REST API](#cosmosdb-indexer-rest).

+ **Databáze** je existující databáze z účtu. 

+ **Kolekce** je kontejner dokumentů. Aby byl import úspěšný, musí existovat dokumenty. 

+ **Dotaz** může být prázdný, pokud chcete všechny dokumenty, jinak můžete zadat dotaz, který vybere podmnožinu dokumentu. **Dotaz** je k dispozici pouze pro rozhraní SQL API.

   ![Definice zdroje dat Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Definice zdroje dat Cosmos DB")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Přeskočení stránky "Obohatit obsah" v průvodci

Přidání kognitivních dovedností (nebo obohacení) není požadavek na import. Pokud nemáte konkrétní potřebu [přidat obohacení AI](cognitive-search-concept-intro.md) do kanálu indexování, měli byste tento krok přeskočit.

Chcete-li krok přeskočit, klikněte na modrá tlačítka v dolní části stránky pro "Další" a "Přeskočit".

### <a name="5---set-index-attributes"></a>5 - Nastavení atributů indexu

Na stránce **Rejstřík** byste měli vidět seznam polí s datovým typem a řadou zaškrtávacích políček pro nastavení atributů indexu. Průvodce může vygenerovat seznam polí na základě metadat a vzorkováním zdrojových dat. 

Atributy můžete hromadně vybrat klepnutím na zaškrtávací políčko v horní části sloupce atributu. Zvolte **Retrievable** a **Searchable** pro každé pole, které má být vráceno do klientské aplikace a podléhá zpracování fulltextového vyhledávání. Všimněte si, že celá čísla nejsou plný text nebo fuzzy prohledávatelné (čísla jsou vyhodnocena doslovně a jsou často užitečné ve filtrech).

Další informace naleznete v popisu [atributů indexu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) a [analyzátorů jazyků.](https://docs.microsoft.com/rest/api/searchservice/language-support) 

Udělejte si chvilku, abyste si zkontrolovali svůj výběr. Po spuštění průvodce jsou vytvořeny fyzické datové struktury a nebudete moci tato pole upravovat bez přetažení a opětovného vytvoření všech objektů.

   ![Definice indexu Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definice indexu Cosmos DB")

### <a name="6---create-indexer"></a>6 - Vytvořit indexer

Plně zadaný průvodce vytvoří tři odlišné objekty ve vyhledávací službě. Objekt zdroje dat a objekt indexu se uloží jako pojmenované prostředky ve službě Azure Cognitive Search. Poslední krok vytvoří objekt indexeru. Pojmenování indexeru umožňuje, aby existoval jako samostatný prostředek, který můžete plánovat a spravovat nezávisle na indexu a objektu zdroje dat vytvořeném ve stejném sekvenci průvodce.

Pokud nejste obeznámeni s indexery, *indexer* je prostředek v Azure Cognitive Search, který prochází externí zdroj dat pro prohledávatelný obsah. Výstup průvodce **importem dat** je indexer, který prochází zdroj dat Cosmos DB, extrahuje prohledávatelný obsah a importuje jej do indexu v Azure Cognitive Search.

Následující snímek obrazovky ukazuje výchozí konfiguraci indexeru. Můžete přepnout na **Jednou,** pokud chcete spustit indexer jednou. Klepnutím na **tlačítko Odeslat** spusťte průvodce a vytvořte všechny objekty. Indexování začíná okamžitě.

   ![Definice indexeru Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definice indexeru Cosmos DB")

Import dat můžete sledovat na stránkách portálu. Oznámení průběhu označují stav indexování a počet nahrávek dokumentů. 

Po dokončení indexování můžete k dotazování indexu použít [Průzkumník ahledatlku.](search-explorer.md)

> [!NOTE]
> Pokud očekávaná data nevidíte, možná budete muset nastavit více atributů ve více polích. Odstraňte index a indexer, které jste právě vytvořili, a znovu projděte průvodce a upravte výběry atributů indexu v kroku 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Použití rozhraní REST API

Rozhraní REST API můžete použít k indexování dat Azure Cosmos DB podle třídílného pracovního postupu společného pro všechny indexery v Azure Cognitive Search: vytvořit zdroj dat, vytvořit index, vytvořit indexer. Extrakce dat z Cosmos DB dochází při odeslání požadavku vytvořit indexer. Po dokončení tohoto požadavku budete mít dotazovatelný index. 

> [!NOTE]
> Pro indexování dat z rozhraní Cosmos DB Gremlin API nebo rozhraní COSMOS DB Cassandra API je nutné nejprve požádat o přístup k bránou náhledy vyplněním [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview). Po zpracování vašeho požadavku obdržíte pokyny, jak pomocí [rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) k vytvoření zdroje dat.

Dříve v tomto článku je uvedeno, že [indexování Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) a [indexování Azure Cognitive Search](search-what-is-an-index.md) jsou odlišné operace. Pro indexování Cosmos DB jsou ve výchozím nastavení všechny dokumenty automaticky indexovány s výjimkou rozhraní CASSSandra API. Pokud vypnete automatické indexování, dokumenty lze přistupovat pouze prostřednictvím jejich vlastní odkazy nebo dotazy pomocí ID dokumentu. Azure Cognitive Search indexování vyžaduje Cosmos DB automatické indexování, které mají být zapnuty v kolekci, která bude indexována Azure Cognitive Search. Při registraci do náhledu indexeru rozhraní API Cosmos DB Cassandra budete mít pokyny, jak nastavit indexování Cosmos DB.

> [!WARNING]
> Azure Cosmos DB je nová generace DocumentDB. Dříve s rozhraním API verze **2017-11-11** můžete použít syntaxi. `documentdb` To znamenalo, že můžete zadat `cosmosdb` typ `documentdb`zdroje dat jako nebo . Počínaje verzí rozhraní API **2019-05-06** rozhraní API Azure cognitive `cosmosdb` search a portál usměrní pouze syntaxi podle pokynů v tomto článku. To znamená, že typ `cosmosdb` zdroje dat musí, pokud chcete připojit ke koncovému bodu Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 - Montáž vstupů pro požadavek

Pro každý požadavek musíte zadat název služby a klíč správce pro Azure Cognitive Search (v hlavičce POST) a název účtu úložiště a klíč pro úložiště objektů blob. [Postman](search-get-started-postman.md) můžete použít k odesílání požadavků HTTP do Azure Cognitive Search.

Zkopírujte následující čtyři hodnoty do poznámkového bloku, abyste je mohli vložit do požadavku:

+ Název služby Azure Cognitive Search
+ Klíč správce Azure Cognitive Search
+ Připojovací řetězec Cosmos DB

Tyto hodnoty najdete na portálu:

1. Na stránkách portálu pro Azure Cognitive Search zkopírujte adresu URL vyhledávací služby ze stránky Přehled.

2. V levém navigačním podokně klikněte na **klávesy** a zkopírujte primární nebo sekundární klíč (jsou ekvivalentní).

3. Přepněte na portálové stránky pro váš účet úložiště Cosmos. V levém navigačním podokně klikněte v části **Nastavení**na **klávesy**. Tato stránka obsahuje identifikátor URI, dvě sady připojovacích řetězců a dvě sady klíčů. Zkopírujte jeden z připojovacích řetězců do programu Poznámkový blok.

### <a name="2---create-a-data-source"></a>2 - Vytvoření zdroje dat

**Zdroj dat** určuje data k indexování, pověření a zásady pro identifikaci změn v datech (například upravené nebo odstraněné dokumenty uvnitř kolekce). Zdroj dat je definován jako nezávislý prostředek, takže jej může používat více indexerů.

Chcete-li vytvořit zdroj dat, vytvořte požadavek POST:

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
| **Jméno** | Povinná hodnota. Zvolte libovolný název, který bude představovat objekt zdroje dat. |
|**Typ**| Povinná hodnota. Musí `cosmosdb`být . |
|**Pověření** | Povinná hodnota. Musí se jednat o připojovací řetězec Cosmos DB.<br/>Pro kolekce SQL jsou připojovací řetězce v tomto formátu:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>Pro kolekce MongoDB přidejte **apikind=MongoDb** do připojovacího řetězce:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Pro Gremlin grafy a Cassandra tabulky, zaregistrujte se na [bránou indexer náhled](https://aka.ms/azure-cognitive-search/indexer-preview) získat přístup k náhledu a informace o tom, jak formátovat pověření.<br/><br/>Vyhněte se číslům portů v adrese URL koncového bodu. Pokud zahrnete číslo portu, Azure Cognitive Search nebude možné indexovat databázi Azure Cosmos DB.|
| **Kontejner** | Obsahuje následující prvky: <br/>**název**: Povinné. Zadejte ID kolekce databáze, která má být indexována.<br/>**dotaz**: Volitelné. Můžete zadat dotaz pro sloučení libovolného dokumentu JSON do plochého schématu, které může indexovat Azure Cognitive Search.<br/>Pro rozhraní API MongoDB, Gremlin API a Cassandra API nejsou podporovány. |
| **dataChangeDetectionPolicy** | Doporučené. Viz Část [Indexování změněných dokumentů.](#DataChangeDetectionPolicy)|
|**dataDeletionDetectionPolicy** | Nepovinný parametr. Viz [Indexování odstraněných dokumentů.](#DataDeletionDetectionPolicy)|

### <a name="using-queries-to-shape-indexed-data"></a>Použití dotazů k tvarování indexovaných dat
Můžete zadat dotaz SQL pro sloučení vnořených vlastností nebo polí, vlastnosti projektu JSON a filtrovat data, která mají být indexována. 

> [!WARNING]
> Vlastní dotazy nejsou podporovány pro **rozhraní MongoDB API**, `container.query` **Gremlin API**a Cassandra **API**: parametr musí být nastaven na hodnotu null nebo vynechán. Pokud potřebujete použít vlastní dotaz, dejte nám prosím vědět na [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Příklad dokumentu:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Filtrační dotaz:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Dotaz na sloučení:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projekční dotaz:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Dotaz na sloučení polí:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 - Vytvoření cílového vyhledávacího indexu 

[Vytvořte cílový index Azure Cognitive Search,](/rest/api/searchservice/create-index) pokud ho ještě nemáte. Následující příklad vytvoří index s ID a popisem:

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

Ujistěte se, že schéma cílového indexu je kompatibilní se schématem zdrojových dokumentů JSON nebo výstupem vlastní projekce dotazu.

> [!NOTE]
> Pro rozdělené kolekce je výchozí klíč dokumentu `_rid` vlastnost Azure Cosmos DB, kterou `rid` Azure Cognitive Search automaticky přejmenuje na protože názvy polí nemohou začínat znakem podtržítka. Hodnoty Azure Cosmos `_rid` DB také obsahují znaky, které jsou v klíčích Azure Cognitive Search neplatné. Z tohoto důvodu `_rid` jsou hodnoty zakódovány Base64.
> 
> Pro kolekce MongoDB Azure Cognitive Search `_id` automaticky `id`přejmenuje vlastnost na .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapování mezi datovými typy JSON a datovými typy Azure cognitive search
| Datový typ JSON | Kompatibilní typy cílových indexových polí |
| --- | --- |
| Logická hodnota |Edm.Boolean, Edm.String |
| Čísla, která vypadají jako celá čísla |Edm.Int32, Edm.Int64, Edm.String |
| Čísla, která vypadají jako plovoucí body |Edm.Double, Edm.String |
| Řetězec |Edm.String |
| Pole primitivních typů, například ["a", "b", "c"] |Collection(Edm.String) |
| Řetězce, které vypadají jako data |Edm.DateTimeOffset, Edm.String |
| GeoJSON objekty, například { "type": "Point", "souřadnice": [long, lat] } |Edm.GeographyPoint |
| Další objekty JSON |Není dostupné. |

### <a name="4---configure-and-run-the-indexer"></a>4 - Konfigurace a spuštění indexeru

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

Tento indexer běží každé dvě hodiny (interval plánu je nastaven na "PT2H"). Chcete-li spustit indexer každých 30 minut, nastavte interval na "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je volitelný - pokud je vynechán, indexer se spustí pouze jednou, když je vytvořen. Indexer však můžete spustit indexer na vyžádání kdykoli.   

Další podrobnosti o rozhraní API vytvořit indexeru najdete v části [Vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Další informace o definování plánů indexeru najdete v tématu [Jak naplánovat indexery pro Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Použití .NET

Obecně dostupná sada .NET SDK má úplnou paritu s obecně dostupným rozhraním REST API. Doporučujeme, abyste si přečetli předchozí část rozhraní REST API a zjistili o konceptech, pracovních postupech a požadavcích. Potom můžete odkazovat na následující dokumentaci reference rozhraní .NET API k implementaci indexeru JSON ve spravovaném kódu.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexování změněných dokumentů

Účelem zásady zjišťování změn dat je efektivně identifikovat změněné datové položky. V současné době je jedinou podporovanou zásadou [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) použití vlastnosti `_ts` (časové razítko) poskytované službou Azure Cosmos DB, která je určena následujícím způsobem:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Použití této zásady je důrazně doporučeno zajistit dobrý výkon indexeru. 

Pokud používáte vlastní dotaz, ujistěte `_ts` se, že vlastnost je promítán dotazem.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Přírůstkový průběh a vlastní dotazy

Přírůstkové průběh během indexování zajišťuje, že pokud indexer uprovádění je přerušeno přechodné selhání nebo časový limit spuštění, indexer může vyzvednout, kde skončil při příštím spuštění, namísto nutnosti přeindexovat celou kolekci od začátku. To je zvláště důležité při indexování velké kolekce. 

Chcete-li povolit přírůstkový průběh při použití vlastního dotazu, ujistěte se, že dotaz sestaví výsledky podle `_ts` sloupce. To umožňuje pravidelné kontroly ukazující, že Azure Cognitive Search používá k poskytování přírůstkový průběh v přítomnosti selhání.   

V některých případech i v `ORDER BY [collection alias]._ts` případě, že dotaz obsahuje klauzuli, Azure `_ts`Cognitive Search nemusí odvodit, že dotaz je seřazeny podle . Azure Cognitive Search můžete sdělit, že `assumeOrderByHighWaterMarkColumn` výsledky jsou seřazeny pomocí vlastnosti konfigurace. Chcete-li zadat tuto nápovědu, vytvořte nebo aktualizujte indexer následujícím způsobem: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexování odstraněných dokumentů

Při odstranění řádků z kolekce, obvykle chcete odstranit tyto řádky z indexu vyhledávání také. Účelem zásady detekce odstranění dat je efektivně identifikovat odstraněné datové položky. V současné době je jedinou podporovanou zásadou zásada `Soft Delete` (odstranění je označeno nějakým příznakem), které je určeno takto:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Pokud používáte vlastní dotaz, ujistěte se, `softDeleteColumnName` že vlastnost odkazuje je promítán dotazem.

Následující příklad vytvoří zdroj dat se zásadami obnovitelného odstranění:

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

## <a name="next-steps"></a><a name="NextSteps"></a>Další kroky

Blahopřejeme! Naučili jste se, jak integrovat Azure Cosmos DB s Azure Cognitive Search pomocí indexeru.

* Další informace o Azure Cosmos DB najdete na [stránce služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Další informace o Azure Cognitive Search najdete na [stránce Vyhledávací služba](https://azure.microsoft.com/services/search/).
