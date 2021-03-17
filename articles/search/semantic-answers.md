---
title: Vrátí sémantickou odpověď.
titleSuffix: Azure Cognitive Search
description: Popisuje složení sémantické odpovědi a postup získání odpovědí ze sady výsledků dotazu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: b99cbf91d7fc1c5d90753dfa1461a58eda055180
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418891"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Vrácení sémantické odpovědi v Azure Kognitivní hledání

> [!IMPORTANT]
> Funkce sémantického vyhledávání jsou ve verzi Public Preview dostupné jenom v rámci verze Preview REST API. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)a nemají zaručenou stejnou implementaci při obecné dostupnosti. Další informace najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

Když vytváříte [sémantický dotaz](semantic-how-to-query-request.md), můžete volitelně extrahovat obsah z horních dokumentů, které dotaz přímo odpovídá. Odpověď může obsahovat jednu nebo více odpovědí, které pak můžete vykreslit na stránce pro hledání, a tím zlepšit uživatelské prostředí aplikace.

V tomto článku se dozvíte, jak vyžádat sémantickou odpověď, rozbalit odpověď a zjistit, jaké charakteristiky obsahu nejlépe přispívají k vytváření vysoce kvalitních odpovědí.

## <a name="prerequisites"></a>Požadavky

Všechny požadavky, které platí pro [sémantické dotazy](semantic-how-to-query-request.md) , se vztahují také na odpovědi, včetně úrovně služeb a oblasti.

+ Dotazy jsou formulovány pomocí parametrů sémantického dotazu a obsahují parametr odpovědi. Požadované parametry jsou popsány v tomto článku.

+ Řetězce dotazů musí být formulovány v jazyce, který má charakteristiky otázky (co, kde, kdy a jak).

+ Hledání dokumentů musí obsahovat text, který má vlastnosti odpovědi, a tento text musí existovat v jednom z polí uvedených v "searchFields".

## <a name="what-is-a-semantic-answer"></a>Co je sémantická odpověď?

Sémantická odpověď je artefaktem [sémantického dotazu](semantic-how-to-query-request.md). Skládá se z jednoho nebo více doslovnéch pasáží z dokumentu pro hledání, které se formulují jako odpověď na dotaz, který vypadá jako otázka. Odpověď, která se má vrátit, musí existovat ve vyhledávacím dokumentu, který má jazykové charakteristiky odpovědi, a samotný dotaz musí být považován za otázku.

Kognitivní hledání pro formulaci odpovědí používá model porozumění pro čtení počítačů. Model vytvoří sadu možných odpovědí z dostupných dokumentů a když dosáhne vysoké úrovně spolehlivosti, navrhne odpověď.

Odpovědi se vrátí jako nezávislá objekt nejvyšší úrovně v datové části odpovědi na dotaz, který můžete zvolit pro vykreslování na stránkách vyhledávání společně s výsledky hledání na straně. Strukturální, jedná se o prvek pole odpovědi, která obsahuje text, klíč dokumentu a hodnocení spolehlivosti.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>Požadavek na sémantické odpovědi v dotazu

Pokud chcete vrátit sémantickou odpověď, musí mít dotaz typ sémantického dotazu, jazyk, vyhledávací pole a parametr odpovědi. Zadáním parametru reanswers nezaručujete, že dostanete odpověď, ale požadavek musí zahrnovat tento parametr, pokud se má vyvolávat zpracování odpovědí.

Parametr "searchFields" je velmi důležitý pro vrácení vysoce kvalitní odpovědi, a to jak z hlediska obsahu, tak z hlediska pořadí. 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ Řetězec dotazu nesmí mít hodnotu null a měl by být formulován jako otázka. V této verzi Preview musí být "queryType" a "queryLanguage" nastavené přesně tak, jak je znázorněno v příkladu.

