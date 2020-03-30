---
title: 'Příklad: Víceúrovňové omezující faseje'
titleSuffix: Azure Cognitive Search
description: Naučte se vytvářet struktury fazetování pro víceúrovňové taxonomie a vytvářet vnořenou navigační strukturu, kterou můžete zahrnout na stránky aplikace.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792948"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Příklad: Víceúrovňové aspekty v Azure Cognitive Search

Schémata Azure Cognitive Search explicitně nepodporují víceúrovňové kategorie taxonomie, ale můžete je aproximovat manipulací s obsahem před indexováním a potom použít některé speciální zpracování výsledků. 

## <a name="start-with-the-data"></a>Začněte s daty

Příklad v tomto článku vychází z předchozího příkladu [Model AdventureWorks databáze inventáře](search-example-adventureworks-modeling.md), chcete-li demonstrovat víceúrovňové faceting v Azure Cognitive Search.

AdventureWorks má jednoduchou dvouúrovňovou taxonomii se vztahem rodič-podřízený. Pro hloubky taxonomie této struktury s pevnou délkou lze jednoduchý dotaz spojení SQL použít ke seskupení taxonomie:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Výsledky dotazu](./media/search-example-adventureworks/prod-query-results.png "Výsledky dotazu")

## <a name="indexing-to-a-collection-field"></a>Indexování pole Kolekce

V indexu obsahujícím tuto strukturu vytvořte pole **Kolekce(Edm.String)** ve schématu Azure Cognitive Search pro uložení těchto dat a ujistěte se, že atributy polí zahrnují prohledávatelné, filtrovatelné, směrovatelné a retrievable.

Nyní při indexování obsahu, který odkazuje na konkrétní kategorii taxonomie, odešlete taxonomii jako pole obsahující text z každé úrovně taxonomie. Například pro entitu s `ProductCategoryId = 5 (Mountain Bikes)`, odešlete pole jako`[ "Bikes", "Bikes|Mountain Bikes"]`

Všimněte si zahrnutí nadřazené kategorie "Kola" do hodnoty podřízené kategorie "Horská kola". Každá podkategorie by měla vložit celou svou cestu vzhledem ke kořenovému prvku. Oddělovač znaků kanálu je libovolný, ale musí být konzistentní a neměl by se zobrazovat ve zdrojovém textu. Znak oddělovače se použije v kódu aplikace k rekonstrukci stromu taxonomie z výsledků omezující chod.

## <a name="construct-the-query"></a>Vytvoření dotazu

Při vydávání dotazů uveďte následující specifikaci omezující vlastnosti (kde taxonomie je vaše pole komody s možností taxonomie):`facet = taxonomy,count:50,sort:value`

Hodnota počtu musí být dostatečně vysoká, aby vrátila všechny možné hodnoty taxonomie. Data AdventureWorks obsahuje 41 odlišné hodnoty taxonomie, takže `count:50` je dostačující.

  ![Fazetovaný filtr](./media/search-example-adventureworks/facet-filter.png "Fazetovaný filtr")

## <a name="build-the-structure-in-client-code"></a>Sestavení struktury v kódu klienta

V kódu klientské aplikace rekonstruujte strom taxonomie rozdělením každé omezující hodnoty na znak usměrňovacího kanálu.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

Objekt **kategorie** lze nyní použít k vykreslení sbalitelného stromu taxonomie s přesnými počty:

  ![víceúrovňový fazetovaný filtr](./media/search-example-adventureworks/multi-level-facet.png "víceúrovňový fazetovaný filtr")

 
Každý odkaz ve stromu by měl použít související filtr. Například:

+ **taxonomie/všechny** `(x:x eq 'Accessories')` vrátí všechny dokumenty v pobočce Příslušenství
+ **taxonomie/jakékoliv** `(x:x eq 'Accessories|Bike Racks')` vrácení pouze dokumentů s podkategorií stojanů na kola v rámci větve Příslušenství.

Tato technika bude škálovat tak, aby pokrývala složitější scénáře, jako jsou hlubší taxonomie stromy a duplicitní podkategorie, které se vyskytují v různých nadřazených kategoriích (například `Bike Components|Forks` a `Camping Equipment|Forks`).

> [!TIP]
> Rychlost dotazu je ovlivněna počtem vrácených omezujících ploch. Chcete-li podporovat velmi velké sady taxonomie, zvažte přidání pole **Edm.String** s lícovou hodnotou pro každý dokument. Potom použijte stejnou techniku výše, ale pouze provést dotaz omezující vlastnost kolekce (filtrované na kořenové taxonomie pole) při uživatel rozbalí uzel nejvyšší úrovně. Nebo pokud není vyžadováno 100% odvolání, jednoduše zmenšete počet omezujících položek na přiměřené číslo a ujistěte se, že položky omezující sousto jsou seřazeny podle počtu.

## <a name="see-also"></a>Viz také

[Příklad: Modelujte databázi adventureworks inventáře pro Azure Cognitive Search](search-example-adventureworks-modeling.md)