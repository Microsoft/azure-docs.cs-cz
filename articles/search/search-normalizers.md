---
title: Normalizace textu pro filtry, omezující vlastnosti, řazení
titleSuffix: Azure Cognitive Search
description: Určete normalizace na textová pole v indexu pro přizpůsobení striktního chování pro porovnání klíčových slov při filtrování, omezující vlastnosti a řazení.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609073"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Normalizace textu pro filtrování bez rozlišení velkých a malých písmen, omezující vlastnosti a řazení

 > [!IMPORTANT]
 > Modul pro normalizaci je ve verzi Public Preview, který je k dispozici prostřednictvím **REST API 2020-06-30-Preview**. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s dodatečnými podmínkami použití.

Hledání a načítání dokumentů z indexu služby Azure Kognitivní hledání vyžaduje, aby dotaz odpovídal obsahu dokumentu. Obsah lze analyzovat tak, aby vytvořil tokeny pro spárování, jako je případ `search` , kdy je použit parametr, nebo může být použit jako – pro přesné porovnání klíčových slov, které je uvedeno v `$filter` , `facets` a `$orderby` . Tento úplný přístup k tomuto přístupu pokrývá většinu scénářů, ale je krátký, kde se vyžaduje jednoduché předběžné zpracování, jako je třeba velká a malá písmena, odebrání zvýraznění, asciifolding a tak dále, a to bez nutnosti projít celým řetězcem analýzy.

Představte si následující příklady:

+ `$filter=City eq 'Las Vegas'` budou vracet pouze dokumenty, které obsahují přesný text "Las Vegas", a vyloučit dokumenty s "LAS VEGAS" a "Las Vegas", které jsou nedostatečné, když případ použití vyžaduje všechny dokumenty bez ohledu na velikost písmen.

+ `search=*&facet=City,count:5` budou vracet "Las Vegas", "LAS VEGAS" a "Las Vegas" jako jedinečné hodnoty, i když se shodují se stejným městem.

+ `search=usa&$orderby=City` Vrátí města v lexicographical pořadí: "Las Vegas", "Praha", "Las Vegas", a to i v případě, že se jedná o stejné města bez ohledu na případ. 

## <a name="normalizers"></a>Normalizy

*Normalizer* je součástí vyhledávacího stroje zodpovědného za předběžné zpracování textu pro porovnání klíčových slov. Normalizace se podobají analyzátorům, s výjimkou případů, kdy dotaz netokenizovat. Mezi transformace, které je možné dosáhnout pomocí normalizování, patří:

+ Převod na malá a velká písmena.
+ Normalizuje akcenty a diakritická znaménka jako ö nebo ê na ekvivalentní znaky ASCII "o" a "e".
+ Mapuje znaky jako `-` a prázdné znaky na uživatelem zadaný znak.

Normalizace lze zadat u textových polí v indexu a používá se při indexování i provádění dotazů.

## <a name="predefined-and-custom-normalizers"></a>Předdefinované a vlastní normalizy 

Azure Kognitivní hledání podporuje předdefinované normalizace pro běžné případy použití a možnost přizpůsobit podle potřeby.

