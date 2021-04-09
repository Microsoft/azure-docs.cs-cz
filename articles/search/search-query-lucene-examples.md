---
title: Použít úplnou syntaxi dotazů Lucene
titleSuffix: Azure Cognitive Search
description: Příklady dotazů, které demonstrují syntaxi dotazů Lucene pro přibližné vyhledávání, vyhledávání blízkých výrazů, zvyšování skóre termínů, hledání regulárních výrazů a hledání pomocí zástupných znaků v indexu služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693556"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Použít úplnou syntaxi hledání Lucene (rozšířené dotazy v Azure Kognitivní hledání)

Při sestavování dotazů pro Azure Kognitivní hledání můžete nahradit výchozí [jednoduchý analyzátor dotazů](query-simple-syntax.md) výkonnějším [analyzátorem dotazů Lucene](query-lucene-syntax.md) a formulovat specializované a pokročilé výrazy dotazů.

Analyzátor Lucene podporuje složité formáty dotazů, jako jsou dotazy v oboru pole, přibližné vyhledávání, vpony a přípona zástupného znaku přípony, hledání blízkosti, zvyšování skóre a hledání v regulárních výrazech. Další napájení přináší dodatečné požadavky na zpracování, takže byste měli očekávat trochu delší dobu provádění. V tomto článku si můžete projít příklady, které demonstrují operace s dotazy na základě úplné syntaxe.

