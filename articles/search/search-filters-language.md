---
title: Filtry jazyka ve službě Azure Search | Dokumentace Microsoftu
description: Filtrovat kritéria zabezpečení identity uživatele, jazyka, geografického umístění nebo číselné hodnoty ke snížení výsledky hledání na dotazy ve službě Azure Search, hostované cloudové vyhledávací službě v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: heidist
ms.openlocfilehash: 2bacffe64fed3e2ee0cc2eb983776b4ab7086e51
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466577"
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Jak filtrovat podle jazyka ve službě Azure Search 

Klíčovým požadavkem v aplikaci vícejazyčné vyhledávání je schopnost vyhledávat a načíst výsledky v jazyce vlastněných uživateli. Jedním ze způsobů pro splnění požadavků jazyka vícejazyčné aplikace ve službě Azure Search je vytvořit řadu pole vyhrazený pro ukládání řetězců v určitém jazyce a poté omezit fulltextové vyhledávání pouze na pole v době zpracování dotazu.

Parametry dotazu v žádosti slouží k oboru operace hledání i pak trim výsledky všech polí, které neposkytují kompatibilní s možnosti vyhledávání, kterou chcete doručovat obsah.

| Parametry | Účel |
|-----------|--------------|
| **searchFields** | Omezení fulltextového vyhledávání do seznamu pojmenovaná pole. |
| **$select** | Ořízne odpověď obsahovat pouze pole, které zadáte. Ve výchozím nastavení budou vráceny všechny zobrazitelná pole. **$Select** parametr vám umožní vybrat ty, které chcete vrátit. |

Úspěch tuto techniku závěsy na integritě obsah pole. Služba Azure Search nepodporuje přeložit řetězce ani provádět rozpoznávání jazyka. Je jenom na vás, abyste měli jistotu, že pole obsahující řetězce, které očekáváte.

## <a name="define-fields-for-content-in-different-languages"></a>Definování polí pro obsah v různých jazycích

Dotazy ve službě Azure Search cílit na jeden index. Vývojáři, kteří chtějí poskytnout řetězců specifické pro jazyk v jedné vyhledávání obvykle definují vyhrazených polí pro uložení hodnot: řetězce jedno pole pro angličtinu, jeden pro francouzštinu a tak dále. 

V naše vzorcích, včetně [využil její plochu naplno ukázka](search-get-started-portal.md) vidíte níže, možná jste viděli definice pole, podobně jako na následujícím snímku obrazovky. Všimněte si, jak tento příklad ukazuje jazyk přiřazení analyzátor pro pole v indexu. Pole, které obsahují řetězce líp fungovat ve fulltextovém vyhledávání v kombinaci s analyzátor navržené pro zpracování jazyková pravidla cílový jazyk.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Příklady kódu znázorňující definice polí s analyzátory jazyky, naleznete v tématu [definování indexu (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) a [definování indexu (REST)](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json).

## <a name="build-and-load-an-index"></a>Vytvoření a načtení indexu

Na krok zprostředkující (a možná zřejmé) je, že budete muset [vytvořit a naplnit index](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) před formulování dotazu. Jsme zmínili, tento krok zde pro úplnost. Jedním ze způsobů k určení, zda index je k dispozici je seznam indexů vrácení se změnami [portál](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Omezení dotazu a výsledky uvolnění paměti

Parametry v dotazu se používají k omezení hledání konkrétních polí a potom trim výsledky všech polí není vhodné pro váš scénář. Zadaný cíl omezující hledání na pole, která obsahují francouzské řetězce, byste použili **searchFields** cílit na dotaz u polí, které obsahují řetězce v daném jazyce. 

Ve výchozím nastavení hledání vrátí všechna pole, které jsou označené jako retrievable. V důsledku toho můžete chtít vyloučit pole, která není v souladu s vyhledáváním specifické pro jazyk, který byste chtěli poskytnout. Konkrétně Pokud je omezený hledání pole s francouzské řetězce, pravděpodobně chcete vyloučit pole s anglické řetězce z výsledků. Použití **$select** dotazování parametr vám dává kontrolu nad pole, která se vrátí volající aplikace.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Sice no $filter argument u dotazu tento případ použití je důrazně přidružený filtr koncepty, tak Představujeme scénář a filtrování.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Filtry ve službě Azure Search](search-filters.md)
+ [Analyzátory jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Search](search-lucene-query-architecture.md)
+ [Hledání dokumentů rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

