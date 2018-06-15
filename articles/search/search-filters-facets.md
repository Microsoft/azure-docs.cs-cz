---
title: Filtry omezující vlastnosti ve službě Azure Search | Microsoft Docs
description: Filtrujte kritéria, na základě identity uživatele zabezpečení, jazyk, geografického umístění nebo číselné hodnoty omezit výsledky vyhledávání na dotazy do služby Azure Search, hostované cloudové vyhledávací službě v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: heidist
ms.openlocfilehash: 3f2cfea52d3c3f4bfc75364d0662a4218219152d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792405"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Jak vytvořit filtr omezující vlastnosti ve službě Azure Search 

Fasetové navigace se používá pro řízené samotným filtrování výsledků dotazu v aplikaci pomocí vyhledávání, kde aplikace nabízí ovládacích prvků uživatelského rozhraní pro oboru vyhledávání skupin dokumentů (například kategorie nebo značek), a poskytuje strukturu dat pro zálohování Azure Search prostředí. V tomto článku zkontrolujte rychle základní kroky pro vytvoření struktury Fasetové navigace zálohování možnosti vyhledávání, které chcete poskytnout. 

> [!div class="checklist"]
> * Zvolte pole pro filtrování a používání omezujících vlastností
> * Nastavení atributů na pole
> * Sestavení indexu a načtení dat
> * Přidejte k dotazu filtry omezující vlastnost
> * Zpracování výsledků

Omezující vlastnosti jsou dynamické a vrácený na dotazu. Hledání odpovědi, aby s nimi kategorie faset pro pohyb výsledky. Pokud nejste obeznámeni s omezujícími vlastnostmi, v následujícím příkladu je ilustraci ve struktuře Fasetové navigace.

  ![](./media/search-filters-facets/facet-nav.png)

Nový Fasetové navigace a chcete víc podrobností? V tématu [implementaci Fasetové navigace ve službě Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Zvolte pole

Přes jednu hodnotu pole a také kolekce, lze vypočítat omezující vlastnosti. Pole, která nejlépe fungovat ve Fasetové navigace mít nízkou mohutnost: malý počet jedinečných hodnot, které se opakují v celé dokumenty v souhrnu vaše vyhledávání (například seznam barev, zemí nebo názvy brand). 

Používání omezujících vlastností je povolený na základě pole pole při vytváření indexu, nastavením následující atributy na hodnotu TRUE: `filterable`, `facetable`. Kategorizovat je možné pouze filtrovatelná pole.

Všechny [pole typu](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) , pravděpodobně by šlo použít v Fasetové navigace je označena jako "facetable":

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections
+ Číselné pole typů: Edm.Double Edm.Int32, Edm.Int64,

Edm.GeographyPoint nelze použít v Fasetové navigace. Omezující vlastnosti se vytvářejí na základě lidského čitelný text nebo čísla. Jako takový omezující vlastnosti nejsou podporovány pro geograficky souřadnice. Potřebovali byste pole města nebo oblasti, které chcete omezující vlastnosti podle umístění.

## <a name="set-attributes"></a>Nastavení atributů

Atributy indexu, které řídí použití pole jsou přidány do definice jednotlivých polí v indexu. V následujícím příkladu pole s nízkou mohutnost, užitečné pro používání faset, skládá z: kategorie (hotelů, motel, hostel), zařízení a hodnocení. 

V rozhraní .NET API filtrování atributy musí být explicitně nastaveny. V rozhraní REST API používání omezujících vlastností a filtrování povolená ve výchozím nastavení, což znamená, že potřebujete explicitně nastavit atributy, pokud chcete vypnout. Ačkoli to není technicky požadované, ukážeme práv v následujícím příkladu REST pro vzdělávací účely. 

> [!Tip]
> Jako osvědčený postup pro výkon a optimalizaci úložiště vypněte používání omezujících vlastností pro pole, která má být nikdy použit jako omezující vlastnost. Konkrétně polí s řetězcem hodnoty typu singleton, jako je například ID nebo produktu, název, musí být nastavena na "Facetable": false, aby se zabránilo jejich náhodnému (a neúčinná) používat v Fasetové navigace.


```http
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String", "filterable": true, "facetable": true},
        {"name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true},
        {"name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

> [!Note]
> Tato definice indexu budou zkopírována z [vytvoření indexu Azure Search pomocí REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Je identický s výjimkou poškození rozdíly v definicích pole. Filtrování a kategorizovatelných atributy jsou explicitně přidat na kategorii, značky, parkingIncluded, smokingAllowed a hodnocení pole. V praxi získáte filtrování a kategorizovatelných pro volné na typech pole Edm.String, Edm.Boolean a Edm.Int32. 

## <a name="build-and-load-an-index"></a>Sestavení a načte index

Na krok zprostředkující (a případně zřejmé) je, že máte k [vytvořit a naplnit index](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) před formulování dotazu. Jsme zmínili, tento krok sem pro úplnost. Je možné určit, zda je k dispozici index kontrolou seznamu indexy [portál](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Přidejte k dotazu filtry omezující vlastnost

V kódu aplikace sestavte dotaz, který určuje všech součástí platný dotaz, včetně vyhledávacích výrazech, omezující vlastnosti, filtrů, vyhodnocování profilů – nic používá k formulovali žádost. Následující příklad vytvoří požadavek, který vytvoří omezující vlastnosti navigace na základě typu ubytovací, hodnocení a jiných zařízení.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Návratový filtrované výsledky na události kliknutí

Výraz filtru zpracovává událost kliknutím na je hodnota. Zadána omezující vlastnost kategorie kliknutím na kategorii "motel" se implementuje pomocí `$filter` výraz, který vybere přizpůsobení daného typu. Když uživatel klikne "motely" k označení, že by se měly zobrazit pouze motely, zahrnuje další aplikace odešle dotaz $filter = kategorie eq 'motely'.

Následující fragment kódu přidá kategorie filtru, pokud uživatel vybere z kategorie omezující vlastnost hodnotu.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
Pomocí rozhraní REST API, žádost by být kloubové jako `$filter=category eq 'c1'`. Chcete-li kategorie pole s více hodnotami, použijte následující syntaxi: `$filter=category/any(c: c eq 'c1')`

## <a name="tips-and-workarounds"></a>Tipy a řešení

### <a name="initialize-a-page-with-facets-in-place"></a>Inicializace zobrazí stránka s omezujícími vlastnostmi na místě

Pokud chcete k chybě při inicializaci stránky s omezujícími vlastnostmi na místě, můžete odeslat dotaz jako součást inicializace stránky počáteční hodnoty stránku s strukturu počáteční omezující vlastnosti.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Zachovat ve struktuře Fasetové navigace asynchronně filtrované výsledků

Jedním z problémů s omezující vlastnosti navigace ve službě Azure Search je, že existují omezující vlastnosti pro pouze aktuální výsledky. V praxi je běžné zachovat statické sadu omezující vlastnosti tak, aby mohli uživatelé procházet obráceným, návrat kroků chcete prozkoumat alternativní cesty prostřednictvím vyhledávání obsahu. 

Přestože je toto běžně používá, není něco struktuře Fasetové navigace aktuálně poskytuje se na pole. Vývojáře, kteří chtějí statické omezující vlastnosti obvykle obejít omezený výběr vydáním dva filtrované dotazy: jeden rozsah výsledků, druhé používá k vytvoření statických seznam omezující vlastnosti pro účely navigace.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Filtry ve službě Azure Search](search-filters.md)
+ [Vytvoření indexu REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Hledání dokumentů rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

