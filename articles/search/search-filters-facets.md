---
title: Filtry omezujících vlastností pro navigaci v aplikacích
titleSuffix: Azure Cognitive Search
description: Filtrování kritérií podle identity zabezpečení uživatele, geografického umístění nebo číselných hodnot pro omezení výsledků hledání dotazů v Azure Kognitivní hledání, hostované cloudové vyhledávací služby na Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792900"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Postup sestavení filtru omezujících vlastností v Azure Kognitivní hledání 

Nafiltrovaná navigace se používá pro filtrování na základě výsledků dotazu ve vyhledávací aplikaci, kde aplikace nabízí ovládací prvky uživatelského rozhraní pro určení oboru hledání do skupin dokumentů (například kategorie nebo značky) a Azure Kognitivní hledání poskytuje strukturu dat. pro obnovení prostředí. V tomto článku se rychle podívejte na základní kroky pro vytvoření omezující navigační struktury, kterou chcete poskytnout. 

> [!div class="checklist"]
> * Zvolit pole pro filtrování a omezující vlastnosti
> * Nastavení atributů v poli
> * Sestavení indexu a načtení dat
> * Přidání filtrů omezujících vlastností do dotazu
> * Zpracování výsledků

Omezující vlastnosti jsou dynamické a vracejí se v dotazu. Hledání odpovědí vede s těmito kategoriemi omezujícími vlastnostmi, které slouží k procházení výsledků. Pokud nejste obeznámeni s omezujícími vlastnostmi, následující příklad je ilustrace navigační struktury omezující vlastnosti.

  ![](./media/search-filters-facets/facet-nav.png)

Novinka s omezujícími podrobnostmi a chcete podrobnější informace? Přečtěte si téma [implementace omezující navigace v Azure kognitivní hledání](search-faceted-navigation.md).

## <a name="choose-fields"></a>Zvolit pole

Omezující vlastnosti se dají vypočítat přes pole s jednou hodnotou i pro kolekce. Pole, která fungují nejlépe v omezující navigaci, mají nízkou mohutnost: malý počet jedinečných hodnot, které se opakují v rámci corpus hledání (například seznam barev, zemí nebo oblastí nebo názvy značek). 

Při vytváření indexu nastavením atributu `facetable` na `true`je na základě pole povoleno vytváření omezující vlastnosti. Obecně byste také měli nastavit atribut `filterable` na `true` pro taková pole, aby vaše vyhledávací aplikace mohla filtrovat tato pole na základě omezujících vlastností, které koncový uživatel vybere. 

Při vytváření indexu pomocí REST API musí být libovolný [Typ pole](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) , který může být použit v naznačené navigaci, označen jako `facetable` ve výchozím nastavení:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Typy číselného pole: `Edm.Int32`, `Edm.Int64``Edm.Double`
+ Kolekce výše uvedených typů (například `Collection(Edm.String)` nebo `Collection(Edm.Double)`)

V omezující navigaci nelze použít pole `Edm.GeographyPoint` nebo `Collection(Edm.GeographyPoint)`. Omezující vlastnosti fungují nejlépe u polí s nízkou mohutnost. Z důvodu rozlišení geografických souřadnic je pravděpodobné, že jakékoliv dvě sady souřadnice budou v dané datové sadě stejné. V takovém případě nejsou omezující vlastnosti pro geografické souřadnice podporovány. Podle umístění budete potřebovat pole město nebo oblast pro omezující vlastnost.

## <a name="set-attributes"></a>Nastavit atributy

Atributy indexu, které řídí způsob použití pole, jsou přidány do jednotlivých definic polí v indexu. V následujícím příkladu jsou pole s nízkou mohutnou užitečnou pro omezující vlastnost tvořená: `category` (hotelu, Motel, Hostel), `tags`a `rating`. Tato pole mají atributy `filterable` a `facetable` nastaveny explicitně v následujícím příkladu pro ilustrativní účely. 

