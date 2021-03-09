---
title: Vytvořit sémantický dotaz
titleSuffix: Azure Cognitive Search
description: Nastavte sémantický typ dotazu pro připojení modelů hloubkového učení k zpracování dotazů, odvození záměru a kontextu v rámci pořadí hledání a relevance.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 7f7a09b9e20b461a8a1e448bf4a7b0747a35fbb1
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487136"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Vytvoření sémantického dotazu v Kognitivní hledání

> [!IMPORTANT]
> Sémantický typ dotazu je ve verzi Public Preview, který je k dispozici ve verzi Preview REST API a Azure Portal. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Během úvodního spuštění Preview se neúčtují žádné poplatky za sémantické vyhledávání. Další informace najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

V tomto článku se dozvíte, jak formulovat požadavek hledání, který používá sémantické hodnocení, a vytváří sémantické titulky a odpovědi.

Sémantické dotazy obvykle fungují nejlépe na indexech vyhledávání, které jsou vybudovány s velkým obsahem textu, jako jsou soubory PDF nebo dokumenty s velkými bloky textu.

## <a name="prerequisites"></a>Požadavky

+ Vyhledávací služba na úrovni Standard (S1, S2, S3), která se nachází v jedné z těchto oblastí: Střed USA – sever, Západní USA, Západní USA 2, Východní USA 2, Severní Evropa, Západní Evropa. Pokud máte v jedné z těchto oblastí existující službu S1 nebo větší, můžete požádat o přístup bez nutnosti vytvářet novou službu.

