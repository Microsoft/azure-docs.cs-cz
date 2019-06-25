---
title: Upgradujte na nejnovější verzi rozhraní REST API služby Azure Search – Azure Search
description: Zkontrolovat rozdíly ve verzích rozhraní API a další akce, které jsou potřeba k migraci existujícího kódu na nejnovější verzi rozhraní REST API služby Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 85a8ddf4ce87d7ac8ce460c0aff56311a2ea4578
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540695"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Upgradujte na nejnovější verzi rozhraní REST API služby Azure Search
Pokud používáte předchozí verzi [rozhraní REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/), tento článek vám pomůže při upgradu aplikace použít nejnovější dostupná verze rozhraní API, 2019-05-06.

Verze 2019-05-06 rozhraní REST API obsahuje některé změny z předchozích verzí. Toto jsou většinou zpětně kompatibilní, takže změna kódu by měla trvat pouze minimálním úsilím, v závislosti na verzi, kterou jste používali před. [Kroky pro upgrade](#UpgradeSteps) popisuje změny kódu, vyžaduje se pro použití nových funkcí.

> [!NOTE]
> Instance služby Azure Search podporuje verze rozsah rozhraní REST API, včetně ty dřívější. Můžete dál používat tyto verze rozhraní API, ale doporučujeme migrovat na nejnovější verzi kódu, aby mohli používat nové funkce.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Co je nového ve verzi 2019-05-06
Verze 2019-05-06 je obecně dostupné nejnovější vydání služby REST API služby Azure Search. Funkce, které přešla do stavu v obecně dostupné v této verzi rozhraní API:

* [Automatické dokončování](index-add-suggesters.md) je typeahead funkce, která se dokončí vstup částečně zadaného období.

* [Komplexní typy](search-howto-complex-data-types.md) poskytuje nativní podporu pro data strukturovaný objekt v indexu Azure Search.

* [Režimy parsování JsonLines](search-howto-index-json-blobs.md), součást sady objektů Blob v Azure, indexování, vytvoří jeden hledání v dokumentech na entitu JSON, který je oddělen znakem nového řádku.

* [Kognitivní vyhledávání](cognitive-search-concept-intro.md) poskytuje indexování, které využívají moduly AI rozšíření služeb Cognitive Services.

Několik verzí funkce ve verzi preview se shodují s tato obecná dostupnost aktualizace. Seznam nových funkcí ve verzi preview najdete v tématu [Search REST api-version. 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Změny způsobující chyby

Přeruší stávající kód, který obsahuje následující funkce pro api-version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexer pro službu Azure Cosmos DB – zdroj dat je nyní "typ": "služby cosmos DB"

Pokud používáte [Cosmos DB indexer](search-howto-index-cosmosdb.md ), je nutné změnit `"type": "documentdb"` k `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Chyby výsledek spuštění indexeru už nebude mít stav

Dříve musely strukturu chyba pro spuštění indexeru `status` elementu. Tento prvek byla odebrána, protože ho nebylo poskytují užitečné informace.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Zdroj dat indexer rozhraní API by již nevracelo připojovacích řetězců

Z rozhraní API verze 2019-05-06 a 2019-05-06-Preview, zdroj dat rozhraní API by již nevracelo připojovací řetězce v odpovědi na jakékoli operace REST. V předchozích verzích rozhraní API pro zdroje dat vytvořené využitím příspěvek, Azure Search vrátí **201** za nímž následuje odpověď prostředí OData, která obsahovala připojovací řetězec ve formátu prostého textu.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>S názvem rozpoznávání entit je nyní ukončena kognitivních dovedností

Při volání [rozpoznávání entit název](cognitive-search-skill-named-entity-recognition.md) dovedností v kódu, volání se nezdaří. Nahrazení funkce [rozpoznávání entit](cognitive-search-skill-entity-recognition.md). Byste měli být schopni nahraďte odkaz na dovednosti žádné další změny. Podpis rozhraní API je stejný pro obě verze. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroky pro upgrade
Pokud provádíte upgrade z předchozí verze GA, 2017-11-11 nebo 2016-09-01, pravděpodobně není nutné provádět žádné změny kódu, jiné než Chcete-li změnit číslo verze. Jsou pouze situací, ve kterých může být nutné změnit kód, pokud:

* Vašemu kódu nepodaří při nerozpoznaný vlastnosti jsou vráceny v odpovědi rozhraní API. Ve výchozím nastavení vaše aplikace by měl Ignorovat vlastnosti, které není srozumitelný.

* Váš kód nevyřeší požadavků rozhraní API a pokusí se přeposlat na novou verzi rozhraní API. Například k tomu může dojít, pokud vaše aplikace bude pokračování tokeny vrácená z rozhraní API pro vyhledávání (Další informace vyhledejte `@search.nextPageParameters` v [Reference k rozhraní API hledání](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Pokud se na vás vztahovat jeden z těchto situací, budete muset změnit váš kód odpovídajícím způsobem. V opačném případě žádné změny by měl být nezbytné, pokud chcete začít používat [nové funkce](#WhatsNew) verze 2019-05-06.

Pokud provádíte upgrade z verze preview rozhraní API, výše uvedené platí také, ale také musí být vědomi, že některé funkce ve verzi preview nejsou k dispozici ve verzi 2019-05-06:

* ["Informace následujícím způsobem" dotazy](search-more-like-this.md)
* [Indexování objektů blob CSV](search-howto-index-csv-blobs.md)
* [Podpora rozhraní API MongoDB pro indexery Cosmos DB](search-howto-index-cosmosdb.md)

Pokud váš kód používá tyto funkce, nebude možné upgradovat na verzi 2019-05-06 rozhraní API bez nutnosti odebírání využití z nich.

> [!IMPORTANT]
> Rozhraní API ve verzi Preview jsou určené pro testování a hodnocení a neměli byste používat v produkčním prostředí.
> 

### <a name="upgrading-complex-types"></a>Upgrade komplexní typy

Pokud váš kód používá komplexní typy se starší verzí preview rozhraní API 2017-11-11-Preview nebo 2016-09-01-Preview, existují některá omezení nové a změněné ve verzi 2019-05-06, které musíte vědět:

+ Mít byla snížena omezení hloubky dílčích polí a počet složitých kolekce na index. Pokud jste vytvořili indexy, které tyto limity pomocí verze rozhraní api ve verzi preview, žádný pokus o aktualizaci nebo je znovu vytvořit pomocí verze 2019-05-06 rozhraní API se nezdaří. Pokud se vás to týká, je potřeba změnit návrh schématu vejít do nové limity a pak znovu vytvořte index.

+ Ve verzi api-version. 2019-05-06 na počet prvků objektu komplexní kolekce na dokument je nový limit. Pokud jste vytvořili indexy s dokumenty, které tyto limity pomocí verze rozhraní api ve verzi preview, se nezdaří jakýkoliv pokus o přeindexování data pomocí rozhraní api-version. 2019-05-06. Pokud pro vás platí, musíte ke snížení počtu prvků komplexní kolekci v jednom dokumentu před přeindexování vaše data.

Další informace najdete v tématu [omezení služby Azure Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Postup upgradu původní struktura komplexní typ

Pokud váš kód používá komplexní typy s jedním ze starší verze rozhraní API ve verzi preview, může pomocí formátu definice indexu, který vypadá takto:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Novější stromu jako formát pro definování indexu pole byla zavedena v rozhraní API verze 2017-11-11-Preview. V novém formátu každé komplexní pole má kolekci polí, kde jsou definovány jeho dílčí pole. V rozhraní API verze 2019-05-06 tento nový formát se používá výhradně a se nezdaří pokus o vytvoření nebo aktualizaci indexu pomocí starý formát. Pokud máte indexy vytvořené využitím starý formát, budete muset použít rozhraní API verze 2017-11-11-Preview je aktualizovat na nový formát předtím, než bylo možné je spravovat pomocí rozhraní API verze 2019-05-06.

V následujících krocích pomocí rozhraní API verze 2017-11-11-Preview můžete aktualizovat "ploché" indexy na nový formát:

1. Proveďte požadavek GET na načtení indexu. Pokud je již v novém formátu, jste hotovi.

2. Převede uzel indexu z formátu "ploché" na nový formát. Budete muset psát kód pro tento, protože v době psaní tohoto textu není k dispozici žádná ukázka kódu.

3. Proveďte požadavek PUT se aktualizovat index na nový formát. Ujistěte se, že nebudou je moci měnit další podrobnosti o index, jako je například vyhledatelnost/filterability pole, protože to není povolené v rozhraní API aktualizace indexu.

> [!NOTE]
> Není možné spravovat indexy vytvořené pomocí starý formát "ploché" z portálu Azure portal. K reprezentaci "stromu" nejdříve upgradujte indexů z "ploché" reprezentace.

## <a name="next-steps"></a>Další postup

Projděte si referenční dokumentaci rozhraní REST API služby Azure Search. Pokud narazíte na potíže, požádat nás o pomoc na [StackOverflow](https://stackoverflow.com/) nebo [obraťte se na podporu](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Vyhledávací služba Reference k rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/)

