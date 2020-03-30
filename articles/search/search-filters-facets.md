---
title: Fazetové filtry pro navigaci při vyhledávání v aplikacích
titleSuffix: Azure Cognitive Search
description: Kritéria filtruje podle identity zabezpečení uživatele, geografické polohy nebo číselných hodnot, abyste snížili výsledky hledání v dotazech v Azure Cognitive Search, hostované službě cloudového vyhledávání v Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792900"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Jak vytvořit filtr omezující vlastnosti v Azure Cognitive Search 

Famitová navigace se používá pro filtrování s vlastním řízením na výsledcích dotazu v aplikaci pro vyhledávání, kde vaše aplikace nabízí ovládací prvky uživatelského rozhraní pro hledání oborů pro skupiny dokumentů (například kategorie nebo značky) a Azure Cognitive Search poskytuje strukturu dat podpořit zážitek. V tomto článku rychle zkontrolujte základní kroky pro vytvoření fazetované navigační struktury, která podporuje prostředí vyhledávání, které chcete poskytnout. 

> [!div class="checklist"]
> * Volba polí pro filtrování a fazetování
> * Nastavení atributů v poli
> * Sestavení indexu a načtení dat
> * Přidání omezujících hodnot k dotazu
> * Zpracování výsledků

Omezující vlastnosti jsou dynamické a vráceny v dotazu. Odpovědi na vyhledávání s sebou přinášejí omezující události, které se používají k navigaci ve výsledcích. Pokud nejste obeznámeni s omezující vlastnostmi, následující příklad je ilustrace omezující vlastnost navigační struktury.

  ![](./media/search-filters-facets/facet-nav.png)

