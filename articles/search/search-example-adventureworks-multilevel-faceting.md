---
title: 'Příklad: Omezující vlastnosti na více úrovních – Azure Search'
description: Naučte se vytvářet strukturální struktury pro taxonomie na více úrovních a vytváření vnořené navigační struktury, kterou můžete zahrnout na stránky aplikace.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 9a56bba55f9b3a59126168bc2bbbd50927c3fc78
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274082"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Příklad: Víceúrovňové omezující vlastnosti v Azure Search

Schémata Azure Search nepodporují explicitně víceúrovňové kategorie, ale můžete je roztřídit tak, že před indexováním provedete jejich navýšení a potom použijete pro ně speciální zpracování. 

## <a name="start-with-the-data"></a>Začněte s daty

Příklad v tomto článku sestaví předchozí příklad, [modeluje databázi inventáře AdventureWorks](search-example-adventureworks-modeling.md)a předvádí charakteristiky na více úrovních v Azure Search.

V AdventureWorks je jednoduchá meziúroveň taxonomie s relací typu nadřazený-podřízený. Pro hloubky taxonomie s pevnou délkou této struktury se dá použít jednoduchý dotaz SQL JOIN k seskupení taxonomie:

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

## <a name="indexing-to-a-collection-field"></a>Indexování do pole kolekce

V indexu, který obsahuje tuto strukturu, vytvořte pole **Collection (EDM. String)** ve schématu Azure Search pro uložení těchto dat. Zajistěte, aby atributy pole zahrnovaly prohledávatelný, filtrovatelné, plošky a získatelné.

Nyní při indexování obsahu, který odkazuje na konkrétní kategorii taxonomie, odešlete taxonomii jako pole obsahující text z každé úrovně taxonomie. Například pro entitu s `ProductCategoryId = 5 (Mountain Bikes)`můžete pole Odeslat jako`[ "Bikes", "Bikes|Mountain Bikes"]`

Všimněte si zahrnutí nadřazené kategorie "Bikes" v podřízené kategorii hodnota "horská kola". Každá podkategorie by měla vložit celou cestu relativní k kořenovému elementu. Oddělovač znaků kanálu je libovolný, ale musí být konzistentní a neměl by se zobrazovat ve zdrojovém textu. Znak oddělovače bude použit v kódu aplikace k rekonstrukci stromu taxonomie z výsledků omezujících vlastností.

## <a name="construct-the-query"></a>Sestavit dotaz

Při vydávání dotazů zahrňte následující specifikaci omezující vlastnosti (kde taxonomie je vaše pole taxonomie obličeje):`facet = taxonomy,count:50,sort:value`

Hodnota Count musí být dostatečně vysoká, aby vracela všechny možné hodnoty taxonomie. Data AdventureWorks obsahují 41 různých hodnot taxonomie, takže `count:50` je to dostačující.

  ![Filtr s omezujícími vlastnostmi](./media/search-example-adventureworks/facet-filter.png "Filtr s omezujícími vlastnostmi")

## <a name="build-the-structure-in-client-code"></a>Sestavení struktury v kódu klienta

V kódu klientské aplikace znovu sestavte strom taxonomie rozdělením každé hodnoty omezující vlastnosti na znak kanálu.

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

Objekt **Categories** se teď dá použít k vykreslení sbalitelného stromu taxonomie se správnými počty:

  ![víceúrovňový filtr s omezujícími vlastnostmi](./media/search-example-adventureworks/multi-level-facet.png "víceúrovňový filtr s omezujícími vlastnostmi")

 
U každého odkazu ve stromu by se měl použít příslušný filtr. Příklad:

+ **taxonomie/any** `(x:x eq 'Accessories')` vrátí všechny dokumenty ve větvi příslušenství.
+ **taxonomie/any** `(x:x eq 'Accessories|Bike Racks')` vrátí pouze dokumenty s podkategorií stojanů kol v rámci větve příslušenství.

Tato technika se škáluje tak, aby pokrývala složitější scénáře, jako jsou hlubší stromy taxonomie a zdvojené podkategorie, ke kterým `Bike Components|Forks` dochází `Camping Equipment|Forks`v různých nadřazených kategoriích (například a).

> [!TIP]
> Rychlost dotazu je ovlivněna počtem vrácených omezujících vlastností. Pro podporu velmi rozsáhlých sad taxonomie zvažte přidání pole **EDM. String** pro plošky, které bude uchovávat hodnotu taxonomie nejvyšší úrovně pro každý dokument. Pak použijte stejný postup, ale v případě, že uživatel rozbalí uzel nejvyšší úrovně, proveďte pouze dotaz Collection-Faced (filtrováno v kořenovém poli taxonomie). Nebo pokud není vyžadováno odvolání 100%, stačí snížit počet omezujících podmínek na rozumné číslo a zajistit, aby byly položky omezující vlastnosti seřazené podle počtu.

## <a name="see-also"></a>Viz také:

[Příklad: Modelování databáze inventáře AdventureWorks pro Azure Search](search-example-adventureworks-modeling.md)