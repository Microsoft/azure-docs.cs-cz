---
title: Upgradovat na nejnovější verzi služby Azure Kognitivní hledání REST API
titleSuffix: Azure Cognitive Search
description: Projděte si rozdíly ve verzích rozhraní API a zjistěte, které akce jsou potřeba k migraci stávajícího kódu na nejnovější REST API verzi služby Azure Kognitivní hledání.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a9bffb41cce030b7a63e600e5ffaf65130261b4c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791161"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Upgradovat na nejnovější verzi služby Azure Kognitivní hledání REST API

Pokud používáte předchozí verzi [vyhledávacího REST API](https://docs.microsoft.com/rest/api/searchservice/), Tento článek vám pomůže při upgradu vaší aplikace tak, aby používala nejnovější všeobecně dostupnou verzi rozhraní API 2019-05-06.

Verze 2019-05-06 REST API obsahuje některé změny z dřívějších verzí. Jsou to většinou zpětně kompatibilní, takže změna kódu by měla vyžadovat jenom minimální úsilí v závislosti na verzi, kterou jste předtím používali. [Postup upgradu](#UpgradeSteps) popisuje změny kódu, které jsou potřebné pro použití nových funkcí.

> [!NOTE]
> Instance služby Azure Kognitivní hledání podporuje rozsah REST API verzí, včetně předchozích. Tyto verze rozhraní API můžete dál používat, ale doporučujeme migrovat kód na nejnovější verzi, abyste měli přístup k novým funkcím.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Co je nového ve verzi 2019-05-06
Verze 2019-05-06 je nejnovější všeobecně dostupná verze REST API. Funkce, které přešly na všeobecně dostupný stav této verze rozhraní API, zahrnují:

* [Automatické dokončování](index-add-suggesters.md) je funkce typeahead, která dokončí částečně zadaný pojem vstupu.

* [Komplexní typy](search-howto-complex-data-types.md) poskytují nativní podporu pro data strukturovaných objektů v indexu vyhledávání.

* [Režimy analýzy JsonLines](search-howto-index-json-blobs.md), součást indexování objektů BLOB v Azure, vytvoří jeden vyhledávací dokument pro každou entitu JSON, která je oddělená novým řádkem.

* [Rozšíření AI](cognitive-search-concept-intro.md) poskytuje indexování, která využívá moduly obohacení AI Cognitive Services.

Tato všeobecně dostupná aktualizace se shoduje s několika verzemi funkcí verze Preview. Seznam nových funkcí ve verzi Preview najdete v tématu věnovaném [hledání rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Změny způsobující chyby

Existující kód, který obsahuje následující funkce, bude přerušit rozhraní API-Version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexer pro Azure Cosmos DB-DataSource je nyní "Type": "cosmosdb"

Pokud používáte [Cosmos DB indexer](search-howto-index-cosmosdb.md ), musíte změnit `"type": "documentdb"` na `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Chyby výsledků spuštění indexeru už nejsou ve stavu.

Struktura chyb pro provádění indexeru dříve měla `status` element. Tento prvek byl odebrán, protože neposkytl užitečné informace.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Rozhraní API zdroje dat indexeru už nevrací připojovací řetězce.

V rozhraní API verze 2019-05-06 a 2019-05-06-Preview a vyšší, rozhraní API zdroje dat už v reakci na operaci REST nevrací připojovací řetězce. V předchozích verzích rozhraní API byly pro zdroje dat vytvořené pomocí POST Kognitivní hledání Azure vrátil **201** a odpověď OData, která obsahovala připojovací řetězec v prostém textu.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Již je ukončeno rozpoznávání pojmenovaných entit.

Pokud ve svém kódu voláte dovednost [rozpoznávání entit název](cognitive-search-skill-named-entity-recognition.md) , volání se nezdaří. Nahrazování funkcí je [rozpoznávání entit](cognitive-search-skill-entity-recognition.md). Měli byste být schopni nahradit odkaz na dovednosti bez dalších změn. Podpis rozhraní API je pro obě verze stejný. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu
Pokud provádíte upgrade z předchozí verze GA, 2017-11-11 nebo 2016-09-01, pravděpodobně nebudete muset provádět žádné změny v kódu, a to i v případě, že změníte číslo verze. Jedinými situacemi, ve kterých může být nutné změnit kód, jsou:

* Pokud se v odpovědi rozhraní API vrátí nerozpoznané vlastnosti, váš kód se nezdařil. Ve výchozím nastavení by vaše aplikace měla ignorovat vlastnosti, které nerozumí.

* Váš kód uchovává požadavky rozhraní API a pokusí se je znovu odeslat do nové verze rozhraní API. K tomu může dojít například v případě, že vaše aplikace udržuje tokeny pokračování vracené z rozhraní API pro hledání (Další informace najdete v [referenčních informacích k rozhraní API hledání](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)`@search.nextPageParameters`).

Pokud se vám některé z těchto situací vztahují, možná budete muset změnit kód odpovídajícím způsobem. V opačném případě by neměly být potřebné žádné změny, pokud nechcete začít používat [nové funkce](#WhatsNew) verze 2019-05-06.

Pokud provádíte upgrade z verze Preview rozhraní API, platí i výše, ale je také nutné si uvědomit, že některé funkce verze Preview nejsou k dispozici ve verzi 2019-05-06:

* ["Podobně jako tento" dotazy](search-more-like-this.md)
* [Indexování objektů BLOB ve formátu CSV](search-howto-index-csv-blobs.md)
* [Podpora MongoDB API pro indexery Cosmos DB](search-howto-index-cosmosdb.md)

Pokud váš kód používá tyto funkce, nebudete moct upgradovat na rozhraní API verze 2019-05-06 bez odebrání jejich použití.

> [!IMPORTANT]
> Rozhraní API ve verzi Preview jsou určená pro testování a vyhodnocení a neměla by se používat v produkčních prostředích.
> 

### <a name="upgrading-complex-types"></a>Upgrade složitých typů

Pokud váš kód používá komplexní typy s verzí starší verze Preview 2017-11-11-Preview nebo 2016-09-01-Preview, existuje několik nových a změněných omezení verze 2019-05-06, o kterých potřebujete vědět:

+ Omezení hloubky dílčích polí a počtu složitých kolekcí na index byly sníženy. Pokud jste vytvořili indexy, které překračují tato omezení pomocí verze Preview rozhraní API, všechny pokusy o jejich aktualizaci nebo jejich opětovné vytvoření pomocí rozhraní API verze 2019-05-06 selžou. Pokud to platí pro vás, budete muset změnit návrh schématu tak, aby odpovídal novému omezení, a pak index znovu sestavit.

+ Rozhraní API-Version 2019-05-06 obsahuje nový limit počtu prvků komplexních kolekcí na dokument. Pokud jste vytvořili indexy s dokumenty, které překračují tato omezení pomocí verze Preview rozhraní API, všechny pokus o Reindexování těchto dat pomocí rozhraní API-Version 2019-05-06 se nezdaří. Pokud to platí pro vás, budete muset před změnou indexu dat snížit počet komplexních prvků kolekce na dokument.

Další informace najdete v tématu [omezení služby pro Azure kognitivní hledání](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Postup upgradu staré struktury komplexního typu

Pokud váš kód používá komplexní typy s jednou ze starších verzí rozhraní API verze Preview, možná použijete formát definice indexu, který vypadá takto:

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

Novější formát stromu pro definování indexovaných polí byl představený v rozhraní API verze 2017-11-11-Preview. V novém formátu má každé komplexní pole kolekci pole, kde jsou definována jeho dílčí pole. V rozhraní API verze 2019-05-06 se tento nový formát používá výhradně a pokus o vytvoření nebo aktualizaci indexu pomocí starého formátu selže. Pokud máte indexy vytvořené pomocí starého formátu, budete muset použít rozhraní API verze 2017-11-11-Preview, abyste je aktualizovali do nového formátu předtím, než je můžete spravovat pomocí rozhraní API verze 2019-05-06.

Pomocí následujících kroků můžete pomocí rozhraní API verze 2017-11-11-Preview aktualizovat v novém formátu "ploché" indexy:

1. Proveďte požadavek GET k načtení indexu. Pokud už je v novém formátu, jste hotovi.

2. Převede index z formátu "Flate" do nového formátu. Pro tuto operaci budete muset napsat kód, protože v době psaní tohoto zápisu není k dispozici žádný vzorový kód.

3. Proveďte požadavek PUT a aktualizujte index na nový formát. Ujistěte se, že nechcete měnit žádné další podrobnosti o indexu, jako je například možnost Prohledat nebo filtrovat pole, protože to není povoleno rozhraním API aktualizace indexu.

> [!NOTE]
> Není možné spravovat indexy vytvořené se starým formátem "plochý" z Azure Portal. Upgradujte prosím své indexy z "ploché" reprezentace na "strom", a to nejdřívějším pohodlím.

## <a name="next-steps"></a>Další kroky

Projděte si referenční dokumentaci REST API hledání. Pokud narazíte na problémy, požádejte nás o pomoc na [StackOverflow](https://stackoverflow.com/) nebo [kontaktujte podporu](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Odkaz na REST API služby Search](https://docs.microsoft.com/rest/api/searchservice/)

