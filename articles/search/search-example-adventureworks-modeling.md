---
title: 'Příklad: Model databáze AdventureWorks inventáře – Azure Search'
description: Informace o modelování relačních dat, transformují se do ploché sady dat, pro indexování a úplné vyhledávání ve službě Azure Search.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 6d5d01dfbbcfda56818f5c38b06117a87e021445
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174563"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Příklad: Model databáze AdventureWorks inventáře pro službu Azure Search

Modelování strukturovaných databáze obsahu do indexu search efektivnější je zřídka jednoduché cvičení. Plánování a správa změn jste si poznamenali, existuje odvrácená denormalizing zdrojové řádky od jejich stavu připojených k tabulce do podporou vyhledávání entit. Tento článek používá AdventureWorks ukázková data, k dispozici online, abyste měli na očích běžné možnosti při přechodu z databáze pro hledání. 

## <a name="about-adventureworks"></a>O AdventureWorks

Pokud máte instanci systému SQL Server, může být obeznámeni s ukázkovou databází AdventureWorks. Mezi tabulkami, které jsou zahrnuté v této databázi jsou pět tabulek, které poskytují informace o produktu.

+ **ProductModel**: název
+ **Produkt**: název, barvy, náklady, velikost, váha, image, kategorie (každý řádek se připojí k určité ProductModel)
+ **ProductDescription**: popis
+ **ProductModelProductDescription**: národní prostředí (každý řádek spojení ProductModel na konkrétní ProductDescription pro konkrétní jazyk)
+ **ProductCategory**: název, nadřazené kategorie

Kombinování všechna tato data do ploché sady řádků, které je možné ingestovat v indexu vyhledávání je daný úkol. 

## <a name="considering-our-options"></a>Vzhledem k tomu, naše možnosti

Naivní přístup by k indexování všechny řádky z tabulky Product (připojené, kde je to vhodné) od do tabulky Product má nejvíce specifické informace. Tento přístup však by vystavovat indexu vyhledávání vnímaná pouze objekt třídy resultset. Například je k dispozici ve dvou barvy a velikosti šest silniční 650 modelu. Dotaz "silniční kola" by poté dominovaly dvanáct výskyty stejného modelu rozlišené pouze pomocí velikostí a barvou. Dalších šesti silniční specifické modely by všechny možné předané centrům nether řadě vyhledávání: dvě stránky.

  ![Seznam produktů](./media/search-example-adventureworks/products-list.png "seznam produktů")
 
Všimněte si, že model silniční 650 má dvanáct možnosti. Jeden na mnoho entit řádky jsou nejlépe reprezentovány ve formě pole s více hodnotami nebo předprodukčním aggregated hodnotu pole v indexu vyhledávání.

Řešení tohoto problému je stejně jednoduché jako přesunutí cílový index do tabulky ProductModel velká a malá písmena. Mohlo by ignorovat důležitá data v tabulce produktu, které by měly být zastoupeny pořád ve výsledcích hledání.

## <a name="use-a-collection-data-type"></a>Použijte datový typ kolekce

"Správný přístup" je využívat funkce schéma vyhledávání, která nemá přímé paralelní databázový model: **Collection(EDM.String)**. Datový typ kolekce se používá v případě, že máte seznam jednotlivých řetězců, spíše než velmi dlouhý řetězec (jeden). Pokud už máte značky nebo klíčová slova, použijete datový typ kolekce pro toto pole.

Definováním více hodnotami indexu pole **Collection(Edm.String)** "color", "velikost" a "image" pomocné informace jsou zachované pro "faceting" a filtrování bez zahlcení index s duplicitní položky. Podobně platí agregační funkce pro číselná pole produktu indexování **minListPrice** namísto každých jednoho produktu **listPrice**.

Zadaný index s těchto struktur, vyhledejte "Horská kola" by zobrazil diskrétní jízdních kol modely, při zachování důležitá metadata, jako je barva, velikost a nejnižší cenu. Na následujícím snímku obrazovky je uvedena ukázka.

  ![Příklad prohledávání kolo Horská oblast](./media/search-example-adventureworks/mountain-bikes-visual.png "příklad prohledávání kolo Horská oblast")

## <a name="use-script-for-data-manipulation"></a>Pomocí skriptu pro manipulaci s daty

Bohužel tento typ modelování nelze snadno uskutečnit pomocí příkazů jazyka SQL samostatně. Místo toho použijte jednoduchý skript NodeJS k načtení dat a jejich mapování na hledání vhodných JSON entit.

Mapování konečné databázové hledání vypadá takto:

+ Model (Edm.String: prohledávatelné, filtrovatelné, retrievable) z "ProductModel.Name"
+ description_en (Edm.String: prohledávatelné) z "ProductDescription" pro model kde jazyková verze = "en"
+ Barva (Collection(Edm.String): prohledávatelné, filtrovatelné, facetable, retrievable): jedinečné hodnoty z "Product.Color" pro model
+ velikost (Collection(Edm.String): prohledávatelné, filtrovatelné, facetable, retrievable): jedinečné hodnoty z "Product.Size" pro model
+ bitové kopie (Collection(Edm.String): retrievable): jedinečné hodnoty z "Product.ThumbnailPhoto" pro model
+ minStandardCost (Edm.Double: filtrovatelné, facetable, řazení, retrievable): agregační minimum všech "Product.StandardCost" pro model
+ minListPrice (Edm.Double: filtrovatelné, facetable, řazení, retrievable): agregační minimum všech "Product.ListPrice" pro model
+ minWeight (Edm.Double: filtrovatelné, facetable, řazení, retrievable): agregační minimum všech "Product.Weight" pro model
+ produkty (Collection(Edm.String): prohledávatelné, filtrovatelné, retrievable): jedinečné hodnoty z "Product.Name" pro model

Po připojení k tabulce ProductModel s používáním produktu a ProductDescription, [lodash](https://lodash.com/) (nebo LINQ v jazyku C#) pro rychlou transformaci výsledků:

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

Výsledný kód JSON vypadá takto:

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

A konečně tady je dotaz SQL, který vrací počáteční sada záznamů. Můžu použít [mssql](https://www.npmjs.com/package/mssql) modul npm k načtení dat do aplikace NodeJS.

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Příklad: Víceúrovňové omezující vlastnosti taxonomií ve službě Azure Search](search-example-adventureworks-multilevel-faceting.md)