Začínáte s fazetovou navigací a chcete více podrobností? V [tématu Jak implementovat fazetovou navigaci v Azure Cognitive Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Výběr polí

Omezující aspekty lze vypočítat přes pole s jednou hodnotou, stejně jako kolekce. Pole, která nejlépe fungují v fazetované navigaci, mají nízkou mohutnost: malý počet odlišných hodnot, které se opakují v dokumentech ve vyhledávacím korpusu (například seznam barev, země nebo oblasti nebo názvy značek). 

Fazetování je povoleno v poli podle pole při vytváření `facetable` indexu `true`nastavením atributu na . Obecně byste měli `filterable` také `true` nastavit atribut pro tato pole tak, aby vyhledávací aplikace můžete filtrovat na těchto polích na základě omezující vlastnosti, které vybere koncový uživatel. 

Při vytváření indexu pomocí rozhraní REST API je ve výchozím nastavení označen `facetable` libovolný typ [pole,](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) který by mohl být použit v fazetované navigaci:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Typy číselných `Edm.Int32` `Edm.Int64`polí: , ,`Edm.Double`
+ Kolekce výše uvedených typů (například `Collection(Edm.String)` nebo `Collection(Edm.Double)`)

V fazetované navigaci nelze použít `Edm.GeographyPoint` ani `Collection(Edm.GeographyPoint)` pole. Omezující aspekty fungují nejlépe na polích s nízkou mohutností. Vzhledem k rozlišení geosouřadnic je vzácné, že všechny dvě sady souřadnic budou v dané datové sadě stejné. Jako takové nejsou pro zeměpisné souřadnice podporovány omezující okolnosti. K umístění byste potřebovali pole města nebo oblasti.

## <a name="set-attributes"></a>Nastavit atributy

Atributy indexu, které řídí způsob použití pole, jsou přidány do jednotlivých definic polí v indexu. V následujícím příkladu se pole s nízkou mohutností, `category` která jsou užitečná `tags`pro `rating`fazetování, skládají z: (hotel, motel, hostel) a . Tato pole `filterable` mají `facetable` atributy a explicitně nastaveny v následujícím příkladu pro ilustrativní účely. 

> [!Tip]
> Jako osvědčený postup pro optimalizaci výkonu a úložiště vypněte omezující vlastnosti pro pole, která by nikdy neměla být použita jako omezující vlastnost. Zejména řetězcová pole pro jedinečné hodnoty, jako je například ID nebo název produktu, by měla být nastavena tak, aby `"facetable": false` se zabránilo jejich náhodnému (a neúčinnému) použití v fasetové navigaci.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Tato definice indexu se zkopíruje z [vytvoření indexu Azure Cognitive Search pomocí rozhraní REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Je totožný s výjimkou povrchních rozdílů v definicích polí. `filterable` Atributy `facetable` a jsou explicitně `tags` `parkingIncluded`přidány `smokingAllowed`do `rating` `category`polí , , , a. V praxi `filterable` `facetable` a by být povolena ve výchozím nastavení na tato pole při použití rozhraní REST API. Při použití sady .NET SDK musí být tyto atributy explicitně povoleny.

## <a name="build-and-load-an-index"></a>Sestavení a načtení indexu

Mezilehlý (a možná zřejmý) krok je, že je třeba [vytvořit a naplnit index](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) před formulovánídotazu. Zmiňujeme tento krok zde pro úplnost. Jedním ze způsobů, jak zjistit, zda je index k dispozici, je kontrola seznamu indexů na [portálu](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Přidání omezujících hodnot k dotazu

V kódu aplikace vytvořte dotaz, který určuje všechny části platného dotazu, včetně vyhledávacích výrazů, omezujících okolností, filtrů, profilů hodnocení – cokoli, co se používá k formulování požadavku. Následující příklad vytvoří požadavek, který vytvoří omezující vlastnost navigace na základě typu ubytování, hodnocení a další vybavení.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Vrácení filtrovaných výsledků při událostech kliknutí

Když koncový uživatel klikne na hodnotu omezující vlastnosti, obslužná rutina události kliknutí by měla použít výraz filtru k realizaci záměru uživatele. Vzhledem `category` k tomu, omezující faset, kliknutím `$filter` na kategorii "motel" je implementována s výrazem, který vybere ubytování tohoto typu. Když uživatel klepne na tlačítko "motel" označující, že by měly `$filter=category eq 'motel'`být zobrazeny pouze motely, další dotaz, který aplikace odešle, zahrnuje .

Následující fragment kódu přidá kategorii do filtru, pokud uživatel vybere hodnotu z omezující vlastnostkategorie.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Pokud uživatel klikne na hodnotu omezující vlastnosti pro pole kolekce, jako je `tags`například hodnota "fond", aplikace by měla použít následující syntaxi filtru:`$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Tipy a řešení

### <a name="initialize-a-page-with-facets-in-place"></a>Inicializovat stránku s omezujícími fasejemi na místě

Pokud chcete inicializovat stránku s omezujícími stránkami na místě, můžete odeslat dotaz jako součást inicializace stránky na počáteční strukturu omezujících stránek.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Zachovat fazetovou navigační strukturu asynchronně filtrovaných výsledků

Jednou z výzev s aspekty navigace v Azure Cognitive Search je, že omezující schopnosti existují pouze pro aktuální výsledky. V praxi je běžné zachovat statickou sadu omezujících fasejí tak, aby uživatel mohl procházet v opačném směru, retracing kroky prozkoumat alternativní cesty prostřednictvím obsahu vyhledávání. 

I když se jedná o běžný případ použití, není to něco, co omezující struktura navigace v současné době poskytuje out-of-the-box. Vývojáři, kteří chtějí statické omezující vlastnosti obvykle obejít omezení vydáním dvou filtrovaných dotazů: jeden s rozsahem na výsledky, druhý slouží k vytvoření statického seznamu omezujících vlastností pro účely navigace.

## <a name="see-also"></a>Viz také

+ [Filtry v Azure Cognitive Search](search-filters.md)
+ [Vytvořit rozhraní REST rozhraní REJSTŘÍKU](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Rozhraní API pro vyhledávání v dokumentech](https://docs.microsoft.com/rest/api/searchservice/search-documents)