+ Parametr "searchFields" Určuje, která pole poskytují tokeny modelu extrakce. Nezapomeňte nastavit tento parametr. Musíte mít alespoň jedno pole řetězce, ale zahrňte jakékoli pole řetězce, které považujete za užitečné při poskytování odpovědi. Do modelu se předává jenom přibližně 8 000 tokenů na dokument. Zahajte seznam polí s stručnými poli a pak průběh do polí s textem v textu. Podrobné pokyny k nastavení tohoto pole naleznete v tématu [set searchFields](semantic-how-to-query-request.md#searchfields).

+ Pro "odpovědi" je základní konstrukce parametrů `"answers": "extractive"` , kde je výchozí počet vrácených odpovědí jedna. Počet odpovědí můžete zvýšit přidáním počtu, maximálně po pěti.  Bez ohledu na to, jestli potřebujete víc než jednu odpověď, závisí na uživatelském prostředí vaší aplikace a na tom, jak se mají vykreslovat výsledky.

## <a name="deconstruct-an-answer-from-the-response"></a>Dekonstruovat odpověď z odpovědi

Odpovědi jsou k dispozici v poli @search.answers , které se zobrazí jako první v odpovědi. Pokud odpověď je neurčitá, zobrazí se odpověď jako `"@search.answers": []` . Při návrhu stránky výsledků hledání, která zahrnuje odpovědi, nezapomeňte zpracovat případy, kdy se odpovědi nenašly.

V rámci @search.answers je "klíč" klíčem dokumentu nebo ID shody. Vzhledem k klíči dokumentu můžete použít rozhraní API pro [vyhledávání](/rest/api/searchservice/lookup-document) k načtení všech částí dokumentu hledání, které chcete zahrnout na stránku vyhledávání nebo na stránku podrobností.

Jak "text" a "zvýraznění" poskytují stejný obsah, jak v prostém textu, tak i ve světlech. Ve výchozím nastavení se zvýrazní jako styl `<em>` , který můžete přepsat pomocí stávajících parametrů highlightPreTag a highlightPostTag. Jak je uvedeno jinde, je látka odpovědi v dokumentu pro hledání doslovné. Model extrakce vyhledá charakteristiky odpovědi, aby našel příslušný obsah, ale nevytváří v odpovědi nový jazyk.

Skóre je hodnocení spolehlivosti, které odráží sílu odpovědi. Pokud odpověď obsahuje více odpovědí, použije se k určení objednávky toto skóre. Hlavní odpovědi a horní popisky lze odvodit z různých vyhledávacích dokumentů, kde nejvyšší odpověď vznikla z jednoho dokumentu a z titulku nejvyšší úrovně, ale obecně uvidíte stejné dokumenty v horních pozicích v každém poli.

V odpovědích následuje pole "hodnota", které vždy obsahuje skóre, popisky a všechna pole, která lze ve výchozím nastavení načíst. Pokud jste zadali parametr SELECT, pole hodnota je omezeno na pole, která jste zadali. Další informace o položkách v odpovědi najdete v tématu [Vytvoření sémantického dotazu](semantic-how-to-query-request.md).

V případě dotazu "How to Clouds Form" se v odpovědi vrátí následující odpověď:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>Tipy pro vytváření vysoce kvalitních odpovědí

Pro dosažení nejlepších výsledků vraťte sémantické odpovědi na dokument corpus s následujícími charakteristikami:

+ "searchFields" by měl obsahovat jedno nebo více polí, která poskytují dostatečný text, ve kterém je pravděpodobně nalezena odpověď.

+ Sémantická extrakce a sumarizace mají omezení, kolik obsahu je možné včas analyzovat. Souhrnně se analyzují jenom první tokeny 20 000. Vše nad rámec, které je ignorováno. V praktických případech, pokud máte velké dokumenty, které běží na stovkách stránek, byste se měli pokusit rozdělit obsah do spravovatelných částí jako první.

+ řetězce dotazu nesmí mít hodnotu null (Search = `*` ) a řetězec by měl mít charakteristiky otázky, a to na rozdíl od vyhledávání klíčového slova (sekvenční seznam libovolných podmínek nebo frází). Pokud se řetězec dotazu nejeví jako odpověď, je zpracování odpovědi přeskočeno, i když požadavek určuje jako parametr dotazu "odpovědi".

## <a name="next-steps"></a>Další kroky

+ [Přehled sémantického hledání](semantic-search-overview.md)
+ [Algoritmus sémantického hodnocení](semantic-ranking.md)
+ [Algoritmus podobnosti](index-ranking-similarity.md)
+ [Vytvořit sémantický dotaz](semantic-how-to-query-request.md)