---
title: Upgradovat REST API verze
titleSuffix: Azure Cognitive Search
description: Projděte si rozdíly ve verzích rozhraní API a zjistěte, které akce jsou potřeba k migraci stávajícího kódu na nejnovější REST API verzi služby Azure Kognitivní hledání.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d7734fde529c24e8113ea3b019d343b7223f0122
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91929638"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Upgrade na nejnovější REST API v Azure Kognitivní hledání

Pokud používáte starší verzi [**vyhledávacího REST API**](/rest/api/searchservice/), Tento článek vám pomůže při upgradu aplikace na nejnovější všeobecně dostupnou verzi rozhraní API **2020-06-30**.

Verze 2020-06-30 obsahuje důležitou novou funkci ([znalostní báze Knowledge Store](knowledge-store-concept-intro.md)) a zavádí několik menších změn chování. V takovém případě je tato verze většinou zpětně kompatibilní, takže změny kódu by měly být minimální, pokud upgradujete z předchozí verze (2019-05-06).

> [!NOTE]
> Vyhledávací služba podporuje řadu REST API verzí, včetně předchozích. Tyto verze rozhraní API můžete dál používat, ale doporučujeme migrovat kód na nejnovější verzi, abyste měli přístup k novým funkcím. V průběhu času budou zastaralé verze REST API zastaralé a [už se nepodporují](search-api-versions.md#unsupported-versions).

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>Postup upgradu

Při upgradu na novou verzi pravděpodobně nebudete muset provádět mnoho změn v kódu, a to i v případě, že změníte číslo verze. Jedinými situacemi, ve kterých může být nutné změnit kód, jsou:

* Pokud se v odpovědi rozhraní API vrátí nerozpoznané vlastnosti, váš kód se nezdařil. Ve výchozím nastavení by vaše aplikace měla ignorovat vlastnosti, které nerozumí.

* Váš kód uchovává požadavky rozhraní API a pokusí se je znovu odeslat do nové verze rozhraní API. K tomu může dojít například v případě, že vaše aplikace udržuje tokeny pokračování vracené z rozhraní API pro hledání (Další informace najdete `@search.nextPageParameters` v [referenčních informacích rozhraní API hledání](/rest/api/searchservice/Search-Documents)).

* Váš kód odkazuje na verzi rozhraní API, která předchází datu 2019-05-06 a podléhá jedné nebo více zásadním změnám v této verzi. Další podrobnosti najdete v části [upgrade na 2019-05-06](#upgrade-to-2019-05-06) . 

Pokud se vám některé z těchto situací vztahují, budete možná muset změnit kód odpovídajícím způsobem. V opačném případě by neměly být nutné žádné změny, i když budete chtít začít používat funkce přidané v nové verzi.

## <a name="upgrade-to-2020-06-30"></a>Upgradovat na 2020-06-30

Verze 2020-06-30 je nová všeobecně dostupná verze REST API. Existuje jedna zásadní změna a několik rozdílů v chování. 

Funkce jsou teď obecně dostupné v této verzi rozhraní API:

* [Znalostní báze](knowledge-store-concept-intro.md), trvalé úložiště obohaceného obsahu vytvořeného prostřednictvím dovednosti vytvořené pro analýzu a zpracování dat prostřednictvím jiných aplikací. Díky této funkci může kanál rozšíření AI řízený indexerem naplnit úložiště znalostí kromě indexu vyhledávání. Pokud jste použili verzi Preview této funkce, je ekvivalentem všeobecně dostupné verze. Jediná požadovaná změna kódu mění verzi rozhraní API.

### <a name="breaking-change"></a>Zásadní změna

Stávající kód napsaný pro předchozí verze rozhraní API se přeruší u rozhraní API-Version = 2020-06-30 a novější, pokud kód obsahuje následující funkce:

* Všechny literály EDM. Date (datum tvořený rokem v měsíci, například `2020-12-12` ) ve výrazech filtru musí splňovat formát EDM. DateTimeOffset: `2020-12-12T00:00:00Z` . Tato změna byla nutná ke zpracování chybných nebo neočekávaných výsledků dotazu z důvodu rozdílů v časovém pásmu.

### <a name="behavior-changes"></a>Změny chování

* [Algoritmus řazení BM25](index-ranking-similarity.md) nahrazuje algoritmus předchozí klasifikace novější technologií. Nové služby budou tento algoritmus používat automaticky. Pro existující služby musíte nastavit parametry pro použití nového algoritmu.

* Seřazené výsledky pro hodnoty null se v této verzi změnily, přičemž hodnoty null se zobrazují jako první, pokud je řazení `asc` a poslední `desc` . Pokud jste napsali kód pro zpracování, jak jsou hodnoty null seřazené, nezapomeňte o této změně.

## <a name="upgrade-to-2019-05-06"></a>Upgradovat na 2019-05-06

Verze 2019-05-06 je předchozí všeobecně dostupná verze REST API. K funkcím, které se všeobecně k dispozici v této verzi rozhraní API, patří:

* [Automatické dokončování](index-add-suggesters.md) je funkce typeahead, která dokončí částečně zadaný pojem vstupu.
* [Komplexní typy](search-howto-complex-data-types.md) poskytují nativní podporu pro data strukturovaných objektů v indexu vyhledávání.
* [Režimy analýzy JsonLines](search-howto-index-json-blobs.md), součást indexování objektů BLOB v Azure, vytvoří jeden vyhledávací dokument pro každou entitu JSON, která je oddělená novým řádkem.
* [Rozšíření AI](cognitive-search-concept-intro.md) poskytuje indexování, která využívá moduly obohacení AI Cognitive Services.

### <a name="breaking-changes"></a>Změny způsobující chyby

Existující kód napsaný pro předchozí verze rozhraní API se přeruší u rozhraní API-Version = 2019-05-06 a novější, pokud kód obsahuje následující funkce:

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexer pro Azure Cosmos DB-DataSource je nyní "Type": "cosmosdb"

Pokud používáte [Cosmos DB indexer](search-howto-index-cosmosdb.md ), musíte změnit `"type": "documentdb"` na `"type": "cosmosdb"` .

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Chyby výsledků spuštění indexeru už nejsou ve stavu.

Struktura chyb pro provádění indexeru dříve měla `status` element. Tento prvek byl odebrán, protože neposkytl užitečné informace.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Rozhraní API zdroje dat indexeru už nevrací připojovací řetězce.

V rozhraní API verze 2019-05-06 a 2019-05-06-Preview a vyšší, rozhraní API zdroje dat už v reakci na operaci REST nevrací připojovací řetězce. V předchozích verzích rozhraní API byly pro zdroje dat vytvořené pomocí POST Kognitivní hledání Azure vrátil **201** a odpověď OData, která obsahovala připojovací řetězec v prostém textu.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Již je ukončeno rozpoznávání pojmenovaných entit.

Pokud jste ve svém kódu volali název dovednosti pro [rozpoznávání entit](cognitive-search-skill-named-entity-recognition.md) , volání se nezdaří. Nahrazování funkcí je [rozpoznávání entit](cognitive-search-skill-entity-recognition.md). Měli byste být schopni nahradit odkaz na dovednosti bez dalších změn. Podpis rozhraní API je pro obě verze stejný. 

### <a name="upgrading-complex-types"></a>Upgrade složitých typů

Rozhraní API verze 2019-05-06 přidalo formální podporu pro komplexní typy. Pokud váš kód implementoval předchozí doporučení pro typ rovnocennosti v 2017-11-11-Preview nebo 2016-09-01-Preview, existují některá nová a změněná omezení počínaje verzí 2019-05-06, o kterých potřebujete vědět:

+ Omezení hloubky dílčích polí a počtu složitých kolekcí na index byly sníženy. Pokud jste vytvořili indexy, které překračují tato omezení pomocí verze Preview rozhraní API, všechny pokusy o jejich aktualizaci nebo jejich opětovné vytvoření pomocí rozhraní API verze 2019-05-06 selžou. Pokud to platí pro vás, budete muset změnit návrh schématu tak, aby odpovídal novému omezení, a pak index znovu sestavit.

+ V rozhraní API-Version 2019-05-06 se začíná nové omezení pro počet prvků komplexních kolekcí na dokument. Pokud jste vytvořili indexy s dokumenty, které překračují tato omezení pomocí verze Preview rozhraní API, všechny pokus o Reindexování těchto dat pomocí rozhraní API-Version 2019-05-06 se nezdaří. Pokud to platí pro vás, budete muset před změnou indexu dat snížit počet komplexních prvků kolekce na dokument.

Další informace najdete v tématu [omezení služby pro Azure kognitivní hledání](search-limits-quotas-capacity.md).

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Postup upgradu staré struktury komplexního typu

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

Projděte si referenční dokumentaci REST API hledání. Pokud narazíte na problémy, požádejte nás, abychom vám pomohli [Stack Overflow](https://stackoverflow.com/) nebo [kontaktujte podporu](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Odkaz na REST API služby Search](/rest/api/searchservice/)
