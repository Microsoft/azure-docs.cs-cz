---
title: Filtry jazyka pro vícejazyčný obsah ve vyhledávacím indexu – Azure Search
description: Kritéria filtru pro podporu vyhledávání ve více jazycích, určení rozsahu provádění dotazů na pole pro konkrétní jazyk
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1eced868b180a916355d6f9fbfc8cd47a5d7d6e2
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649862"
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Postup filtrování podle jazyka v Azure Search 

Klíčovým požadavkem v aplikaci pro vyhledávání ve více jazycích je schopnost vyhledávat a načítat výsledky v jazyce uživatele. V Azure Search jeden ze způsobů, jak splňovat jazykové požadavky vícejazyčné aplikace, je vytvořit řadu polí vyhrazených pro ukládání řetězců do konkrétního jazyka a potom omezit fulltextové vyhledávání jenom na ta pole v době dotazu.

Parametry dotazu v žádosti se používají k určení oboru operace vyhledávání a pak se oříznou výsledky všech polí, která neposkytují obsah kompatibilní s vyhledávacím prostředím, které chcete doručit.

| Parametry | Účel |
|-----------|--------------|
| **searchFields** | Omezí úplné hledání textu na seznam pojmenovaných polí. |
| **$select** | Ořízne odpověď tak, aby zahrnovala pouze pole, která zadáte. Ve výchozím nastavení jsou vrácena všechna pole, která lze načíst. Parametr **$Select** vám umožní zvolit, která z nich se má vrátit. |

Úspěch této techniky se přestavuje na základě integrity obsahu polí. Azure Search nepřevádí řetězce ani neprovádí detekci jazyka. Ujistěte se, že pole obsahují řetězce, které očekáváte.

## <a name="define-fields-for-content-in-different-languages"></a>Definovat pole pro obsah v různých jazycích

V Azure Search dotazy cílí na jeden index. Vývojáři, kteří chtějí poskytnout řetězce pro konkrétní jazyk v rámci jednoho vyhledávacího prostředí, obvykle definují vyhrazená pole pro ukládání hodnot: jedno pole pro anglické řetězce, jeden pro francouzštinu a tak dále. 

V našich ukázkách, včetně níže uvedeného [příkladu reálného majetku](search-get-started-portal.md) , jste pravděpodobně viděli definice polí podobné následujícímu snímku obrazovky. Všimněte si, jak tento příklad ukazuje přiřazení analyzátoru jazyka pro pole v tomto indexu. Pole, která obsahují řetězce, fungují lépe při fulltextovém vyhledávání, když se spáruje s analýzou analyzátoru pro zpracování jazykových pravidel cílového jazyka.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Příklady kódu znázorňující definice polí s analyzátory jazyků naleznete v tématu [definice indexu (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) a [definice indexu (REST)](search-create-index-rest-api.md).

## <a name="build-and-load-an-index"></a>Sestavení a načtení indexu

Mezilehlého (a možná zjevné) kroku je, že před vytvořením dotazu musíte [Sestavit a naplnit index](https://docs.microsoft.com/azure/search/search-create-index-dotnet) . Tento krok uvádíme pro úplnost. Jedním ze způsobů, jak zjistit, zda je index k dispozici, je kontrola seznamu indexů na [portálu](https://portal.azure.com).

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
> I když v dotazu neexistuje žádný $filter argument, je tento případ použití silně spojen s koncepty filtru, takže prezentujme jako scénář filtrování.

## <a name="see-also"></a>Viz také:

+ [Filtry v Azure Search](search-filters.md)
+ [Analyzátory jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Jak funguje úplné hledání textu v Azure Search](search-lucene-query-architecture.md)
+ [Hledat dokumenty REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