> [!Tip]
> Jako osvědčený postup pro optimalizaci výkonu a úložiště můžete zapnout charakteristiky pro pole, která by se nikdy neměla používat jako omezující vlastnost. Konkrétně pole řetězce pro jedinečné hodnoty, jako je ID nebo název produktu, by měly být nastavené na `"facetable": false`, aby se zabránilo nechtěnému (a neúčinnému) použití v omezující navigaci.


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
> Tato definice indexu je zkopírována z [části Vytvoření indexu služby Azure kognitivní hledání pomocí REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Je stejný, s výjimkou povrchových rozdílů v definicích polí. Atributy `filterable` a `facetable` jsou explicitně přidány v polích `category`, `tags`, `parkingIncluded`, `smokingAllowed`a `rating`. V praxi by při použití REST API byla ve výchozím nastavení v těchto polích povolena `filterable` a `facetable`. Při použití sady .NET SDK musí být tyto atributy povoleny explicitně.

## <a name="build-and-load-an-index"></a>Sestavení a načtení indexu

Mezilehlého (a možná zjevné) kroku je, že před [vytvořením dotazu musíte sestavit a naplnit index](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) . Tento krok uvádíme pro úplnost. Jedním ze způsobů, jak zjistit, zda je index k dispozici, je kontrola seznamu indexů na [portálu](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Přidání filtrů omezujících vlastností do dotazu

V kódu aplikace Sestavte dotaz, který určuje všechny části platného dotazu, včetně vyhledávacích výrazů, omezujících vlastností, filtrů a profilů vyhodnocování – cokoli, co se používá k formulaci žádosti. Následující příklad vytvoří požadavek, který vytvoří navigaci omezující vlastnosti na základě typu ubytování, hodnocení a dalších možností.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Vrátit filtrované výsledky při kliknutí na události

Když koncový uživatel klikne na hodnotu omezující vlastnosti, obslužná rutina události Click by měla použít výraz filtru ke realizaci záměru uživatele. Po `category` omezující vlastnost se kliknutím na kategorii Motel implementuje pomocí výrazu `$filter`, který vybere ubytování daného typu. Když uživatel klikne na "Motel", aby označoval, že by se měl zobrazit jenom Motels, další dotaz, který aplikace odešle, zahrnuje `$filter=category eq 'motel'`.

Následující fragment kódu přidá kategorii do filtru, pokud uživatel vybere hodnotu z omezující vlastnosti Category.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Pokud uživatel klikne na hodnotu omezující vlastnosti pro pole kolekce, jako je `tags`, například hodnota "Pool", vaše aplikace by měla použít následující syntaxi filtru: `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Tipy a alternativní řešení

### <a name="initialize-a-page-with-facets-in-place"></a>Inicializovat stránku s omezujícími vlastnostmi

Pokud chcete inicializovat stránku s omezujícími vlastnostmi, můžete odeslat dotaz jako součást inicializace stránky, aby se stránka nasadí na počáteční strukturu omezující vlastnosti.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Asynchronní zachování omezujících vlastností struktury v rámci filtrovaných výsledků

Jedním z problémů s navigací omezujícími vlastnostmi v Azure Kognitivní hledání je, že omezující vlastnosti existují jenom pro aktuální výsledky. V praxi je běžné uchovávat statickou sadu omezujících vlastností, aby uživatel mohl přecházet do reverzních a přetrasovacích kroků a prozkoumat alternativní cesty prostřednictvím vyhledávaného obsahu. 

I když se jedná o běžný případ použití, není to něco, že navigační struktura omezující vlastnosti aktuálně poskytuje okamžitý stav. Vývojáři, kteří chtějí, aby statické omezující vlastnosti typicky obužívali omezení tím, že vydávají dva filtrované dotazy: jeden obor s výsledky, druhý, který se používá k vytvoření statického seznamu omezujících vlastností pro účely navigace.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Filtry v Azure Kognitivní hledání](search-filters.md)
+ [Vytvořit index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Hledat dokumenty REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