| Kategorie | Popis |
|----------|-------------|
| [Předdefinované normalizy](#predefined-normalizers) | Tato možnost je k dispozici předem a je možné ji použít bez jakékoli konfigurace. |
|[Vlastní normalizace](#add-custom-normalizers) | Pro pokročilé scénáře. Vyžaduje uživatelsky definovanou konfiguraci kombinace stávajících prvků, která se skládá z filtrů char a token. <sup>1</sup>|

<sup>(1)</sup> vlastní normalizace nespecifikují tokenizátory musíte nejdřív, protože normalizace vždy vytvoří jeden token.

## <a name="how-to-specify-normalizers"></a>Jak určit normalizace

Normalizace lze zadat pro každé pole v textových polích ( `Edm.String` a `Collection(Edm.String)` ), které mají alespoň jednu `filterable` `sortable` vlastnost, nebo `facetable` vlastností nastavenou na hodnotu true. Nastavení normalizace je volitelné a je `null` ve výchozím nastavení. Před konfigurací vlastního nastavení pro snadné použití doporučujeme vyhodnotit předdefinované normalizy. Pokud nejsou očekávány výsledky, zkuste použít jiný Normalizer.

Normalizace lze zadat pouze v případě, že je do indexu přidáno nové pole. Doporučujeme, abyste vyhodnotili normalizaci potřeb dopředu a v počátečních fázích vývoje přiřadíte normalizy při odstraňování a opětovném vytváření indexů. Normalizace nelze zadat pro pole, které již bylo vytvořeno.

1. Při vytváření definice pole v [indexu](/rest/api/searchservice/create-index)nastavte vlastnost  **normalizovat** na jednu z následujících možností: [předdefinovaný Normalizer](#predefined-normalizers) `lowercase` , například nebo vlastní normalizace (definováno ve stejném schématu indexu).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. Vlastní normalizace musí být nejprve definovány v sekci **[normalizes]** v indexu a pak přiřazeny k definici pole, jak je uvedeno v předchozím kroku. Další informace najdete v tématech [vytvoření indexu](/rest/api/searchservice/create-index) a také [Přidání vlastních normalizů](#add-custom-normalizers).


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Chcete-li změnit normalizace stávajícího pole, bude nutné znovu sestavit index úplně (jednotlivá pole nemůžete znovu sestavit).

Dobrým řešením pro produkční indexy, kde je opětovné sestavení indexů nákladné, je vytvořit nové pole stejné jako staré, ale s novým normalizem a použít ho místo starého. Pomocí [aktualizačního indexu](/rest/api/searchservice/update-index) zahrňte nové pole a [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) k jeho naplnění. Později jako součást plánované údržby indexu můžete vyčistit index a odebrat tak zastaralá pole.

## <a name="add-custom-normalizers"></a>Přidat vlastní normalizy

Vlastní normalizace jsou definovány v rámci schématu indexu a lze je zadat pomocí vlastnosti pole. Definice vlastního normalizace obsahuje název, typ, jeden nebo více filtrů znaků a filtrů tokenů. Filtry znaků a filtry tokenů jsou stavebními bloky pro vlastní normalizaci a zodpovídají za zpracování textu. Tyto filtry jsou aplikovány zleva doprava.

 `token_filter_name_1`Je název filtru tokenů a `char_filter_name_1` `char_filter_name_2` jsou názvy filtrů znaků (Další informace najdete v tabulkách [podporované filtry tokenů](#supported-token-filters) a filtry znaků níže).

Definice normalizace je součástí většího indexu. Informace o zbývajících částech indexu najdete v tématu [Vytvoření rozhraní API pro index](/rest/api/searchservice/create-index) .

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Vlastní normalizace lze přidat během vytváření indexu nebo později aktualizací existujícího. Přidání vlastního normalizu do existujícího indexu vyžaduje zadání příznaku **allowIndexDowntime** v [indexu aktualizace](/rest/api/searchservice/update-index) a způsobí, že index nebude k dispozici po dobu několika sekund.

## <a name="normalizers-reference"></a>Reference k normalizům

### <a name="predefined-normalizers"></a>Předdefinované normalizy
|**Název**|**Popis a možnosti**|  
|-|-|  
|standardní| Malý text následovaný asciifoldingem.|  
|malá| Transformuje znaky na malá písmena.|
|všechna| Transformuje znaky na velká písmena.|
|asciifolding| Transformuje znaky, které nejsou v bloku Unicode s latinkou na jejich ekvivalent ASCII, pokud jedna existuje. Například změna na.|
|Elizi| Odebere Elizi od začátku tokenů.|

### <a name="supported-char-filters"></a>Podporované filtry znaků
Další podrobnosti o filtrech znaků najdete v referenčních informacích o [filtrech znaků](index-add-custom-analyzers.md#CharFilter).
+ [mapování](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Podporované filtry tokenů
V následujícím seznamu jsou uvedeny filtry tokenů podporované pro normalizování a je podmnožinou celkových filtrů tokenů zapojených do lexikální analýzy. Další podrobnosti o filtrech najdete v referenčních informacích o [filtrech tokenů](index-add-custom-analyzers.md#TokenFilters).

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [Elizi](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [malá](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [všechna](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Příklad vlastního normalizování

Následující příklad znázorňuje vlastní definici normalizace s odpovídajícími filtry znaků a filtry tokenů. Vlastní možnosti pro filtry znaků a filtry tokenů jsou zadány samostatně jako pojmenované konstrukce a následně odkazovány v definici normalizace, jak je znázorněno níže.

* Vlastní normalizace "my_custom_normalizer" je definována v `normalizers` oddílu definice indexu.
* Normalizace se skládá ze dvou filtrů znaků a tří filtrů tokenů: Elizi, malými písmeny a přizpůsobený filtr asciifolding "my_asciifolding".
* První filtr znaků "map_dash" nahradí všechny pomlčky podtržítkem a druhým prvním znakem "remove_whitespace" Odebere všechny mezery.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Viz také
+ [Analyzátory pro jazyk a zpracování textu](search-analyzers.md)

+ [Rozhraní API pro vyhledávání v dokumentech](/rest/api/searchservice/search-documents) 
