---
title: Vytvořit sémantický dotaz
titleSuffix: Azure Cognitive Search
description: Nastavte sémantický typ dotazu pro připojení modelů hloubkového učení k zpracování dotazů, odvození záměru a kontextu v rámci pořadí hledání a relevance.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0af868f62f9bc62ee6b4b2a10d16f8eed632b6d3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679397"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Vytvoření sémantického dotazu v Kognitivní hledání

> [!IMPORTANT]
> Sémantický typ dotazu je ve verzi Public Preview, který je k dispozici ve verzi Preview REST API a Azure Portal. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Během úvodního spuštění Preview se neúčtují žádné poplatky za sémantické vyhledávání. Další informace najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

V tomto článku se dozvíte, jak formulovat požadavek hledání, který používá sémantické hodnocení, a vytváří sémantické titulky a odpovědi.

## <a name="prerequisites"></a>Požadavky

+ Vyhledávací služba na úrovni Standard (S1, S2, S3), která se nachází v jedné z těchto oblastí: Střed USA – sever, Západní USA, Západní USA 2, Východní USA 2, Severní Evropa, Západní Evropa. Pokud máte v jedné z těchto oblastí existující službu S1 nebo větší, můžete požádat o přístup bez nutnosti vytvářet novou službu.

+ Přístup ke službě sémantického vyhledávání ve verzi Preview: [registrace](https://aka.ms/SemanticSearchPreviewSignup)

+ Existující index vyhledávání, který obsahuje anglický obsah

+ Vyhledávací klient pro odesílání dotazů

  Vyhledávací klient musí v žádosti o dotaz podporovat rozhraní REST API pro náhled. Můžete použít [post](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)nebo kód, který jste změnili, aby se v rozhraní API verze Preview zavolala volání REST. Můžete také použít [Průzkumníka vyhledávání](search-explorer.md) v Azure Portal k odeslání sémantického dotazu.

+ [Dokument hledání](/rest/api/searchservice/preview-api/search-documents) vyžádá se sémantickou možností a dalšími parametry popsanými v tomto článku.

## <a name="whats-a-semantic-query"></a>Co je sémantický dotaz?

V Kognitivní hledání dotaz je parametrizovaná žádost, která určuje zpracování dotazů a tvar odpovědi. *Sémantický dotaz* přidává parametry, které vyvolávají algoritmus sémantického přeřazení, který může vyhodnotit kontext a význam odpovídajících výsledků a propagovat relevantnější shody na nejvyšší úrovni.

Následující žádost je zástupcem základního sémantického dotazu (bez odpovědí).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us",  
}
```

Stejně jako u všech dotazů v Kognitivní hledání se požadavek zaměřuje na kolekci dokumentů jediného indexu. Sémantický dotaz dále projde stejnou sekvencí analýzy, analýzy a skenování jako nesémantický dotaz. Rozdíl spočívá v tom, jak je vypočítána relevance. Jak je definováno v této verzi Preview, sémantický dotaz je ten, jehož *výsledky* se znovu zpracovávají pomocí pokročilých algoritmů, a to tak, aby se shodovaly s tím, jak se považuje za nejrelevantnější, a ne pro skóre přiřazené výchozím algoritmem řazení podle podobnosti. 

Pouze prvních 50 shod od počátečních výsledků lze sémanticky seřadit a všechny popisky zahrnutí v odpovědi. Volitelně můžete zadat **`answer`** parametr pro požadavek na extrakci potenciální odpovědi. Tento model formuluje až pět potenciálních odpovědí na dotaz, které můžete vybrat pro vykreslení na stránce hledání nahoře.

## <a name="query-using-rest-apis"></a>Dotaz pomocí rozhraní REST API

Úplnou specifikaci REST API najdete v [dokumentu hledání (REST Preview)](/rest/api/searchservice/preview-api/search-documents).

Sémantické dotazy jsou určené pro otevřené otázky, jako je například "Co je nejlepší rostlina pro pollinators" nebo "How to SRJ a". Pokud chcete, aby odpověď zahrnovala odpovědi, můžete do žádosti přidat volitelný **`answer`** parametr.

V následujícím příkladu se k vytvoření žádosti sémantického dotazu se sémantickými odpověďmi a popisky používá pohostines-Sample-index:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

### <a name="formulate-the-request"></a>Formulování žádosti

1. Nastavte "queryType" na "sémantické" a "queryLanguage" na "en-US". Oba parametry jsou povinné.

   QueryLanguage musí být konzistentní s jakýmkoli [analyzátorem jazyka](index-add-language-analyzers.md) přiřazeným k definicím polí ve schématu indexu. Pokud queryLanguage je "en-US", musí být všechny analyzátory jazyka také anglické variantou ("en. Microsoft" nebo "en. Lucene"). Žádné nezávislá analyzátory jazyka, jako je klíčové slovo nebo jednoduchá, nemají žádné konflikty s hodnotami queryLanguage.

   Pokud v žádosti o dotaz používáte také [korekci pravopisu](speller-how-to-add.md), nastavení queryLanguage se použije stejně jako pravopisné, odpovědi a titulky. Pro jednotlivé části neexistuje žádné přepsání. 

   Obsah ve vyhledávacím indexu může být tvořen v několika jazycích, takže vstup dotazu je pravděpodobně v jednom. Vyhledávací web nekontroluje kompatibilitu queryLanguage, Language Analyzer a jazyka, ve kterém se obsah skládá, a nezapomeňte jim zajistit jejich obor, aby nedocházelo k nesprávným výsledkům.

1. Volitelné, ale Doporučené, nastavte "searchFields".

   V sémantickém dotazu pořadí polí v "searchFields" odráží prioritu nebo relativní důležitost pole v sémantickém hodnocení. Budou použita pouze pole řetězců nejvyšší úrovně (samostatné nebo v kolekci). Vzhledem k tomu, že searchFields obsahuje jiné chování v jednoduchých a úplných dotazech Lucene (kde není k dispozici žádný předpokládaný způsob priority), neobsahovaná pole a podpole nebudou mít za následek chybu, ale také se nepoužijí v sémantickém hodnocení.

   Při zadávání searchFields postupujte podle těchto pokynů:

   + Stručná pole, jako je název hotelu nebo název, by měla předcházet podrobným polím jako Description.

   + Pokud má váš index pole adresy URL, které je text (lidské čitelnost, jako je například `www.domain.com/name-of-the-document-and-other-details` a ne počítač zaměřený na například `www.domain.com/?id=23463&param=eis` ), umístěte ho v seznamu za druhý (Pokud není k dispozici pole stručného názvu).

   + Pokud je zadáno pouze jedno pole, bude považováno za popisné pole pro sémantické hodnocení dokumentů.  

   + Pokud nejsou zadaná žádná pole, budou se všechna hledaná pole považovat za sémantické hodnocení dokumentů. To se ale nedoporučuje, protože nemusí vracet optimální výsledky z indexu vyhledávání.

1. Odeberte klauzule orderBy, pokud existují v existující žádosti. Sémantické skóre se používá k seřazení výsledků a pokud zahrnete explicitní logiku řazení, vrátí se chyba HTTP 400.

1. Volitelně můžete přidat "odpovědi" nastavené na "extrahovatelné" a zadat počet odpovědí, pokud potřebujete více než 1.

1. Volitelně můžete přizpůsobit styl zvýraznění aplikovaný na titulky. Popisky použijí formátování zvýraznění u klíčových pasáží v dokumentu, které shrnují odpověď. Výchozí formát je `<em>`. Pokud chcete určit typ formátování (například žluté pozadí), můžete nastavit highlightPreTag a highlightPostTag.

1. Zadejte všechny další parametry, které chcete v žádosti. Parametry, jako je třeba [Kontrola pravopisu](speller-how-to-add.md), [Výběr](search-query-odata-select.md)a počet, zlepšují kvalitu žádosti a čitelnosti odpovědi.

### <a name="review-the-response"></a>Zkontrolovat odpověď

Reakce na výše uvedený dotaz vrátí následující shodu jako horního výběru. Titulky se vrátí automaticky, s prostým textem a zvýrazněnými verzemi. Další informace o sémantických odpovědích najdete v tématu [sémantické hodnocení a odpovědi](semantic-how-to-query-response.md).

```json
"@odata.count": 29,
"value": [
    {
        "@search.score": 1.8920634,
        "@search.rerankerScore": 1.1091284966096282,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Budget. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Budget. New Luxury Hotel. Be the first to stay.<strong> Bay views</strong> from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelId": "18",
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Budget"
    },
```

### <a name="parameters-used-in-a-semantic-query"></a>Parametry používané v sémantickém dotazu

Následující tabulka shrnuje parametry dotazu používané v sémantickém dotazu, abyste je mohli zobrazit komplexní. Seznam všech parametrů najdete v tématu [hledání dokumentů (REST Preview)](/rest/api/searchservice/preview-api/search-documents) .

| Parametr | Typ | Popis |
|-----------|-------|-------------|
| queryType | Řetězec | Mezi platné hodnoty patří jednoduchá, plná a sémantická. Pro sémantické dotazy je požadována hodnota sémantika. |
| queryLanguage | Řetězec | Vyžaduje se pro sémantické dotazy. V současné době je implementována pouze "en-US". |
| searchFields | Řetězec | Seznam prohledávatelných polí oddělených čárkami. Volitelné, ale doporučené. Určuje pole, přes která probíhá sémantické hodnocení. </br></br>Na rozdíl od jednoduchých a úplných typů dotazů je pořadí, ve kterém jsou pole uvedena, určena přednostně.|
| zodpovídá |Řetězec | Volitelné pole, které určuje, zda jsou ve výsledku zahrnuty sémantické odpovědi. V současné době je implementována pouze "extrakce". Odpovědi lze nakonfigurovat tak, aby vracely maximálně pět. Příklad "extrakce"|count3 "" zobrazuje počet tří odpovědí. Výchozí hodnota je 1.|

## <a name="query-with-search-explorer"></a>Dotaz s využitím Průzkumníka služby Hledání

Následující dotaz cílí na integrovaný ukázkový index hotelů, pomocí rozhraní API verze 2020-06-30-Preview a běží v Průzkumníkovi vyhledávání. `$select`Klauzule omezí výsledky jenom na několik polí, což usnadňuje kontrolu v podrobném formátu JSON v Průzkumníkovi vyhledávání.

### <a name="with-querytypesemantic"></a>With queryType = sémantická

```json
search=I want a nice hotel on the water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
```

Následuje několik prvních výsledků.

```json
{
    "@search.score": 0.38330218,
    "@search.rerankerScore": 0.9754053303040564,
    "HotelId": "18",
    "HotelName": "Oceanside Resort",
    "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
    "Tags": [
        "view",
        "laundry service",
        "air conditioning"
    ]
},
{
    "@search.score": 1.8920634,
    "@search.rerankerScore": 0.8829904259182513,
    "HotelId": "36",
    "HotelName": "Pelham Hotel",
    "Description": "Stunning Downtown Hotel with indoor Pool. Ideally located close to theatres, museums and the convention center. Indoor Pool and Sauna and fitness centre. Popular Bar & Restaurant",
    "Tags": [
        "view",
        "pool",
        "24-hour front desk service"
    ]
},
{
    "@search.score": 0.95706713,
    "@search.rerankerScore": 0.8538530203513801,
    "HotelId": "22",
    "HotelName": "Stone Lion Inn",
    "Description": "Full breakfast buffet for 2 for only $1.  Excited to show off our room upgrades, faster high speed WiFi, updated corridors & meeting space. Come relax and enjoy your stay.",
    "Tags": [
        "laundry service",
        "air conditioning",
        "restaurant"
    ]
},
```

### <a name="with-querytype-default"></a>S hodnotou queryType (výchozí)

Pro porovnání spusťte stejný dotaz, jak je uvedeno výše, a odeberte `&queryType=semantic&queryLanguage=english&searchFields=Description,Tags` . Všimněte si, že `"@search.rerankerScore"` v těchto výsledcích nejsou žádné, a v horních třech pozicích se zobrazí různé hotely.

```json
{
    "@search.score": 8.633856,
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Tags": [
        "air conditioning",
        "bar",
        "continental breakfast"
    ]
},
{
    "@search.score": 6.407289,
    "HotelId": "40",
    "HotelName": "Trails End Motel",
    "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
    "Tags": [
        "continental breakfast",
        "view",
        "view"
    ]
},
{
    "@search.score": 5.843788,
    "HotelId": "14",
    "HotelName": "Twin Vertex Hotel",
    "Description": "New experience in the Making.  Be the first to experience the luxury of the Twin Vertex. Reserve one of our newly-renovated guest rooms today.",
    "Tags": [
        "bar",
        "restaurant",
        "air conditioning"
    ]
},
```

## <a name="next-steps"></a>Další kroky

Pomocí počáteční sady výsledků odvoláte, aby se sémantické hodnocení a odpovědi vystavily. Všechny logiky, které zvyšují kvalitu počátečních výsledků, se přenesou na sémantické vyhledávání. V dalším kroku si Projděte funkce, které přispívají k počátečním výsledkům, včetně analyzátorů, které mají vliv na tokeny řetězců, vyhodnocovací profily, které mohou ladit výsledky a výchozí algoritmus pro relevanci.

+ [Analyzátory pro zpracování textu](search-analyzers.md)
+ [Podobnost a bodování v Kognitivní hledání](index-similarity-and-scoring.md)
+ [Přidání profilů vyhodnocování](index-add-scoring-profiles.md)
+ [Přehled sémantického hledání](semantic-search-overview.md)
+ [Přidat kontrolu pravopisu pro dotaz na výrazy](speller-how-to-add.md)
