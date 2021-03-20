---
title: Filtrovat podle jazyka ve vyhledávacím indexu
titleSuffix: Azure Cognitive Search
description: Kritéria filtru pro podporu vyhledávání ve více jazycích, určení rozsahu provádění dotazů na pole pro konkrétní jazyk
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 49a12203c833fc817b1898e6179d7f812d0a994e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89002517"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Postup filtrování podle jazyka v Azure Kognitivní hledání 

Klíčovým požadavkem v aplikaci pro vyhledávání ve více jazycích je schopnost vyhledávat a načítat výsledky v jazyce uživatele. V Azure Kognitivní hledání jeden ze způsobů, jak splnit jazykové požadavky vícejazyčné aplikace, je vytvoření řady polí vyhrazených pro ukládání řetězců v konkrétním jazyce a omezení fulltextového vyhledávání jenom na tato pole v době dotazu.

Parametry dotazu v žádosti se používají k určení oboru operace vyhledávání a pak se oříznou výsledky všech polí, která neposkytují obsah kompatibilní s vyhledávacím prostředím, které chcete doručit.

| Parametry | Účel |
|-----------|--------------|
| **searchFields** | Omezí úplné hledání textu na seznam pojmenovaných polí. |
| **$select** | Ořízne odpověď tak, aby zahrnovala pouze pole, která zadáte. Ve výchozím nastavení jsou vrácena všechna pole, která lze načíst. Parametr **$Select** vám umožní zvolit, která z nich se má vrátit. |

Úspěch této techniky se přestavuje na základě integrity obsahu polí. Azure Kognitivní hledání nepřevádí řetězce ani neprovádí detekci jazyka. Ujistěte se, že pole obsahují řetězce, které očekáváte.

## <a name="define-fields-for-content-in-different-languages"></a>Definovat pole pro obsah v různých jazycích

V Azure Kognitivní hledání dotazy cílí na jeden index. Vývojáři, kteří chtějí poskytnout řetězce pro konkrétní jazyk v rámci jednoho vyhledávacího prostředí, obvykle definují vyhrazená pole pro ukládání hodnot: jedno pole pro anglické řetězce, jeden pro francouzštinu a tak dále. 

Následující příklad pochází z [ukázky reálného majetku](search-get-started-portal.md) , který obsahuje několik polí řetězců obsahujících obsah v různých jazycích. Všimněte si, že přiřazení analyzátoru jazyka pro pole v tomto indexu. Pole, která obsahují řetězce, fungují lépe při fulltextovém vyhledávání, když se spáruje s analýzou analyzátoru pro zpracování jazykových pravidel cílového jazyka.

  ![Snímek obrazovky znázorňující obrazovku s poli pro ukázku reálného majetku. Je zvýrazněna skupina polí, která ukazuje, jak přiřazení analyzátoru jazyka odpovídají jazykům zvýrazněných polí.](./media/search-filters-language/lang-fields.png)

> [!Note]
> Příklady kódu znázorňující definice polí s analyzátory jazyků naleznete v tématu [definice indexu (.NET)](./search-get-started-dotnet.md) a [definice indexu (REST)](./search-get-started-powershell.md).

## <a name="build-and-load-an-index"></a>Sestavení a načtení indexu

Mezilehlého (a možná zjevné) kroku je, že před [vytvořením dotazu musíte sestavit a naplnit index](./search-get-started-dotnet.md) . Tento krok uvádíme pro úplnost. Jedním ze způsobů, jak zjistit, zda je index k dispozici, je kontrola seznamu indexů na [portálu](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Omezení výsledků dotazu a oříznutí

Parametry dotazu slouží k omezení vyhledávání na konkrétní pole a následnému oříznutí výsledků všech polí, která nejsou pro váš scénář vhodná. Vzhledem k tomu, že je výsledkem omezení vyhledávání pole obsahující francouzsky řetězce, byste použili **searchFields** k zacílení dotazu na pole obsahující řetězce v daném jazyce. 

Ve výchozím nastavení vrátí hledání všechna pole, která jsou označena jako získatelné. V takovém případě můžete chtít vyloučit pole, která neodpovídají jazykově specifickému hledanému prostředí, které chcete poskytnout. Konkrétně, pokud jste omezili hledání na pole se francouzskými řetězci, pravděpodobně budete chtít vyloučit pole s anglickým řetězcem z vašich výsledků. Použití parametru dotazu **$Select** vám poskytne kontrolu nad tím, která pole se vrátí do volající aplikace.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> I když v dotazu není žádný $filter argument, je tento případ použití silně spojený s koncepty filtru, takže se zobrazí jako scénář filtrování.

## <a name="see-also"></a>Viz také

+ [Filtry v Azure Kognitivní hledání](search-filters.md)
+ [Analyzátory jazyka](/rest/api/searchservice/language-support)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Rozhraní API pro vyhledávání v dokumentech](/rest/api/searchservice/search-documents)