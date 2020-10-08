---
title: Modelování komplexních datových typů
titleSuffix: Azure Cognitive Search
description: Vnořené nebo hierarchické datové struktury je možné modelovat v indexu Azure Kognitivní hledání pomocí datových typů ComplexType a Collections.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: ee1c0957761fc1c8b9ca80477defae8cef044827
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824471"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Postup modelování komplexních datových typů v Azure Kognitivní hledání

Externí datové sady, které se používají k naplnění indexu služby Azure Kognitivní hledání, můžou být v mnoha tvarech. Někdy zahrnují hierarchické nebo vnořené podstruktury. Příklady mohou zahrnovat několik adres pro jednoho zákazníka, více barev a velikostí pro jednu SKU, více autorů jedné knihy a tak dále. V rámci modelovacích podmínek se tyto struktury můžou zobrazit jako *komplexní*, *složené*, *složené*nebo *agregované* datové typy. Pojem Azure Kognitivní hledání používá pro tento koncept je **komplexní typ**. V Azure Kognitivní hledání jsou komplexní typy modelovány pomocí **složitých polí**. Komplexní pole je pole, které obsahuje podřízené položky (podpole), které mohou být libovolného datového typu, včetně jiných složitých typů. Funguje podobným způsobem jako strukturované datové typy v programovacím jazyce.

Složitá pole zastupují buď jeden objekt v dokumentu, nebo pole objektů, v závislosti na datovém typu. Pole typu `Edm.ComplexType` reprezentují jednotlivé objekty, zatímco pole typu `Collection(Edm.ComplexType)` reprezentují pole objektů.

Azure Kognitivní hledání nativně podporuje komplexní typy a kolekce. Tyto typy umožňují modelovat skoro jakoukoli strukturu JSON v indexu služby Azure Kognitivní hledání. V předchozích verzích rozhraní API služby Azure Kognitivní hledání bylo možné importovat pouze sady plochých řádků. V nejnovější verzi může váš index nyní přesněji odpovídat zdrojovým datům. Jinými slovy, pokud zdrojová data mají komplexní typy, váš index může mít také komplexní typy.

Chcete-li začít, doporučujeme [sadu dat hotelů](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), kterou můžete načíst do průvodce **importem dat** v Azure Portal. Průvodce detekuje ve zdroji komplexní typy a navrhuje schéma indexu založené na zjištěných strukturách.

