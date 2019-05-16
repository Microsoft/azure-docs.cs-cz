---
title: Jak modelování komplexních datových typů – Azure Search
description: Vnořené nebo hierarchické datových struktur můžete modelovat v indexu Azure Search pomocí typu ComplexType a kolekce datových typů.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.custom: seodec2018
ms.openlocfilehash: 10400b0342fbe8667b22fea82c6446713d019e0d
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597332"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Jak modelování komplexních datových typů ve službě Azure Search

Externích datových sad použitých k naplnění indexu Azure Search někdy obsahovat hierarchické, nebo jsou vnořené používání dílčích struktur. Příklady můžou zahrnovat více umístění a telefonní čísla pro jednoho zákazníka, více barvy a velikosti pro jeden SKU, více autoři jednoho adresáře a tak dále. V modelovacích podmínky, může se zobrazit tyto struktury říká *komplexních datových typů*, *složené datové typy*, *složené datové typy*, nebo *agregace datové typy*. V, řečeno terminologií Azure Search komplexní typ je pole, které obsahuje podřízené položky (dílčích polí), která může být jednoduché nebo složité. Toto je podobný typu strukturovaných dat v programovacím jazyce. Komplexní pole může být jednoho pole, které představují jeden objekt v dokumentu nebo kolekci, která představuje pole objektů

Služba Azure Search nativně podporuje komplexní typy a kolekce. Společně tyto typy umožňují modelovat téměř jakékoli vnořené struktury JSON do indexu Azure Search. V předchozích verzích rozhraní API služby Azure Search sloučí pouze řádek, který nastaví nebylo možné importovat. V nejnovější verzi indexu můžete nyní lépe odpovídají zdrojová data. Jinými slovy Pokud se zdrojová data obsahuje komplexní typy, indexu může mít složité typy také.

Abyste mohli začít, doporučujeme, abyste [Hotels datovou sadu](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), které se dají načíst v **importovat data** Průvodce na webu Azure Portal. Průvodce zjistí komplexní typy ve zdroji a navrhne schéma indexu podle zjištěných struktury.

> [!Note]
> Podpora pro komplexní typy je všeobecně dostupná v `api-version=2019-05-06`. 
>
> Pokud vaše hledání řešení je vybudováno na starší řešení plochá datových sad v kolekci, měli byste změnit indexu na komplexní typy jako v nejnovější verzi rozhraní API nepodporuje. Další informace o upgradu verze rozhraní API najdete v tématu [upgradovat na nejnovější verzi rozhraní REST API](search-api-migration.md) nebo [upgradovat na nejnovější verzi sady .NET SDK](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Příklad složité struktury

Následující dokument JSON se skládá z jednoduchého polí a polí komplexní. Komplexní pole, jako například `Address` a `Rooms`, mají dílčí pole. `Address` má jednu sadu hodnot pro tyto dílčí pole, protože je jeden objekt v dokumentu. Naproti tomu `Rooms` má víc kopií hodnoty pro jeho dílčí pole, jeden pro každý objekt v kolekci.

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
      "BaseRate": 96.99,
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99,
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Vytváření složitých polí

Jako v jakékoli definice indexu můžete použít portál, [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/create-index), nebo [sady .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) k vytvoření schématu, která obsahuje komplexní typy. 

Následující příklad ukazuje schématu indexu JSON s jednoduchou pole, kolekcí a komplexní typy. Všimněte si, že v rámci komplexní typ, každý dílčí pole má typ, může mít atributy, stejně jako nejvyšší úrovně pole. Schéma odpovídá výše uvedeného příkladu s daty. `Address` je komplexní pole, která není kolekcí (hotelu má jednu adresu). `Rooms` je komplexní kolekce pole (hotelu má mnoho místnosti).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true  },
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

## <a name="updating-complex-fields"></a>Aktualizují se komplexní pole

Všechny [přeindexování pravidla](search-howto-reindex.md) , které se týkají pole obecně stále platí pro komplexní pole. Bylo nutné restartovat některé hlavní pravidel, přidání pole nevyžaduje, aby opětovné sestavení indexu, ale většina úpravy provést.

### <a name="structural-updates-to-the-definition"></a>Strukturální aktualizace definice

Můžete přidat nový dílčí pole komplexní pole kdykoli bez nutnosti opětovné sestavení indexu. Například "PSČ" přidání do `Address` nebo "Zařízení" k `Rooms` smí, stejně jako přidávání do indexu pole nejvyšší úrovně. Stávající dokumenty mít hodnotu null pro nové pole, dokud explicitně naplnění těchto polí stačí aktualizovat vaše data.

Všimněte si, že v rámci komplexní typ, každý dílčí pole má typ, může mít atributy, stejně jako nejvyšší úrovně pole

### <a name="data-updates"></a>Aktualizace dat

