---
title: Filtry omezující vlastnost pro navigaci hledání v aplikacích – Azure Search
description: Filtrovat kritéria zabezpečení identity uživatele, geografického umístění nebo číselné hodnoty ke snížení výsledky hledání na dotazy ve službě Azure Search, hostované cloudové vyhledávací službě v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 94a0d3f19e595ac040d908ea47d6332ceae0943c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314801"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Jak vytvořit filtr omezující vlastnosti ve službě Azure Search 

Fasetová navigace se používá pro účely samořízeného filtrování na výsledky dotazu v aplikaci na hledání, kde vaše aplikace nabízí ovládací prvky uživatelského rozhraní pro vytváření oboru vyhledávání do skupin dokumentů (například kategorie nebo značky) a Azure Search poskytuje strukturu dat pro zálohování prostředí. V tomto článku se rychle zkontrolujte základní kroky pro vytvoření fasetovou strukturu navigace zálohování možnosti vyhledávání, které byste chtěli poskytnout. 

> [!div class="checklist"]
> * Vyberte pole pro filtrování a "faceting"
> * Nastavení atributů u pole
> * Vytvoření indexu a načtení dat
> * Přidat do dotazu filtry omezující vlastnost
> * Zpracování výsledků

Omezující vlastnosti jsou dynamická a vrácené na dotazu. Odpovědi na vyhledávání na používání s nimi kategorie faset použít pro účely navigace výsledky. Pokud nejste obeznámeni s omezujícími vlastnostmi, je následující příklad znázorňuje ve struktuře Fasetové navigace.

  ![](./media/search-filters-facets/facet-nav.png)

Teprve se fasetová navigace a chcete další podrobnosti? Zobrazit [jak ve službě Azure Search implementovat fasetovou navigaci](search-faceted-navigation.md).

## <a name="choose-fields"></a>Zvolte pole

Omezující vlastnosti můžete počítat z polí s jednou hodnotou, stejně jako kolekce. Pole, která nejlépe fungovat ve Fasetové navigace mají nízké Kardinalita: malý počet jedinečných hodnot, které se opakují v celém dokumenty ve vaší ve vyhledávacím korpusu služby (například seznam barev, zemí nebo názvy). 

"Faceting" je povolený na základě pole pomocí pole při vytváření indexu, tak, že nastavíte na hodnotu TRUE následující atributy: `filterable`, `facetable`. Kategorizovat je možné pouze filtrovatelná pole.

Žádné [typ pole](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) , který se může pravděpodobně použije v Fasetové navigace je označena jako "facetable":

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections
+ Číselné pole typů: Typem Edm.Int32 Edm.Int64, Edm.Double

Edm.GeographyPoint nelze použít v Fasetové navigace. Omezující vlastnosti se vytvářejí na základě lidské čitelný text nebo čísla. V důsledku toho omezující vlastnosti nejsou podporovány pro geografické souřadnice. Je třeba pole Město nebo oblasti pro omezující vlastnosti podle umístění.

## <a name="set-attributes"></a>Nastavení atributů

Atributy indexu, které řídí, jak se pole používá jsou přidány do definice jednotlivá pole v indexu. V následujícím příkladu, obsahovat pole s nízkou kardinalitu, užitečné pro používání faset,: kategorie (hotelu, motel, hostel), zařízení a hodnocení. 

V rozhraní .NET API filtrování atributů musí být explicitně nastaveny. V rozhraní REST API "faceting" a filtrování povolená ve výchozím nastavení, což znamená, že potřebujete explicitně nastavit atributy, pokud chcete vypnout. I když to není technicky potřeba, vám ukážeme, Poděkování v následujícím příkladu REST pro vzdělávací účely. 

> [!Tip]
> Jako osvědčený postup pro výkon a optimalizace úložiště vypněte "faceting" pro pole, která byste nikdy neměli používat jako omezující vlastnost. Pole řetězců pro hodnoty typu singleton, jako je například název ID nebo produktu, zejména, musí být nastavená na "Facetable": false, pokud chcete zabránit jejich nechtěné (a neefektivní) používat v Fasetové navigace.


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
> Tato definice indexu je zkopírován z [vytvoření indexu Azure Search pomocí rozhraní REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Je shodná s výjimkou povrchové rozdíly v definicích polí. Filterable a facetable atributy jsou explicitně přidány na kategorii, značky, parkingIncluded, smokingAllowed a hodnocení pole. V praxi získáte filterable a facetable pro zdarma na Edm.String, typem Edm.Boolean a typem Edm.Int32 typy polí. 

## <a name="build-and-load-an-index"></a>Vytvoření a načtení indexu

Na krok zprostředkující (a možná zřejmé) je, že budete muset [vytvořit a naplnit index](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) před formulování dotazu. Jsme zmínili, tento krok zde pro úplnost. Jedním ze způsobů k určení, zda index je k dispozici je seznam indexů vrácení se změnami [portál](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Přidat do dotazu filtry omezující vlastnost

V kódu aplikace sestavte dotaz, který určuje všechny části platného dotazu včetně vyhledávacích výrazech, omezující vlastnosti, filtry, bodovací profily – všechno umožňuje zformulujte podobnou žádost. Následující příklad vytvoří požadavek, který vytvoří omezující vlastnost navigace na základě typu ubytování, hodnocení a jiných zařízení.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Vrácení filtrovaných výsledků na události kliknutí

Výraz filtru zpracovává událost click na hodnota omezující vlastnosti. Zadaný omezující vlastnost kategorie, kliknutím na kategorii "motel" se implementuje pomocí `$filter` výraz, který vybere ubytováním daného typu. Když uživatel klikne na tlačítko "motely" k označení, že mají být zobrazeny pouze motely, zahrnuje další aplikace odesílá dotaz $filter = kategorie eq 'motely".

Následující fragment kódu přidá kategorie filtru, pokud uživatel vybere z kategorie omezující vlastnost hodnotu.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
Pomocí rozhraní REST API, žádost by být kloubové jako `$filter=category eq 'c1'`. Chcete-li kategorie pole s více hodnotami, použijte následující syntaxi: `$filter=category/any(c: c eq 'c1')`

## <a name="tips-and-workarounds"></a>Tipy a alternativní řešení

### <a name="initialize-a-page-with-facets-in-place"></a>Inicializace stránky s omezujícími vlastnostmi na místě

Pokud chcete inicializovat stránku s omezujícími vlastnostmi na místě, můžete odeslat dotaz jako součást inicializace stránky naplnit stránky s počáteční omezující vlastnost strukturou.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Zachovat ve struktuře Fasetové navigace asynchronně filtrovaných výsledků

Jedním z problémů s omezující vlastnost navigace ve službě Azure Search je, že existují omezující vlastnosti pro pouze aktuální výsledky. V praxi je běžné zachovat statickou sadu omezujících vlastností tak, aby uživatel přejít v opačném pořadí, návrat kroky pro zkoumání alternativní cesty hledání obsahu. 

Přestože je toto běžným případem použití, není něco struktuře Fasetové navigace v současné době poskytuje out-of-the-box. Vývojáři, kteří mají obvykle statické omezující vlastnosti obejít omezení pomocí dvou filtrovaných dotazů: jeden rozsah výsledky, druhé používá k vytvoření Statický seznam omezující vlastnosti pro účely navigace.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Filtry ve službě Azure Search](search-filters.md)
+ [Vytvoření indexu REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Hledání dokumentů rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

