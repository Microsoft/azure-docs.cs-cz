---
title: Použít jednoduchou syntaxi dotazů Lucene
titleSuffix: Azure Cognitive Search
description: Příklady dotazů, které demonstrují jednoduchou syntaxi pro fulltextové vyhledávání, filtrování vyhledávání a geografické hledání oproti indexu služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694032"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Použití jednoduché syntaxe hledání v Azure Kognitivní hledání

V Azure Kognitivní hledání vyvolá [Jednoduchá syntaxe dotazů](query-simple-syntax.md) výchozí analyzátor dotazu pro fulltextové vyhledávání. Analyzátor je rychlý a pracuje s běžnými scénáři, včetně fulltextového vyhledávání, filtrovaných a omezujících výsledků hledání a hledání předpon. Tento článek používá příklady k ilustraci jednoduchého použití syntaxe v žádosti [hledání dokumentů (REST API)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> Alternativná syntaxe dotazu je [Úplná Lucene](query-lucene-syntax.md), která podporuje složitější struktury dotazů, jako je hledání přibližných a zástupných znaků. Další informace a příklady najdete v tématu [použití úplné syntaxe Lucene](search-query-lucene-examples.md).

## <a name="hotels-sample-index"></a>Vzorový index hotelů

Následující dotazy jsou založené na pohostincích – vzorový index, který můžete vytvořit podle pokynů v tomto [rychlém](search-get-started-portal.md)startu.

Příklady dotazů jsou klouby pomocí REST API a POST. Můžete je vkládat a spouštět v nástroji [post](search-get-started-rest.md) nebo v [Visual Studio Code s rozšířením kognitivní hledání](search-get-started-vs-code.md).

Hlavičky požadavku musí mít následující hodnoty:

| Klíč | Hodnota |
|-----|-------|
| Typ obsahu | application/json|
| klíč rozhraní API  | `<your-search-service-api-key>`, buď dotaz, nebo klíč správce |

Parametry identifikátoru URI musí zahrnovat koncový bod vyhledávací služby s názvem indexu, kolekcemi dokumentů, příkazem hledání a verzí rozhraní API, podobně jako v následujícím příkladu:

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

Text požadavku by měl být vytvořen jako platný kód JSON:

```json
{
    "search": "*",
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "hledání" nastavené na `*` je nespecifikovaný dotaz, který odpovídá hodnotě null nebo prázdné vyhledávání. Není to zvláště užitečné, ale je to nejjednodušší hledání, které můžete provést, a zobrazuje všechna pole, která lze načíst v indexu, včetně všech hodnot.

+ Výchozí hodnota je "queryType" nastavená na "jednoduchá" a může být vynechána, ale je k dispozici pro další posílení, že příklady dotazů v tomto článku jsou vyjádřeny v jednoduché syntaxi.

+ pro kompozici výsledků hledání je použita možnost vybrat jako seznam polí oddělených čárkami, včetně pouze těch polí, která jsou užitečná v kontextu výsledků hledání.

+ možnost Count vrátí počet dokumentů, které odpovídají kritériím vyhledávání. U prázdného vyhledávacího řetězce bude počet všechny dokumenty v indexu (50 v případě hotelů-Sample-index).

## <a name="example-1-full-text-search"></a>Příklad 1: fulltextové vyhledávání

Fulltextové vyhledávání může být libovolný počet samostatných výrazů nebo frází uzavřených v uvozovkách s logickými operátory nebo bez nich. 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

Hledání klíčového slova, které se skládá z důležitých podmínek nebo frází, má za následek nejlépe fungovat. Pole řetězců se při indexování a dotazování procházejí analýzou textu a odstraňují nepotřebná slova, jako jsou ",", "a", "IT". Chcete-li zjistit, jak je řetězec dotazu v indexu založen na tokenu, předejte řetězec v volání funkce [analyzovat text](/rest/api/searchservice/test-analyzer) do indexu.

Parametr "searchMode" řídí přesnost a odvolání. Pokud požadujete další odvolání, použijte výchozí hodnotu any, která vrátí výsledek, pokud je splněna kterákoli část řetězce dotazu. Pokud upřednostňujete přesnost, kde musí být všechny části řetězce spárovány, změňte searchMode na "All". Vyzkoušejte výše uvedený dotaz oběma způsoby, jak zjistit, jak searchMode mění výsledek.

Odpověď na dotaz "fond Spa + letiště" by měla vypadat podobně jako v následujícím příkladu, oříznuté pro zkrácení.

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

Všimněte si skóre hledání v odpovědi. Toto je skóre významnosti shody. Ve výchozím nastavení vyhledávací služba vrátí prvních 50 shod, které jsou založené na tomto skóre.

K rovnoměrnému hodnocení "1,0" dochází v případě, že není k dispozici žádné pořadí, protože vyhledávání nevrátilo fulltextové vyhledávání, nebo vzhledem k tomu, že nebyla zadána žádná kritéria. Například při prázdném hledání (Search = `*` ) se řádky vrátí v libovolném pořadí. Pokud zahrnete skutečná kritéria, uvidíte, že výsledky hledání se budou vyvíjet na smysluplné hodnoty.

## <a name="example-2-look-up-by-id"></a>Příklad 2: Vyhledání podle ID

Když vrátíte výsledky hledání v dotazu, logický další krok je poskytnout stránku podrobností, která obsahuje více polí z dokumentu. Tento příklad ukazuje, jak vrátit jeden dokument pomocí [vyhledávacího dokumentu](/rest/api/searchservice/lookup-document) předáním ID dokumentu.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

Všechny dokumenty mají jedinečný identifikátor. Pokud používáte portál, vyberte kartu index z **indexů** a potom si prohlédněte definice polí a určete, které pole je klíčem. Pomocí REST vrátí volání [Get index](/rest/api/searchservice/get-index) definici indexu v těle odpovědi.

Odpověď pro výše uvedený dotaz se skládá z dokumentu, jehož klíč je 41. Každé pole, které je označeno jako "načístelné" v definici indexu, může být vráceno ve výsledcích hledání a vykresleno ve vaší aplikaci.

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>Příklad 3: filtrování textu

[Syntaxe filtru](search-query-odata-filter.md) je výraz OData, který můžete použít samostatně nebo pomocí příkazu "vyhledat". Společně se používá filtr, který se použije jako první pro celý index a pak se provede hledání na základě výsledků filtru. Používání filtrů tak může být užitečné pro zlepšení výkonu dotazů zmenšením sady dokumentů, které musí dotaz vyhledávání zpracovat.

Filtry je možné definovat u libovolného pole označeného jako "filtrovatelné" v definici indexu. Pro hotely – vzorový-index, pole s filtrováním zahrnují kategorii, značky, ParkingIncluded, hodnocení a většinu polí adresy.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Reakce na výše uvedený dotaz je vymezená jenom na hotely zařazené do kategorií jako "Report a Spa", které zahrnují výrazy "umění" nebo "prohlídky". V tomto případě existuje pouze jedna shoda.

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>Příklad 4: filtrování funkcí

Výrazy filtru můžou zahrnovat [funkce Search. POZVYHLEDAT a Search. ismatchscoring](search-query-odata-full-text-search-functions.md), které vám umožní vytvořit vyhledávací dotaz v rámci filtru. Tento výraz filtru používá zástupný znak *zdarma* pro výběr rekreačního nástroje včetně bezplatných Wi-Fi, bezplatného parkování a tak dále.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

Reakce na výše uvedený dotaz se shoduje s 19 hotely, které nabízejí bezplatného rekreačního. Všimněte si, že skóre hledání je v průběhu výsledků jednotné "1,0". Důvodem je, že vyhledávací výraz má hodnotu null nebo je prázdný, což vede k navýšení shody filtru, ale bez fulltextového vyhledávání. Skóre relevance se vrací jenom při fulltextovém vyhledávání. Pokud používáte filtry bez vyhledávání, ujistěte se, že máte dostatečná pole, abyste mohli řídit pořadí hledání.

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>Příklad 5: filtry rozsahu

Filtrování rozsahu je podporováno prostřednictvím výrazů filtrů pro libovolný datový typ. Následující příklady znázorňují číselné a řetězcové rozsahy. Datové typy jsou důležité v filtrech rozsahu a fungují nejlépe, pokud jsou číselná data v číselném poli a řetězcová data v polích řetězců. Číselná data v polích řetězců nejsou vhodná pro rozsah, protože číselné řetězce nejsou srovnatelné.

Následující dotaz je numerický rozsah. V části hotely – vzorový index je jediné vyfiltrované číselné pole hodnocení.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako v následujícím příkladu, oříznuté pro zkrácení.

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

Další dotaz je filtr rozsahu přes pole řetězce (adresa/StateProvince):

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako v následujícím příkladu, oříznuté pro zkrácení. V tomto příkladu není možné řadit podle StateProvince, protože pole není v definici indexu v atributu "Sorted".

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>Příklad 6: geografické hledání

Hotely – vzorový index obsahuje pole geo_location s souřadnicemi zeměpisné šířky a délky. V tomto příkladu se používá [funkce Geo. Distance](search-query-odata-geo-spatial-functions.md#examples) , která filtruje dokumenty v rámci obvodu počátečního bodu, a to až do libovolné vzdálenosti (v kilometrech), kterou zadáte. Poslední hodnotu v dotazu (10) můžete upravit tak, aby se snížila nebo rozšířila oblast povrchu dotazu.

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

Odpověď pro tento dotaz vrátí všechny hotely v rámci 10 kilometer vzdálenosti zadaných souřadnic:

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>Příklad 7: logické hodnoty s searchMode

Jednoduchá syntaxe podporuje logické operátory ve formě znaků ( `+, -, |` ) pro podporu operátorů and, or a NOT Query Logic. Logická hledání se chová stejně, jako byste očekávali, s několika výjimkami zajímavosti. 

V předchozích příkladech byl parametr "searchMode" zavedený jako mechanismus pro konkrétní přesnost a odvolání, přičemž "searchMode = any" (dokument, který splňuje některá kritéria se považuje za shodu) a "searchMode = All" upřednostňuje přesnost (všechna kritéria musí být v dokumentu shodná). 

V kontextu hledání typu Boolean může být výchozí hodnota "searchMode = any" matoucí, pokud vytváříte dotaz pomocí více operátorů a místo užších výsledků získáváte širší výsledky. To platí zejména u možnosti Ne, kde výsledky zahrnují všechny dokumenty "neobsahující" konkrétní výraz nebo frázi.

V následujícím příkladu je uvedena ukázka. Spuštěním následujícího dotazu s searchMode (any) se vrátí 42 dokumentů: těch, které obsahují pojem "restaurace", a také všechny dokumenty, které nemají frázi "klimatizace". 

Všimněte si, že mezi logickým operátorem ( `-` ) a frází "klimatizace" není mezera.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

Když se změní na "searchMode = All", vyhodnotí se Kumulativní efekt u kritérií a vrátí menší výslednou sadu (7 shod) sestávající z dokumentů obsahujících pojem "restaurace" a minus ty, které obsahují frázi "klimatizace".

Odpověď pro tento dotaz by teď vypadala podobně jako v následujícím příkladu, která se ořízne pro zkrácení.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>Příklad 8: výsledky stránkování

V předchozích příkladech jste se dozvěděli o parametrech, které ovlivňují složení výsledků hledání, včetně příkazu SELECT, který určuje, která pole jsou ve výsledku, seřadit objednávky a jak zahrnout počet všech shod. Tento příklad je pokračováním v sestavování výsledků hledání ve formě parametrů stránkování, které umožňují dávkovat počet výsledků, které se zobrazí na dané stránce. 

Ve výchozím nastavení vyhledávací služba vrátí prvních 50 shod. Chcete-li určit počet shod na každé stránce, použijte "Top" pro definování velikosti dávky a pak pomocí příkazu "Přeskočit" vyzvedněte následné dávky.

Následující příklad používá filtr a pořadí řazení v poli hodnocení (hodnocení je možné filtrovat a řadit), protože je snazší zobrazit účinky stránkování na seřazené výsledky. V běžném dotazu s úplným vyhledáváním jsou horní shody seřazeny podle pořadí a na stránkou @search.score .

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

Dotaz najde 21 odpovídajících dokumentů, ale vzhledem k tomu, že jste zadali "Top", odpověď vrátí jenom prvních pět shod, přičemž hodnocení začíná na 4,9 a končí na 4,7 s "Ladyem Lake B & B". 

Pokud chcete získat další 5, přeskočte první dávku:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

Odpověď na druhou dávku přeskočí prvních pět shod a vrátí další pět, počínaje "Pull'r Inn Motel". Pokud chcete pokračovat s dalšími dávkami, zachovejte "Top" v 5 a pak u každé nové žádosti zvýšíme "Přeskočit" o 5 na každou novou žádost (Přeskočit = 5, přeskočit = 10, přeskočit = 15 a tak dále).

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>Další kroky

Teď, když máte určitý postup se základní syntaxí dotazů, zkuste zadat dotazy v kódu. Následující odkazy vysvětlují, jak nastavit vyhledávací dotazy pomocí sad Azure SDK.

+ [Dotazování indexu pomocí sady .NET SDK](search-get-started-dotnet.md)
+ [Dotazování indexu pomocí sady Python SDK](search-get-started-python.md)
+ [Dotazování indexu pomocí sady JavaScript SDK](search-get-started-javascript.md)

Další informace o syntaxi, architektuře dotazů a příkladech najdete na následujících odkazech:

+ [Příklady dotazů syntaxe Lucene pro vytváření pokročilých dotazů](search-query-lucene-examples.md)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Jednoduchá syntaxe dotazů](query-simple-syntax.md)
+ [Úplná syntaxe dotazů Lucene](query-lucene-syntax.md)
+ [Syntaxe filtru](search-query-odata-filter.md)