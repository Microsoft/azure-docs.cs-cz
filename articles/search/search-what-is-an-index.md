---
title: Definice indexu a principy – Azure Search
description: Úvod do indexu terminologie a koncepty ve službě Azure Search, včetně fyzických structre obrácenou indexu.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/08/2017
ms.custom: seodec2018
ms.openlocfilehash: 5a39021367c2f51125876081e9174eb372d7b9c9
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353154"
---
# <a name="indexes-and-indexing-overview-in-azure-search"></a>Přehled indexování ve službě Azure Search a indexy

Ve službě Azure Search *index* je trvalé úložiště *dokumenty* a jiných objektů, které používá pro vyhledávání filtrované a úplný text na službu Azure Search. Dokument je jednou jednotkou prohledávatelných dat v indexu. Například internetový prodejce může mít dokument pro každou prodanou položku, zatímco tisková agentura může mít dokument pro každý článek atd. Mapování těchto položek do známějších databázových ekvivalentů: *indexů* se koncepčně podobá *tabulce*, kdy *dokumenty* přibližně odpovídají *řádkům* tabulky.

Při přidání nebo odeslat dokumenty nebo odesíláte vyhledávací dotazy do služby Azure Search, jsou odesílání požadavků do konkrétního indexu ve vyhledávací službě. Proces přidávání dokumentů do indexu se nazývá *indexování*.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Typy a atributy polí v indexu Azure Search
Při definování schématu musíte zadat název, typ a atributy každého pole v indexu. Typ pole klasifikuje data, která jsou v tomto poli uložená. Atributy se nastavují u jednotlivých polí a určují, jak se příslušné pole použije. Následující tabulky poskytují výčet typů a atributů, které můžete zadat.

### <a name="field-types"></a>Typy polí
| Typ | Popis |
| --- | --- |
| *Edm.String* |Text, který jde volitelně tokenizovat k fulltextovému hledání (dělení slov, rozklad atd). |
| *Collection(Edm.String)* |Seznam řetězců, které jde volitelně tokenizovat k fulltextovému hledání. Ačkoli neexistuje žádné teoretické omezení počtu položek v kolekci, na kolekce se vztahuje 16MB omezení velikosti datové části. |
| *Edm.Boolean* |Obsahuje hodnoty true nebo false. |
| *Edm.Int32* |32bitové celočíselné hodnoty. |
| *Edm.Int64* |64bitové celočíselné hodnoty. |
| *Edm.Double* |Číselné údaje s dvojitou přesností. |
| *Edm.DateTimeOffset* |Hodnoty data a času ve formátu OData V4 (například `yyyy-MM-ddTHH:mm:ss.fffZ` nebo `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Bod představující geografické umístění na zeměkouli. |

Podrobnější informace o [datových typech podporovaných službou Azure Search najdete tady](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="field-attributes"></a>Atributy polí
| Atribut | Popis |
| --- | --- |
| *Klíč* |Řetězec obsahující jedinečné ID jednotlivých dokumentů, které slouží k vyhledávání dokumentů. Každý index musí mít jeden klíč. Jenom jedno pole může být klíč a jeho typ musí být nastavený na Edm.String. |
| *Retrievable* |Určuje, jestli může být pole vrácené ve výsledku hledání. |
| *Filterable* |Umožňuje použít pole ve filtrovacích dotazech. |
| *Sortable* |Umožňuje dotazu seřadit výsledky hledání podle tohoto pole. |
| *Facetable* |Umožňuje použití pole ve struktuře [fasetové navigace](search-faceted-navigation.md) k filtrování, které je řízené samotným uživatelem. Jako fasety obvykle nejlépe fungují pole, která obsahují opakované hodnoty použitelné k seskupení více dokumentů (například více dokumentů, které spadají pod jednu značku nebo kategorii služeb). |
| *Searchable* |Označí pole jako fulltextově prohledávatelné. |

Podrobnější informace o [atributech indexu služby Azure Search najdete tady](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="guidance-for-defining-an-index-schema"></a>Pokyny pro definování schématu indexu
Při navrhování indexu si ve fázi plánování pečlivě promyslete každé rozhodnutí. Při navrhování indexu je důležité zohlednit uživatelskou práci při vyhledávání a potřeby podniku, protože každému poli se musí přiřadit [správné atributy](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Při změně indexu po nasazení je nutné znovu sestavit a načíst data.

Pokud se v průběhu času změní požadavky na úložiště dat, můžete zvýšit nebo snížit kapacitu přidáním nebo odebráním oddílů. Podrobnosti najdete v tématu [Správa služby Search v Azure](search-manage.md) nebo [Omezení služby](search-limits-quotas-capacity.md).

