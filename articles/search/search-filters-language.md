---
title: Filtrování podle jazyka v indexu vyhledávání
titleSuffix: Azure Cognitive Search
description: Kritéria filtru pro podporu vícejazyčné vyhledávání, obor provádění dotazů na pole specifická pro jazyk.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5dbf32610e54df4ff009d4cb0a0b080babb4ec73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112058"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Jak filtrovat podle jazyka v Azure Cognitive Search 

Klíčovým požadavkem v aplikaci pro vícejazyčné vyhledávání je možnost vyhledávat a načítat výsledky ve vlastním jazyce uživatele. V Azure Cognitive Search jedním ze způsobů, jak splnit jazykové požadavky vícejazyčné aplikace je vytvořit řadu polí vyhrazených pro ukládání řetězců v určitém jazyce a pak omezit fulltextové vyhledávání pouze na tato pole v době dotazu.

Parametry dotazu v požadavku se používají k oboru operace hledání a potom ořízněte výsledky všech polí, která neposkytují obsah kompatibilní s prostředím vyhledávání, které chcete doručit.

| Parametry | Účel |
|-----------|--------------|
| **hledatPole** | Omezí fulltextové vyhledávání na seznam pojmenovaných polí. |
| **$select** | Ořízne odpověď tak, aby zahrnovala pouze zadaná pole. Ve výchozím nastavení jsou vrácena všechna načítatelná pole. Parametr **$select** umožňuje zvolit, které z nich se mají vrátit. |

Úspěch této techniky závisí na integritě obsahu pole. Azure Cognitive Search nepřekládá řetězce ani neprovádí rozpoznávání jazyka. Je na vás, abyste se ujistili, že pole obsahují řetězce, které očekáváte.

## <a name="define-fields-for-content-in-different-languages"></a>Definování polí pro obsah v různých jazycích

V Azure Cognitive Search dotazy cílí na jeden index. Vývojáři, kteří chtějí poskytovat řetězce specifické pro jazyk v jednom prostředí vyhledávání obvykle definovat vyhrazená pole pro uložení hodnot: jedno pole pro anglické řetězce, jeden pro francouzštinu a tak dále. 

V našich ukázkách, včetně [níže uvedeného vzorku nemovitostí,](search-get-started-portal.md) jste možná viděli definice polí podobné následujícímu snímku obrazovky. Všimněte si, jak tento příklad zobrazuje přiřazení analyzátoru jazyka pro pole v tomto indexu. Pole, která obsahují řetězce, fungují lépe při fulltextovém vyhledávání při spárování s analyzátorem navrženým tak, aby zpracovával jazyková pravidla cílového jazyka.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Příklady kódu zobrazující definice polí pomocí analyzátorů jazyků naleznete [v tématech Definování indexu (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) a [Definování indexu (REST).](search-create-index-rest-api.md)

## <a name="build-and-load-an-index"></a>Sestavení a načtení indexu

Mezilehlý (a možná zřejmý) krok je, že je třeba [vytvořit a naplnit index](https://docs.microsoft.com/azure/search/search-create-index-dotnet) před formulovánídotazu. Zmiňujeme tento krok zde pro úplnost. Jedním ze způsobů, jak zjistit, zda je index k dispozici, je kontrola seznamu indexů na [portálu](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Omezení výsledků dotazu a oříznutí

Parametry v dotazu se používají k omezení hledání na určitá pole a potom ořízněte výsledky všech polí, která nejsou pro váš scénář užitečná. Vzhledem k tomu, za cíl omezení vyhledávání na pole obsahující francouzské řetězce, by použít **searchFields** k cílení dotazu na pole obsahující řetězce v daném jazyce. 

Ve výchozím nastavení hledání vrátí všechna pole, která jsou označena jako retrievable. V důsledku toho můžete vyloučit pole, která neodpovídají prostředí vyhledávání specifickému pro daný jazyk, které chcete poskytnout. Konkrétně pokud jste omezili vyhledávání na pole s francouzskými řetězci, pravděpodobně budete chtít vyloučit pole s anglickými řetězci z výsledků. Pomocí **$select** parametr dotazu umožňuje kontrolu nad tím, která pole jsou vrácena volající aplikaci.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Přestože neexistuje žádný $filter argument na dotaz, tento případ použití je silně spojen s koncepty filtru, takže jsme prezentovat jako scénář filtrování.

## <a name="see-also"></a>Viz také

+ [Filtry v Azure Cognitive Search](search-filters.md)
+ [Analyzátory jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Rozhraní API pro vyhledávání v dokumentech](https://docs.microsoft.com/rest/api/searchservice/search-documents)

