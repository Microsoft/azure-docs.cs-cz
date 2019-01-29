---
title: 'Příklad: Víceúrovňové omezující vlastnosti – Azure Search'
description: Zjistěte, jak vytvořit strukturu, "faceting" pro více úrovní taxonomií vytváření vnořených navigační strukturu, kterou můžete na stránkách aplikace.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 7fa17528931be40109d81edac0f15a6a6822ec01
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194810"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Příklad: Víceúrovňové omezující vlastnosti v Azure Search

Azure Search schémata nepodporují explicitně víceúrovňových taxonomie kategorií, ale můžete ji odhadnout je pomocí manipulace s obsahem před indexování a potom se použijí nějakou zvláštní zacházení s výsledky. 

## <a name="start-with-the-data"></a>Začněte s daty

Příklad v tomto článku je založen na předchozím příkladu [Model databáze AdventureWorks inventáře](search-example-adventureworks-modeling.md), na které si předvedeme omezující vlastnosti na více úrovních ve službě Azure Search.

AdventureWorks má jednoduché taxonomie dvouúrovňová pomocí vztahu nadřazenosti a podřízenosti. Pro pevnou délkou taxonomie hlubin této struktury jednoduchý dotaz připojení k SQL slouží k seskupení taxonomie:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Výsledky dotazu](./media/search-example-adventureworks/prod-query-results.png "výsledků dotazu")

## <a name="indexing-to-a-collection-field"></a>Indexování do kolekce pole

V indexu obsahující tuto strukturu, vytvářet **Collection(Edm.String)** pole ve schématu Azure Search pro ukládání těchto dat, a ujistěte se, že atributy pole zahrnout facetable prohledávatelné, filtrovatelné a získat.

Nyní názvy při indexování obsahu, který odkazuje na konkrétní taxonomie kategorií, odešlete taxonomie jako pole obsahujícího text z každou úroveň taxonomie. Například pro entitu s `ProductCategoryId = 5 (Mountain Bikes)`, odešlete pole jako `[ "Bikes", "Bikes|Mountain Bikes"]`

Všimněte si, že zařazení nadřazené kategorie "Kol" v hodnotě podřízené kategorie "Horská kola". Každá podkategorie vložit jeho celou cestu relativní vzhledem k kořenový element. Znak oddělovače kanálu je volitelný, ale musí být konzistentní vzhledem k aplikacím a by se neměl zobrazit ve zdrojovém textu. Znak oddělovače se použije v kódu aplikace k rekonstrukci stromu taxonomie ve výsledcích omezující vlastnost.

## <a name="construct-the-query"></a>Vytvořit dotaz

Při vydání dotazů, patří následující omezující vlastnost specifikaci (kde taxonomie zahrnuje epics vlastního pole taxonomie kategorizovatelné): `facet = taxonomy,count:50,sort:value`

Hodnotu count musí být dostatečně vysoká, aby vrácení všech hodnot možné taxonomie. Dat AdventureWorks obsahuje 41 taxonomie odlišné hodnoty, takže `count:50` je dostačující.

  ![Fasetová filtr](./media/search-example-adventureworks/facet-filter.png "Fasetová filtru")

## <a name="build-the-structure-in-client-code"></a>Struktura v klientském kódu sestavení

V kódu aplikace klienta rekonstrukci stromu taxonomie rozdělením každá hodnota omezující vlastnosti na znakem svislé čáry.

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

**Kategorie** objektu teď můžete použít k vykreslení sbalitelné taxonomie stromu s přesný počet:

  ![Víceúrovňové fasetová filtr](./media/search-example-adventureworks/multi-level-facet.png "víceúrovňové fasetová filtru")

 
Každé propojení ve stromu měli použít související filtru. Příklad:

+ **taxonomie/any** `(x:x eq 'Accessories')` vrátí všechny dokumenty ve větvi příslušenství
+ **taxonomie/any** `(x:x eq 'Accessories|Bike Racks')` vrátí jenom dokumenty s podkategorie nosiče větvi příslušenství.

Tato technika se škálovat pro složitější scénáře, jako jsou podrobnější taxonomie stromy a duplicitní podkategorie, ke kterým dochází v rámci jiné nadřazené kategorie (například `Bike Components|Forks` a `Camping Equipment|Forks`).

> [!TIP]
> Rychlost dotazu je ovlivněna počet omezujících vlastností vrácena. Chcete-li podporovat taxonomie velmi velké sady, zvažte přidání facetable **Edm.String** pole pro uchování hodnoty nejvyšší úrovně taxonomii pro každý dokument. Potom použijte stejný postup výše, ale pouze provedení dotazu omezující kolekce – vlastnost (filtruje taxonomie kořenové pole) Pokud je uživatel rozbalí uzel nejvyšší úrovně. Nebo, pokud 100 % odvolání se nevyžaduje, jednoduše snížení počtu omezující vlastnosti na přiměřenou hodnotu a ujistěte se, že omezující vlastnost položky jsou seřazeny podle počtu.

## <a name="see-also"></a>Další informace najdete v tématech

[Příklad: Model databáze AdventureWorks inventáře pro službu Azure Search](search-example-adventureworks-modeling.md)