Aktualizace stávající dokumenty v indexu s nahrávání akcí funguje stejným způsobem pro komplexní a jednoduché pole – všechna pole jsou nahrazena. Ale sloučení (nebo mergeOrUpload při použití existujícího dokumentu) nebude fungovat stejně ve všech polích. Konkrétně sloučení nemá možnost sloučení elementů v rámci kolekce. To platí pro kolekce primitivních typů, jakož i komplexní kolekce. Aktualizujte kolekci, bude nutné k načtení hodnoty celé kolekce provedete změny a poté zahrnout do nové kolekce žádosti rozhraní API indexu.


## <a name="searching-complex-fields"></a>Vyhledávání složitých pole

Volného tvaru hledaných výrazů fungovat podle očekávání s komplexní typy. Pokud je vyhledáno všechny prohledávatelné pole nebo dílčí pole kamkoli v dokumentu, samotný dokument je shoda. 

Dotazy get další odlišování, pokud máte více podmínek a operátory a termíny, které se mají názvy zadané, jako je možné s [syntaxe Lucene](query-lucene-syntax.md). Například tento dotaz se pokusí o porovnání těchto dvou výrazů, "Portland" a "Nebo" proti dvě dílčí pole do pole adresy:

```json
search=Address/City:Portland AND Address/State:OR
```

Dotazy tímto způsobem jsou bez korelace nejsou pro fulltextové vyhledávání (na rozdíl od filtry, kde můžete zadávat dotazy na dílčí pole komplexní kolekci korelaci pomocí kteréhokoli nebo všech, stejně jako korelovaný poddotaz v SQL). To znamená, že výše uvedené Lucene dotaz by vrátil dokumenty, které obsahují "Portland, Maine" a ", Portland, Oregon" nebo jiné měst v Oregon. Je to proto, že každou klauzuli je porovnán všech hodnot v zadaném poli celý dokument, takže není pojem "aktuální dílčí dokumentu". 

 

## <a name="selecting-complex-fields"></a>Výběr komplexních polí

`$select` Parametr se používá k výběru, která pole jsou vráceny ve výsledcích hledání. Chcete-li tento parametr použijte k výběru konkrétního dílčího pole komplexní pole, patří nadřazené pole a dílčí oddělené lomítkem (`/`).

```json
$select=HotelName, Address/City, Rooms/BaseRate
```

Pole musí označená jako Retrievable v indexu, pokud chcete ve výsledcích hledání. Může být používáno pouze pole označené jako Retrievable `$select` příkazu. 


## <a name="filter-facet-and-sort-complex-fields"></a>Filtr, omezující vlastnosti a komplexní pole řazení

Stejné [syntaxe cesty OData](query-odata-filter-orderby-syntax.md) použít pro filtrování a fielded vyhledávání je také možné použít pro používání faset, řazení a vyberete pole v požadavku hledání. Pro komplexní typy platí pravidla, kterými se řídí dílčí pole, která může být označený jako sortable a facetable. 

### <a name="faceting-sub-fields"></a>Dílčí pole "faceting" 

Všechny dílčí pole může být označený jako facetable, pokud se nejedná o typ `Edm.GeographyPoint` nebo `Collection(Edm.GeographyPoint)`. 

Pokud pro struktuře Fasetové navigace se vrátí počet dokumentů, počty nejsou vzhledem k nadřazený dokument (hotelu), do vnořených dokumentů v kolekci komplexních (místnosti). Například předpokládejme, že má hotelu 20 místnosti typu "suite". Zadaný parametr tato omezující vlastnost `facet=Rooms/Type`, počet omezující vlastnost pro nadřazený dokument (hotely), který se není zprostředkující dílčí dokumentů (místnosti). 

### <a name="sorting-complex-fields"></a>Komplexní pole řazení

Operace řazení se nevztahuje na dokumenty (hotely) a není dílčí dokumenty (místnosti). Pokud máte kolekci komplexní typ, jako je například místnosti, je důležité si uvědomit, že nelze řadit v místnosti vůbec. Ve skutečnosti nelze řadit podle žádné kolekce. 

Operace řazení fungovat, pokud jsou pole jednohodnotové, zda jako jednoduchý pole nebo jako dílčí pole v komplexního typu. Například `$orderby=Address/ZipCode` komplexní typ lze seřadit, protože existuje pouze jedna poštovní směrovací číslo za hotelu. 

Bylo nutné restartovat pravidla ohledně řazení, v rámci pole indexu musí být označen jako Filterable a Sortable použije `$orderby` příkazu. 

## <a name="next-steps"></a>Další postup

 Zkuste [Hotels datovou sadu](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) v **importovat data** průvodce. Budete potřebovat informace o připojení služby Cosmos DB k dispozici v souboru readme pro přístup k datům. 
 
 Pomocí těchto informací v dolním je prvním krokem v průvodci k vytvoření nového zdroje dat služby Azure Cosmos DB. Další na v průvodci, při přechodu na cíl indexovou stránku, uvidíte indexu s komplexní typy. Vytvoření a načtení indexu a následné provádění dotazů pochopit novou strukturu.

> [!div class="nextstepaction"]
> [Rychlý start: Průvodce portálem pro import, indexování a dotazy](search-get-started-portal.md)