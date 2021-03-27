---
title: Přidat kontrolu pravopisu
titleSuffix: Azure Cognitive Search
description: Připojte kontrolu pravopisu k kanálu dotazů a opravte překlepy na dotazové výrazy před spuštěním dotazu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: references_regions
ms.openlocfilehash: 52ac3ee4ea2f71e285d21c7b6d082e84fa090da1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625904"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Přidat kontrolu pravopisu do dotazů v Kognitivní hledání

> [!IMPORTANT]
> Oprava pravopisu je ve verzi Public Preview, která je dostupná jenom v rámci verze Preview REST API. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Při úvodním spuštění Preview se neúčtují žádné poplatky za kontrolu pravopisu. Další informace najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

Odvolání můžete vylepšit tak, že před dosažením vyhledávacího modulu opravíte pravopis jednotlivých výrazů vyhledávacího dotazu. Parametr pro **kontrolu pravopisu** se podporuje pro všechny typy dotazů: [Jednoduchá](query-simple-syntax.md), [plná](query-lucene-syntax.md)a nová [sémantická](semantic-how-to-query-request.md) možnost, která je aktuálně ve verzi Public Preview.

## <a name="prerequisites"></a>Požadavky

+ Existující index vyhledávání, který obsahuje anglický obsah. V současné době Oprava pravopisu nefunguje s [synonymy](search-synonyms.md). Nepoužívejte ji v indexech, které určují mapu synonym v jakékoli definici pole.

+ Vyhledávací klient pro odesílání dotazů

  Vyhledávací klient musí v žádosti o dotaz podporovat rozhraní REST API pro náhled. Můžete použít [post](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)nebo kód, který jste změnili, aby se v rozhraní API verze Preview zavolala volání REST.

+ [Požadavek na dotaz](/rest/api/searchservice/preview-api/search-documents) , který používá opravu pravopisu, má "API-Version = 2020-06 -30-Preview", "pravopis" nebo "queryLanguage = en-US".

  QueryLanguage se vyžaduje pro kontrolu pravopisu a aktuálně "en-US" je jediná platná hodnota.

> [!Note]
> Parametr pro kontrolu pravopisu je k dispozici na všech úrovních ve stejných oblastech, které poskytují sémantické vyhledávání. Pro přístup k této funkci verze Preview se nemusíte registrovat. Další informace najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

## <a name="spell-correction-with-simple-search"></a>Snadné hledání oprav pravopisu

V následujícím příkladu je použit vestavěný index hotelů – vzor k předvedení opravy pravopisu v jednoduchém textovém dotazu bezplatného formuláře. Bez opravy pravopisu vrátí dotaz nula výsledků. V případě opravy vrátí dotaz jeden výsledek pro Johnsonem orientované na rodinu.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Korekce pravopisu s využitím úplného Lucene

K opravě pravopisu dochází u jednotlivých dotazovacích výrazů, které jsou schopné analyzovat, což znamená, že můžete použít parametr pro kontrolu pravopisu u některých dotazů Lucene, ale ne jiných.

+ Nekompatibilní formuláře dotazů, které obcházejí analýzu textu, zahrnují: zástupný znak, Regex, fuzzy
+ Kompatibilní formuláře dotazů zahrnují: hledání s poli, blízkost, zvyšování skóre termínu

V tomto příkladu se v poli kategorie používá hledání pomocí pole, s úplnou syntaxí Lucene a s chybně napsaným termínem dotazu. Pokud zahrnete kontrolu pravopisu, překlepy v části "Suiite" se opraví a dotaz bude úspěšný.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Korekce pravopisu s sémantickým vyhledáváním

Tento dotaz s překlepy v každém termínu, s výjimkou jednoho, prochází pravopisné opravy, které vracejí relevantní výsledky. Další informace najdete v tématu [Vytvoření sémantického dotazu](semantic-how-to-query-request.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>Jazykové požadavky

Parametr queryLanguage vyžadovaný pro kontrolu pravopisu musí být konzistentní s případnými [analyzátory jazyka](index-add-language-analyzers.md) přiřazenými k definicím polí ve schématu indexu. 

+ queryLanguage určuje, které lexikony se používají pro kontrolu pravopisu, a používá se také jako vstup do [algoritmu sémantického hodnocení](semantic-answers.md) , pokud používáte "queryType = sémantické".

+ Analyzátory jazyka se používají během indexování a provádění dotazů k nalezení vyhovujících dokumentů v indexu vyhledávání. Příkladem definice pole, která používá analyzátor jazyka, je `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"` .

Pro dosažení nejlepších výsledků při použití nástroje pro kontrolu pravopisu, pokud je queryLanguage "en-US", musí být všechny analyzátory jazyka také anglické variantou ("en. Microsoft" nebo "en. Lucene").

> [!NOTE]
> Analyzátory jazyka nezávislá (například klíčové slovo, Simple, Standard, stop, prázdné znaky nebo `standardasciifolding.lucene` ) nejsou v konfliktu s nastavením queryLanguage.

QueryLanguage, který nastavíte, se v požadavku na dotaz aplikuje rovnoměrně na kontrolu pravopisu, odpovědi a titulky. Pro jednotlivé části neexistuje žádné přepsání.

Obsah ve vyhledávacím indexu může být tvořen v několika jazycích, takže vstup dotazu je pravděpodobně v jednom. Vyhledávací web nekontroluje kompatibilitu queryLanguage, Language Analyzer a jazyka, ve kterém se obsah skládá, a nezapomeňte jim zajistit jejich obor, aby nedocházelo k nesprávným výsledkům.

## <a name="next-steps"></a>Další kroky

+ [Vytvořit sémantický dotaz](semantic-how-to-query-request.md)
+ [Vytvoření základního dotazu](search-query-create.md)
+ [Použít úplnou syntaxi dotazů Lucene](query-Lucene-syntax.md)
+ [Použití jednoduché syntaxe dotazů](query-simple-syntax.md)
+ [Přehled sémantického hledání](semantic-search-overview.md)