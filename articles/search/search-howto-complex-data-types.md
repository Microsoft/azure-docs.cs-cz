---
title: Jak modelovat složité datové typy
titleSuffix: Azure Cognitive Search
description: Vnořené nebo hierarchické datové struktury lze modelovat v indexu Azure Cognitive Search pomocí datových typů ComplexType a Collections.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283053"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Jak modelovat složité datové typy v Azure Cognitive Search

Externí datové sady používané k naplnění indexu Azure Cognitive Search se můžou datovat v mnoha obrazcích. Někdy obsahují hierarchické nebo vnořené podstruktury. Příklady mohou zahrnovat více adres pro jednoho zákazníka, více barev a velikostí pro jednu skladovou položku, více autorů jedné knihy a tak dále. V modelovacích termínech se mohou tyto struktury označovat jako *složité*, *složené*, *složené*nebo *agregované* datové typy. Termín Azure Cognitive Search používá pro tento koncept je **komplexní typ**. V Azure Cognitive Search jsou složité typy modelovány pomocí **složitých polí**. Komplexní pole je pole, které obsahuje podřízené položky (dílčí pole), které může být libovolného datového typu, včetně jiných komplexních typů. To funguje podobným způsobem jako strukturované datové typy v programovacím jazyce.

Komplexní pole představují buď jeden objekt v dokumentu, nebo pole objektů v závislosti na datovém typu. Pole typu `Edm.ComplexType` představují jednotlivé objekty, `Collection(Edm.ComplexType)` zatímco pole typu představují pole objektů.

Azure Cognitive Search nativně podporuje složité typy a kolekce. Tyto typy umožňují modelovat téměř všechny struktury JSON v indexu Azure Cognitive Search. V předchozích verzích azure cognitive search API lze importovat jenom sloučí cítění řádků. V nejnovější verzi může nyní index více odpovídat zdrojovým datům. Jinými slovy, pokud zdrojová data má složité typy, index může mít také složité typy.

Chcete-li začít, doporučujeme [datovou sadu Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), kterou můžete načíst v průvodci **importem dat** na webu Azure Portal. Průvodce zjistí složité typy ve zdroji a navrhne schéma indexu na základě zjištěných struktur.