> [!Note]
> Podpora komplexních typů se stala všeobecně dostupnou od `api-version=2019-05-06` . 
>
> Pokud je vaše řešení hledání postavené na předchozích řešeních plochých datových sad v kolekci, měli byste index změnit tak, aby zahrnoval komplexní typy, které jsou podporované v nejnovější verzi rozhraní API. Další informace o upgradu verzí rozhraní API najdete v tématu [upgrade na nejnovější verzi REST API](search-api-migration.md) nebo [upgrade na nejnovější verzi sady .NET SDK](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Příklad komplexní struktury

Následující dokument JSON se skládá z jednoduchých polí a složitých polí. Složitá pole, například `Address` a `Rooms` , mají dílčí pole. `Address` má jednu sadu hodnot pro tyto dílčí pole, protože se jedná o jediný objekt v dokumentu. Naproti tomu `Rooms` má více sad hodnot pro své dílčí pole, jeden pro každý objekt v kolekci.


```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Tags": ["Free wifi", "on-site parking", "indoor pool", "continental breakfast"]
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "RoomNumber": 1105,
      "BaseRate": 96.99,
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99,
    }
    . . .
  ]
}
```

<název = "indexování – komplexní typy></a>

## <a name="indexing-complex-types"></a>Indexování složitých typů

Během indexování můžete mít ve všech komplexních kolekcích v rámci jednoho dokumentu maximálně 3000 prvků. Prvek komplexní kolekce je členem této kolekce, takže v případě místností (pouze komplexní kolekce v příkladu hotelu) je každá místnost prvkem. Pokud se v předchozím příkladu Motel "tajným bodem" mělo 500 místností, bude mít dokument z hotelu 500 prvky místnosti. U vnořených komplexních kolekcí je každý vnořený prvek také počítán Kromě vnějšího (nadřazeného) prvku.

Toto omezení platí pouze pro komplexní kolekce, nikoli pro komplexní typy (například adresy) nebo kolekce řetězců (například značky).

## <a name="creating-complex-fields"></a>Vytváření složitých polí

Stejně jako u libovolné definice indexu můžete použít portál, [REST API](/rest/api/searchservice/create-index)nebo [.NET SDK](/dotnet/api/microsoft.azure.search.models.index) k vytvoření schématu, které obsahuje komplexní typy. 

Následující příklad ukazuje schéma indexu JSON s jednoduchými poli, kolekcemi a komplexními typy. Všimněte si, že v rámci komplexního typu má každé dílčí pole typ a může mít atributy, stejně jako pole nejvyšší úrovně. Schéma odpovídá výše uvedeným ukázkovým datům. `Address` je komplexní pole, které není kolekcí (Hotel má jednu adresu). `Rooms` je komplexní pole kolekce (Hotel má mnoho místností).

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

Všechna [pravidla přeindexace](search-howto-reindex.md) , která platí pro pole obecně, se stále vztahují na složitá pole. Pokud je zde uvedeno několik hlavních pravidel, přidání pole do komplexního typu nevyžaduje opětovné sestavení indexu, ale většina úprav provádí.

### <a name="structural-updates-to-the-definition"></a>Strukturální aktualizace definice

Můžete kdykoli přidat nová dílčí pole do komplexního pole bez nutnosti opětovného sestavení indexu. Například přidávání "PSČ" do `Address` nebo "rekreačního" na `Rooms` je povoleno, stejně jako přidání pole nejvyšší úrovně do indexu. Existující dokumenty mají hodnotu null pro nová pole, dokud tato pole explicitně nenastavíte tak, že aktualizujete svá data.

Všimněte si, že v rámci komplexního typu má každé dílčí pole typ a může mít atributy, stejně jako pole nejvyšší úrovně.

### <a name="data-updates"></a>Aktualizace dat

Aktualizace existujících dokumentů v indexu pomocí `upload` Akce funguje stejně jako u složitých a jednoduchých polí – všechna pole se nahrazují. Nicméně `merge` (nebo `mergeOrUpload` při použití pro existující dokument) nefungují stejně jako ve všech polích. Konkrétně `merge` nepodporuje sloučení prvků v rámci kolekce. Toto omezení existuje pro kolekce primitivních typů a komplexních kolekcí. Chcete-li aktualizovat kolekci, je nutné načíst úplnou hodnotu kolekce, provést změny a pak zahrnout novou kolekci do požadavku rozhraní API pro index.

## <a name="searching-complex-fields"></a>Hledání složitých polí

Výrazy hledání volných formulářů fungují podle očekávání u složitých typů. Pokud se v dokumentu shodují libovolné prohledávatelné pole nebo dílčí pole, pak se jedná o shodu samotného dokumentu.

Dotazy dostanou více odlišit, když máte více podmínek a operátorů, přičemž některé výrazy mají zadané názvy polí, jak je to možné v [syntaxi Lucene](query-lucene-syntax.md). Tento dotaz se například pokusí porovnat dvě podklíče pole adresa, "Portland" a "nebo".

> `search=Address/City:Portland AND Address/State:OR`

Podobné dotazy nejsou pro fulltextové vyhledávání na rozdíl od filtrů *nekorelační* . Ve filtrech jsou dotazy nad dílčími poli komplexní kolekce korelační pomocí proměnných rozsahu v [ `any` nebo `all` ](search-query-odata-collection-operators.md). Výše uvedený dotaz Lucene vrátí dokumenty, které obsahují "Portland, Maine" a "Portlandd, Brno", spolu s ostatními městy v Brno. K tomu dochází, protože každá klauzule se vztahuje na všechny hodnoty svého pole v celém dokumentu, takže neexistuje koncept "aktuálního dílčího dokumentu". Další informace najdete v tématu [porozumění filtrům kolekce OData v Azure kognitivní hledání](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Výběr komplexních polí

`$select`Parametr slouží k výběru polí, která se vrátí ve výsledcích hledání. Chcete-li použít tento parametr pro výběr konkrétní dílčí pole složeného pole, zahrňte nadřazené pole a dílčí pole oddělené lomítkem ( `/` ).

> `$select=HotelName, Address/City, Rooms/BaseRate`

Pole musí být označena jako zavedená v indexu, pokud je chcete ve výsledcích hledání. V příkazu lze použít pouze pole, která jsou označena jako schopná `$select` .

## <a name="filter-facet-and-sort-complex-fields"></a>Filtrování, omezující vlastnosti a řazení složitých polí

Stejnou [syntaxi cesty OData](query-odata-filter-orderby-syntax.md) použitou pro filtrování a hledání v terénu lze také použít pro omezující vlastnosti, řazení a výběr polí v žádosti o vyhledávání. U komplexních typů platí pravidla, která určují, která dílčí pole mohou být označena jako řaditelné nebo plošky. Další informace o těchto pravidlech najdete v referenčních informacích k [rozhraní API pro vytvoření indexu](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Podpole omezující vlastnosti

Jakékoli dílčí pole může být označeno jako ploška, pokud není typu `Edm.GeographyPoint` nebo `Collection(Edm.GeographyPoint)` .

Počty dokumentů vracené ve výsledcích omezující vlastnosti jsou vypočítány pro nadřazený dokument (Hotel), nikoli pro dílčí dokumenty ve složité kolekci (místnosti). Předpokládejme například, že Hotel má 20 místností typu "Suite". Pro tento parametr omezující vlastnosti `facet=Rooms/Type` bude počet omezující vlastnosti jeden pro Hotel, ne 20 pro místnosti.

### <a name="sorting-complex-fields"></a>Řazení složitých polí

Operace řazení se vztahují na dokumenty (hotely) a ne na poddokumenty (místnosti). Pokud máte komplexní kolekci typů, například místnosti, je důležité si uvědomit, že už nemůžete řadit na místnosti. Ve skutečnosti nemůžete řadit podle žádné kolekce.

Operace řazení fungují, pokud pole mají jednu hodnotu v jednom dokumentu, ať už je to jednoduché pole, nebo dílčí pole komplexního typu. Například `Address/City` je povoleno seřaditelné, protože existuje pouze jedna adresa pro každý Hotel, takže `$orderby=Address/City` bude setříděno hotely podle města.

### <a name="filtering-on-complex-fields"></a>Filtrování u složitých polí

Můžete se podívat na dílčí pole komplexního pole ve výrazu filtru. Stačí použít stejnou [syntaxi cesty OData](query-odata-filter-orderby-syntax.md) , která se používá pro omezující vlastnosti, řazení a výběr polí. Například následující filtr vrátí všechny hotely v Kanadě:

> `$filter=Address/Country eq 'Canada'`

Chcete-li filtrovat podle komplexního pole kolekce, můžete použít **výraz lambda** s [ `any` `all` operátory a](search-query-odata-collection-operators.md). V takovém případě **Proměnná rozsahu** výrazu lambda je objekt s podoblastmi. Můžete se podívat na tato dílčí pole se standardní syntaxí cesty OData. Například následující filtr vrátí všechny hotely s aspoň jednou a všemi místnostmi, které nepatří ke kouření:

> `$filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)`

Stejně jako u jednoduchých polí nejvyšší úrovně lze jednoduchá dílčí pole komplexních polí zahrnout pouze do filtrů, pokud mají atribut **Filtered** nastaven na hodnotu `true` v definici indexu. Další informace najdete v referenčních informacích k [rozhraní API pro vytvoření indexu](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Další kroky

V průvodci **importem dat** zkuste [nastavit data hotelů](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) . Pro přístup k datům budete potřebovat informace o Cosmos DB připojení, které jsou k dispozici v souboru Readme.

Díky těmto informacím je prvním krokem v Průvodci vytvořením nového zdroje dat Azure Cosmos DB. V průvodci se po zobrazení stránky cílový index zobrazí rejstřík se složitými typy. Vytvořte a načtěte tento index a pak spusťte dotazy, abyste pochopili novou strukturu.

> [!div class="nextstepaction"]
> [Rychlý Start: Průvodce portálem pro import, indexování a dotazy](search-get-started-portal.md)