> [!Note]
> Mnohé z specializovaných konstrukcí dotazů, které jsou povolené prostřednictvím úplné syntaxe dotazů Lucene, nejsou [analyzovány textem](search-lucene-query-architecture.md#stage-2-lexical-analysis), které mohou být překvapivé, pokud očekáváte odvozování nebo lemmatizátor nebo předzpracování. Lexikální analýza je prováděna pouze za úplných podmínek (dotaz dotaz nebo fráze). Typy dotazů s neúplnými podmínkami (dotaz na předponu, dotaz se zástupnými znaky, regulární dotaz, nepřibližný dotaz) jsou přidány přímo do stromu dotazu a vycházejí z fáze analýzy. Jediná transformace prováděná na částečných výrazech dotazu je lowercasing. 
>

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
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "hledání" nastavené na `*` je nespecifikovaný dotaz, který odpovídá hodnotě null nebo prázdné vyhledávání. Není to zvláště užitečné, ale je to nejjednodušší hledání, které můžete provést, a zobrazuje všechna pole, která lze načíst v indexu, včetně všech hodnot.

+ možnost "queryType" nastavenou na hodnotu Full vyvolá úplný analyzátor dotazů Lucene a je pro tuto syntaxi nutné.

+ pro kompozici výsledků hledání je použita možnost vybrat jako seznam polí oddělených čárkami, včetně pouze těch polí, která jsou užitečná v kontextu výsledků hledání.

+ možnost Count vrátí počet dokumentů, které odpovídají kritériím vyhledávání. U prázdného vyhledávacího řetězce bude počet všechny dokumenty v indexu (50 v případě hotelů-Sample-index).

## <a name="example-1-fielded-search"></a>Příklad 1: hledání v poli

Jednotlivé obory vyhledávání v terénu, vložené vyhledávací výrazy do konkrétního pole. V tomto příkladu se vyhledá názvy hotelů s termínem "Hotel", ale ne "Motel". Můžete zadat více polí pomocí a. 

Při použití této syntaxe dotazu můžete vynechat parametr "searchFields", pokud se pole, která chcete dotazovat, nachází ve výrazu hledání samotného. Pokud zahrnete "searchFields" s polem hledání, `fieldName:searchExpression` vždycky má přednost před "searchFields".

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

Odpověď na tento dotaz by měla vypadat podobně jako v následujícím příkladu, který je filtrovaný podle "možnosti" a hesla "a vrací hotely, které v názvu obsahují" Hotel "nebo" Motel ".

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

Výraz hledání může být jediný výraz nebo fráze nebo složitější výraz v závorkách, volitelně s logickými operátory. Mezi příklady patří následující:

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

Nezapomeňte umístit frázi v uvozovkách, pokud chcete, aby oba řetězce byly vyhodnoceny jako jediná entita, jako v tomto případě vyhledávání dvou různých míst v poli Adresa/StateProvince. V závislosti na klientovi možná budete muset řídicí znaky () označit uvozovkami ( `\` ).

Pole zadané v `fieldName:searchExpression` musí být vyhledávací pole. Podrobnosti o tom, jak jsou atributy pole, naleznete v tématu [Create index (REST API)](/rest/api/searchservice/create-index) .

## <a name="example-2-fuzzy-search"></a>Příklad 2: hledání fuzzy

Přibližné shody hledání podle termínů, které jsou podobné, včetně nesprávně napsaných slov. Chcete-li provést hledání přibližné hodnoty, přidejte na `~` konci jednoho slova symbol tildy s volitelným parametrem, hodnotu mezi 0 a 2, která určuje vzdálenost úprav. Například `blue~` nebo `blue~1` by vrátil modrou, blues a glue.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

Odpověď na tento dotaz se přeloží na "concierge" ve vyhovujících dokumentech, které jsou oříznuté pro zkrácení:

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

Fráze nejsou podporované přímo, ale můžete zadat přibližnou shodu pro každý termín fráze s více částmi, jako je například `search=Tags:landy~ AND sevic~` .  Tento výraz dotazu najde 15 shod v "službě prádelní".

> [!Note]
> Fuzzy dotazy nejsou [analyzovány](search-lucene-query-architecture.md#stage-2-lexical-analysis). Typy dotazů s neúplnými podmínkami (dotaz na předponu, dotaz se zástupnými znaky, regulární dotaz, nepřibližný dotaz) jsou přidány přímo do stromu dotazu a vycházejí z fáze analýzy. Jediná transformace prováděná na částečných výrazech dotazu je menší velikost písmen.
>

## <a name="example-3-proximity-search"></a>Příklad 3: hledání blízkosti

Hledání v blízkosti vyhledává v dokumentu skoro stejné výrazy. Vložte symbol tildy "~" na konec fráze následovaný počtem slov, která vytvářejí hranice blízkosti.

Tento dotaz vyhledá termíny "Hotel" a "letiště" do 5 slov navzájem v dokumentu. Uvozovky jsou uvozeny řídicími znaky ( `\"` ), aby se zachovala fráze:

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako v následujícím příkladu:

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>Příklad 4: zvyšování termínu

Termín zvyšování skóre označuje, že dokument je vyšší, pokud obsahuje posílený termín vzhledem k dokumentům, které neobsahují termín. Chcete-li zvýšit výraz, použijte blikající kurzor, `^` symbol s faktorem zvýšení (číslo) na konci hledaného výrazu. Výchozí faktor zvyšování hodnot je 1 a i když musí být kladný, může být menší než 1 (například 0,2). Zvyšování termínů se liší od profilů vyhodnocování v tom, že profily vyhodnocování zvyšují určitá pole, nikoli konkrétní podmínky.

V tomto dotazu "před" vyhledejte "" plážový Access "a Všimněte si, že na jednom nebo obou případech se shodují sedm dokumentů.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

Ve skutečnosti existuje pouze jeden dokument, který odpovídá "Access" a protože se jedná o jedinou shodu, umístění je vysoké (druhé umístění), i když v dokumentu chybí termín "plážový".

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

V dotazu "po" Opakujte hledání, tentokrát se posilováním výsledků s výrazem "plážový" v termínu "přístup". Verze dotazu pro humánní čtení je `search=Description:beach^2 access` . V závislosti na vašem klientovi možná budete muset vyjádřit `^2` jako `%5E2` .

Po zvýšení termínu "plážový" se shoda na starém hotelu Carrabelle posune dolů na šest míst.

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>Příklad 5: Regex

Hledání regulárního výrazu vyhledá shodu na základě obsahu mezi lomítky "/", jak je uvedeno ve [třídě RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako v následujícím příkladu:

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> Dotazy regulárního výrazu nejsou [analyzovány](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Jediná transformace prováděná na částečných výrazech dotazu je menší velikost písmen.
>

## <a name="example-6-wildcard-search"></a>Příklad 6: hledání pomocí zástupných znaků

Můžete použít obecně rozpoznanou syntaxi pro hledání zástupných znaků vícenásobného () `*` nebo Single ( `?` ). Všimněte si, že analyzátor dotazů Lucene podporuje použití těchto symbolů s jedním výrazem a nikoli frází.

V tomto dotazu vyhledejte názvy hotelu, které obsahují předponu ' SC '. `*` `?` Jako první znak hledání nelze použít symbol nebo.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako v následujícím příkladu:

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> Dotazy se zástupnými znaky nejsou [analyzovány](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Jediná transformace prováděná na částečných výrazech dotazu je menší velikost písmen.
>

## <a name="next-steps"></a>Další kroky

Zkuste zadat dotazy v kódu. Následující odkazy vysvětlují, jak nastavit vyhledávací dotazy pomocí sad Azure SDK.

+ [Dotazování indexu pomocí sady .NET SDK](search-get-started-dotnet.md)
+ [Dotazování indexu pomocí sady Python SDK](search-get-started-python.md)
+ [Dotazování indexu pomocí sady JavaScript SDK](search-get-started-javascript.md)

Další informace o syntaxi, architektuře dotazů a příkladech najdete na následujících odkazech:

+ [Příklady dotazů syntaxe Lucene pro vytváření pokročilých dotazů](search-query-lucene-examples.md)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Jednoduchá syntaxe dotazů](query-simple-syntax.md)
+ [Úplná syntaxe dotazů Lucene](query-lucene-syntax.md)
+ [Syntaxe filtru](search-query-odata-filter.md)