> [!Note]
> Podpora pro složité typy `api-version=2019-05-06`je obecně k dispozici v . 
>
> Pokud je vaše vyhledávací řešení postaveno na dřívějších zástupných řešeních sloučících datových sad v kolekci, měli byste změnit index tak, aby zahrnoval složité typy podporované v nejnovější verzi rozhraní API. Další informace o inovaci verzí rozhraní API naleznete v [tématu Upgrade na nejnovější verzi rozhraní REST API](search-api-migration.md) nebo Upgrade na nejnovější verzi sady [.NET SDK](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Příklad složité struktury

Následující dokument JSON se skládá z jednoduchých polí a složitých polí. Komplexní pole, `Address` například a `Rooms`, mají dílčí pole. `Address`má jednu sadu hodnot pro tato dílčí pole, protože se jedná o jeden objekt v dokumentu. Naproti tomu `Rooms` má více sad hodnot pro jeho dílčí pole, jeden pro každý objekt v kolekci.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Vytváření složitých polí

Stejně jako u jakékoli definice indexu můžete pomocí portálu, [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)nebo sady [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) vytvořit schéma, které obsahuje složité typy. 

Následující příklad ukazuje schéma indexu JSON s jednoduchými poli, kolekcemi a složitými typy. Všimněte si, že v rámci komplexního typu má každé dílčí pole typ a může mít atributy, stejně jako pole nejvyšší úrovně. Schéma odpovídá výše uvedené ukázková data. `Address`je komplexní pole, které není sbírkou (hotel má jednu adresu). `Rooms`je komplexní sběrné pole (hotel má mnoho pokojů).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Aktualizace složitých polí

Všechna [pravidla přeindexování,](search-howto-reindex.md) která platí pro pole obecně, stále platí pro složitá pole. Opětovné uvedení několika hlavních pravidel zde přidání pole nevyžaduje opětovné sestavení indexu, ale většina úprav.

### <a name="structural-updates-to-the-definition"></a>Strukturální aktualizace definice

Nová dílčí pole můžete do komplexního pole kdykoli přidat bez nutnosti opětovného sestavení indexu. Například přidání "PSČ" `Address` do nebo "Vybavení" je `Rooms` povoleno, stejně jako přidání pole nejvyšší úrovně do indexu. Existující dokumenty mají nulovou hodnotu pro nová pole, dokud explicitně nenaplníte tato pole aktualizací dat.

Všimněte si, že v rámci komplexního typu má každé dílčí pole typ a může mít atributy, stejně jako pole nejvyšší úrovně

### <a name="data-updates"></a>Aktualizace dat

Aktualizace existujících dokumentů v `upload` indexu s akcí funguje stejným způsobem pro složitá a jednoduchá pole – všechna pole jsou nahrazena. Však `merge` (nebo `mergeOrUpload` při použití na existující dokument) nefunguje stejně ve všech polích. Konkrétně `merge` nepodporuje slučování prvků v rámci kolekce. Toto omezení existuje pro kolekce primitivních typů a komplexní kolekce. Chcete-li aktualizovat kolekci, budete muset načíst úplnou hodnotu kolekce, provést změny a pak zahrnout novou kolekci do požadavku rozhraní API indexu.

## <a name="searching-complex-fields"></a>Hledání složitých polí

Volné vyhledávací výrazy fungují podle očekávání u složitých typů. Pokud se jakékoli prohledávatelné pole nebo dílčí pole kdekoli v dokumentu shoduje, pak se dokument sám shoduje.

Dotazy získat více nuanced, pokud máte více termínů a operátorů a některé termíny mají názvy polí zadané, jak je to možné s [lucene syntaxe](query-lucene-syntax.md). Tento dotaz se například pokusí porovnat dva termíny, "Portland" a "OR", se dvěma dílčími poli pole Adresa:

    search=Address/City:Portland AND Address/State:OR

Dotazy, jako je *tento, nejsou korelovány* pro fulltextové vyhledávání, na rozdíl od filtrů. Ve filtrech jsou dotazy na dílčí pole komplexní kolekce korelovány pomocí proměnných rozsahu v [ `any` nebo `all` ](search-query-odata-collection-operators.md). Výše uvedený dotaz Lucene vrátí dokumenty obsahující jak "Portland, Maine" a "Portland, Oregon", spolu s dalšími městy v Oregonu. K tomu dochází, protože každá klauzule platí pro všechny hodnoty svého pole v celém dokumentu, takže neexistuje žádný koncept "aktuální ho poddokumentu". Další informace o tomto [tématu naleznete v tématu Principy filtrů kolekce OData v Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Výběr složitých polí

Parametr `$select` slouží k výběru polí, která budou vrácena ve výsledcích hledání. Chcete-li tento parametr použít k výběru určitých dílčích polí komplexního pole,`/`zahrňte nadřazené pole a dílčí pole oddělené lomítkem ( ).

    $select=HotelName, Address/City, Rooms/BaseRate

Pole musí být označena jako retrievable v indexu, pokud je chcete ve výsledcích hledání. V příkazu `$select` lze použít pouze pole označená jako Retrievable.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtrování, omezující vlastnost a řazení složitých polí

Stejnou [syntaxi cesty OData použitou](query-odata-filter-orderby-syntax.md) pro filtrování a vyhledávání v poli lze použít také pro fazetování, řazení a výběr polí v požadavku hledání. Pro komplexní typy platí pravidla, která upravují, která dílčí pole mohou být označena jako seřaditelná nebo stůl. Další informace o těchto pravidlech naleznete v [odkazu vytvořit rozhraní API indexu](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Podpole omezující fazety

Jakékoli dílčí pole může být označeno jako `Edm.GeographyPoint` facetable, pokud není typu nebo `Collection(Edm.GeographyPoint)`.

Počty dokumentů vrácených ve výsledcích omezující vlastnosti se počítají pro nadřazený dokument (hotel), nikoli pro dílčí dokumenty ve složité kolekci (místnostech). Předpokládejme například, že hotel má 20 pokojů typu "suite". Vzhledem k `facet=Rooms/Type`tomuto parametru omezující chod bude počet omezujících_ jeten jeden pro hotel, nikoli 20 pro pokoje.

### <a name="sorting-complex-fields"></a>Řazení složitých polí

Operace řazení se vztahují na dokumenty (hotely) a nikoli na dílčí dokumenty (místnosti). Pokud máte komplexní kolekci typů, jako je Rooms, je důležité si uvědomit, že v roomsu nelze seřadit vůbec. Ve skutečnosti, nemůžete třídit na žádné sbírky.

Operace řazení fungují, pokud mají pole jednu hodnotu pro každý doklad, zda je toto pole jednoduché pole, nebo dílčí pole ve složitém typu. Například `Address/City` je povoleno řazení, protože na hotel existuje pouze `$orderby=Address/City` jedna adresa, takže budou třídit hotely podle města.

### <a name="filtering-on-complex-fields"></a>Filtrování složitých polí

Ve výrazu filtru můžete odkazovat na dílčí pole komplexního pole. Stačí použít stejnou [syntaxi cesty OData,](query-odata-filter-orderby-syntax.md) která se používá pro fazetování, řazení a výběr polí. Například následující filtr vrátí všechny hotely v Kanadě:

    $filter=Address/Country eq 'Canada'

Chcete-li filtrovat na komplexní sběrné pole, můžete použít **výraz lambda** s [ `any` `all` operátory a](search-query-odata-collection-operators.md). V takovém případě je **proměnná rozsahu** výrazu lambda objektem s dílčími poli. Na tato dílčí pole můžete odkazovat se standardní syntaxí cesty OData. Například následující filtr vrátí všechny hotely s alespoň jedním pokojem deluxe a všemi nekuřáckými pokoji:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Stejně jako u jednoduchých polí nejvyšší úrovně mohou být jednoduchá dílčí pole složitých polí `true` zahrnuta do filtrů pouze v případě, že mají **filtrovatelný** atribut nastavený v definici indexu. Další informace naleznete v [odkazu Vytvořit rozhraní API indexu](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Další kroky

Vyzkoušejte [sadu dat hotely](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) v Průvodci **importem dat.** Budete potřebovat informace o připojení Cosmos DB uvedené v readme pro přístup k datům.

S těmito informacemi v ruce, prvním krokem v průvodci je vytvořit nový zdroj dat Azure Cosmos DB. Dále v průvodci, když se dostanete na stránku cílového indexu, uvidíte index se složitými typy. Vytvořte a načtěte tento index a potom spouštějte dotazy, abyste pochopili novou strukturu.

> [!div class="nextstepaction"]
> [Úvodní příručka: Průvodce portálem pro import, indexování a dotazy](search-get-started-portal.md)
