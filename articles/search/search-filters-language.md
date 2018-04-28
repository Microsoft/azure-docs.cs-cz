---
title: Filtry jazyka ve službě Azure Search | Microsoft Docs
description: Filtrujte kritéria, na základě identity uživatele zabezpečení, jazyk, geografického umístění nebo číselné hodnoty omezit výsledky vyhledávání na dotazy do služby Azure Search, hostované cloudové vyhledávací službě v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.date: 10/23/2017
ms.author: heidist
ms.openlocfilehash: 6d7fa7ab6db1fe9f8e2d1530c2917f4716a38079
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Jak filtrovat podle jazyka ve službě Azure Search 

Klíčovým požadavkem v vícejazyčné vyhledávací aplikaci je možnost vyhledávání přes a načtěte výsledky v jazyce přihlášeného uživatele. Jedním ze způsobů pro splnění požadavků jazyk vícejazyčné aplikace ve službě Azure Search, je vytvoření řadu pole vyhrazený pro ukládání řetězců v určitém jazyce a pak omezit fulltextové vyhledávání na právě tato pole v době dotazu.

Parametry dotazu v žádosti se používají k určení oboru operace vyhledávání i pak trim výsledky všechna pole, které neposkytují kompatibilní s vyhledáváním, kterou chcete doručovat obsah.

| Parametry | Účel |
|-----------|--------------|
| **searchFields** | Omezení fulltextového vyhledávání do seznamu s názvem pole. |
| **$select** | Ořízne odpověď na obsahovat pouze pole, které zadáte. Ve výchozím nastavení vrátí se dá načíst všechna pole. **$Select** parametr umožňuje zvolit ty, které chcete vrátit. |

Úspěch Tato technika závěsy integrity obsah pole. Vyhledávání systému Azure není přeložit řetězce nebo provádět zjišťování jazyka. Můžete se rozhodnout, abyste měli jistotu, že pole obsahující řetězce, které očekáváte.

## <a name="define-fields-for-content-in-different-languages"></a>Definování polí pro obsah v různých jazycích

Ve službě Azure Search dotazy cíle jeden index. Vývojáři, kteří chtějí poskytnout konkrétní jazyk řetězců v jednom vyhledáváním obvykle definování vyhrazené polí pro uložení hodnot: řetězce do jednoho pole pro angličtinu, jeden pro francouzštinu a tak dále. 

V našem ukázky, včetně [nemovitosti – ukázka](search-get-started-portal.md) vidíte níže, jste mohli vidět definice pole, podobně jako na následujícím snímku obrazovky. Všimněte si, jak tento příklad ukazuje jazyk přiřazení analyzátor pro pole v indexu. Pole obsahující řetězce líp fungovat v fulltextové vyhledávání při spárovat analyzátorem analyzovány pro zpracování lingvistické pravidel jazyka cíl.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Příklady kódu zobrazuje definice pole s jazyky analyzátorů najdete v tématu [definujte index (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) a [definujte index (REST)](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json).

## <a name="build-and-load-an-index"></a>Sestavení a načte index

Na krok zprostředkující (a případně zřejmé) je, že máte k [vytvořit a naplnit index](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) před formulování dotazu. Jsme zmínili, tento krok sem pro úplnost. Je možné určit, zda je k dispozici index kontrolou seznamu indexy [portál](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Omezit dotaz a uvolnění dočasné výsledky

Parametry v dotazu se používají k omezit vyhledávání konkrétních polí a pak trim výsledky všechna pole nebylo užitečné pro váš scénář. Zadané pole obsahující řetězce francouzštině cílem omezení hledání, byste použili **searchFields** pro dotaz na pole obsahující řetězce v daném jazyce. 

Ve výchozím nastavení vrátí vyhledávání všechna pole, které jsou označené jako dá načíst. Takto můžete chtít vyloučit pole, které nevyhovují možnosti vyhledávání pro specifický jazyk, které byste chtěli poskytnout. Konkrétně Pokud omezený počet vyhledávání pole s francouzštině řetězce, pravděpodobně chcete vyloučit pole s anglické řetězce z výsledků. Pomocí **$select** dotaz parametr umožňuje určit, přes která pole se vrátíte na volající aplikace.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> I když je no $filter argument na dotaz, tento případ použití je důrazně spojit s filtru koncepty, takže jsme nabízet v případě filtrování.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Filtry ve službě Azure Search](search-filters.md)
+ [Analyzátory jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Jak úplné textové vyhledávání funguje ve službě Azure Search](search-lucene-query-architecture.md)
+ [Hledání dokumentů rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