+ Přístup ke službě sémantického vyhledávání ve verzi Preview: [registrace](https://aka.ms/SemanticSearchPreviewSignup)

+ Existující index vyhledávání, který obsahuje anglický obsah

+ Vyhledávací klient pro odesílání dotazů

  Vyhledávací klient musí v žádosti o dotaz podporovat rozhraní REST API pro náhled. Můžete použít [post](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)nebo kód, který jste změnili, aby se v rozhraní API verze Preview zavolala volání REST. Můžete také použít [Průzkumníka vyhledávání](search-explorer.md) v Azure Portal k odeslání sémantického dotazu.

+ [Dokument hledání](/rest/api/searchservice/preview-api/search-documents) vyžádá se sémantickou možností a dalšími parametry popsanými v tomto článku.

## <a name="whats-a-semantic-query"></a>Co je sémantický dotaz?

V Kognitivní hledání dotaz je parametrizovaná žádost, která určuje zpracování dotazů a tvar odpovědi. *Sémantický dotaz* přidává parametry, které vyvolávají model pro sémantické přeřazení, který může vyhodnotit kontext a význam odpovídajících výsledků, zvýšit relevantnější shody na začátek a vracet sémantické odpovědi a popisky.

Následující žádost je zástupcem základního sémantického dotazu (bez odpovědí).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Stejně jako u všech dotazů v Kognitivní hledání se požadavek zaměřuje na kolekci dokumentů jediného indexu. Sémantický dotaz dále projde stejnou sekvencí analýzy, analýzy a skenování jako nesémantický dotaz. Rozdíl spočívá v tom, jak je vypočítána relevance. Jak je definováno v této verzi Preview, sémantický dotaz je ten, jehož *výsledky* se znovu zpracovávají pomocí pokročilých algoritmů, a to tak, aby se shodovaly s tím, jak se považuje za nejrelevantnější, a ne pro skóre přiřazené výchozím algoritmem řazení podle podobnosti. 

Pouze prvních 50 shod od počátečních výsledků lze sémanticky seřadit a všechny popisky zahrnutí v odpovědi. Volitelně můžete zadat **`answer`** parametr pro požadavek na extrakci potenciální odpovědi. Tento model formuluje až pět potenciálních odpovědí na dotaz, které můžete vybrat pro vykreslení na stránce hledání nahoře.

## <a name="query-using-rest-apis"></a>Dotaz pomocí rozhraní REST API

Úplnou specifikaci REST API najdete v [dokumentu hledání (REST Preview)](/rest/api/searchservice/preview-api/search-documents).

Sémantické dotazy poskytují popisky a zvýrazňování automaticky. Pokud chcete, aby odpověď zahrnovala odpovědi, můžete do žádosti přidat volitelný **`answer`** parametr. Tento parametr plus konstrukce samotného řetězce dotazu vytvoří odpověď v odpovědi.

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

Tato část popisuje parametry dotazů, které jsou nezbytné pro sémantické vyhledávání.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Krok 1: nastavení queryType a queryLanguage

Do Rest přidejte následující parametry. Oba parametry jsou povinné.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

QueryLanguage musí být konzistentní s jakýmkoli [analyzátorem jazyka](index-add-language-analyzers.md) přiřazeným k definicím polí ve schématu indexu. Pokud queryLanguage je "en-US", musí být všechny analyzátory jazyka také anglické variantou ("en. Microsoft" nebo "en. Lucene"). Žádné nezávislá analyzátory jazyka, jako je klíčové slovo nebo jednoduchá, nemají žádné konflikty s hodnotami queryLanguage.

Pokud v žádosti o dotaz používáte také [korekci pravopisu](speller-how-to-add.md), nastavení queryLanguage se použije stejně jako pravopisné, odpovědi a titulky. Pro jednotlivé části neexistuje žádné přepsání. 

Obsah ve vyhledávacím indexu může být tvořen v několika jazycích, takže vstup dotazu je pravděpodobně v jednom. Vyhledávací web nekontroluje kompatibilitu queryLanguage, Language Analyzer a jazyka, ve kterém se obsah skládá, a nezapomeňte jim zajistit jejich obor, aby nedocházelo k nesprávným výsledkům.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Krok 2: nastavení searchFields

Tento parametr je nepovinný v tom, že není k dispozici žádná chyba, pokud ho necháte, ale pro titulky a odpovědi se důrazně doporučuje zobrazit uspořádaný seznam polí.

Parametr searchFields slouží k identifikaci pasáží pro vyhodnocení "sémantické podobnosti" dotazem. V rámci verze Preview nedoporučujeme ponechání searchFields prázdné, protože model vyžaduje doporučení, která pole jsou pro zpracování nejdůležitější.

Pořadí searchFields je kritické. Pokud už používáte searchFields v existujících jednoduchých nebo úplných dotazech na Lucene, nezapomeňte při přechodu na sémantický typ dotazu přejít na tento parametr znovu.

Postupujte podle těchto pokynů, abyste zajistili optimální výsledky při zadání dvou nebo více searchFields:

+ V kolekcích zahrňte pouze pole řetězců a pole řetězců nejvyšší úrovně. Pokud se rozhodnete zahrnout neřetězcová pole nebo pole nižší úrovně v kolekci, nebude k dispozici žádná chyba, ale tato pole se nepoužijí v sémantickém hodnocení.

+ První pole by mělo být vždy stručné (například název nebo název), a to v ideálním 25 slovem.

+ Pokud má index pole Adresa URL, která je text (lidské čtení, jako je například `www.domain.com/name-of-the-document-and-other-details` a ne počítač zaměřený na například `www.domain.com/?id=23463&param=eis` ), umístěte ho do tohoto seznamu (nebo nejdřív, pokud neexistuje pole stručného názvu).

+ Použijte tato pole podle popisných polí, kde můžete najít odpověď na sémantické dotazy, jako je například hlavní obsah dokumentu.

Pokud je zadáno pouze jedno pole, použijte popisné pole, kde lze nalézt odpověď na sémantické dotazy, jako je například hlavní obsah dokumentu. Vyberte pole, které poskytuje dostatečný obsah.

#### <a name="step-3-remove-orderby-clauses"></a>Krok 3: odebrání klauzulí orderBy

Odeberte jakékoli klauzule orderBy, pokud existují v existující žádosti. Sémantické skóre se používá k seřazení výsledků a pokud zahrnete explicitní logiku řazení, vrátí se chyba HTTP 400.

#### <a name="step-4-add-answers"></a>Krok 4: Přidání odpovědí

Volitelně můžete přidat odpovědi, pokud chcete zahrnout další zpracování, které poskytuje odpověď. Odpovědi (a titulky) jsou formulované z pasáží nalezených v polích uvedených v searchFields. Nezapomeňte do searchFields zahrnout pole s bohatým obsahem, abyste získali nejlepší odpovědi a popisy v odpovědi.

K dispozici jsou explicitní a implicitní podmínky, které vytváří odpovědi. 

+ Mezi explicitní podmínky patří přidání "odpovědi = extrakce". Kromě toho můžete zadat počet odpovědí vrácených v celkové odpovědi a přidat "počet" následovaný číslem: `"answers=extractive|count=3"` .  Výchozí hodnota je jedna. Maximum je pět.

+ Mezi implicitní podmínky patří konstrukce řetězce dotazu, který sám o sobě zavolá odpověď. Dotaz tvořený "jaký Hotel má zelenou místnost", je pravděpodobnější, že se jedná o "zodpovězení", než dotaz složený z příkazu, jako je hotel s ozdobným vnitřním obsahem. Jak můžete očekávat, dotaz nemůže být nezadaný nebo mít hodnotu null.

Důležité je Ukázat, že pokud dotaz nevypadá jako otázka, zpracování odpovědi se přeskočí, i když je nastaven parametr odpovědi.

#### <a name="step-5-add-other-parameters"></a>Krok 5: Přidání dalších parametrů

Nastavte všechny další parametry, které chcete v žádosti. Parametry, jako je třeba [Kontrola pravopisu](speller-how-to-add.md), [Výběr](search-query-odata-select.md)a počet, zlepšují kvalitu žádosti a čitelnosti odpovědi.

Volitelně můžete přizpůsobit styl zvýraznění aplikovaný na titulky. Popisky použijí formátování zvýraznění u klíčových pasáží v dokumentu, které shrnují odpověď. Výchozí formát je `<em>`. Pokud chcete určit typ formátování (například žluté pozadí), můžete nastavit highlightPreTag a highlightPostTag.

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
| zodpovídá |Řetězec | Volitelné pole, které určuje, zda jsou ve výsledku zahrnuty sémantické odpovědi. V současné době je implementována pouze "extrakce". Odpovědi lze nakonfigurovat tak, aby vracely maximálně pět. Výchozí hodnota je jedna. V tomto příkladu se zobrazuje počet tří odpovědí: "extrahovatelné \| count3". |

## <a name="query-with-search-explorer"></a>Dotaz s využitím Průzkumníka služby Hledání

Následující dotaz cílí na integrovaný ukázkový index hotelů, pomocí rozhraní API verze 2020-06-30-Preview a běží v Průzkumníkovi vyhledávání. `$select`Klauzule omezí výsledky jenom na několik polí, což usnadňuje kontrolu v podrobném formátu JSON v Průzkumníkovi vyhledávání.

### <a name="with-querytypesemantic"></a>With queryType = sémantická

```json
search=nice hotel on water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
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
