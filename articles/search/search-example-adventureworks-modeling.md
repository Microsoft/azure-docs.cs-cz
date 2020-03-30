---
title: 'Příklad: Modeldatabáze AdventureWorks Inventory'
titleSuffix: Azure Cognitive Search
description: Naučte se modelovat relační data, transformovat je do sloučící datové sady, pro indexování a fulltextové vyhledávání v Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792994"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Příklad: Modelujte databázi adventureworks inventáře pro Azure Cognitive Search

Azure Cognitive Search přijímá najednocující sadu řádků jako vstupy do [kanálu indexování (ingestování dat).](search-what-is-an-index.md) Pokud zdrojová data pocházejí z relační databáze serveru SQL Server, tento článek ukazuje jeden přístup pro vytvoření najedné najedné jedné sady řádků před indexováním, pomocí AdventureWorks ukázkové databáze jako příklad.

## <a name="about-adventureworks"></a>O společnosti AdventureWorks

Pokud máte instanci serveru SQL Server, můžete být obeznámeni s [ukázkovou databází AdventureWorks](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). Mezi tabulkami zahrnutými v této databázi je pět tabulek, které zveřejňují informace o produktu.

+ **ProductModel**: název
+ **Produkt:** název, barva, náklady, velikost, hmotnost, obrázek, kategorie (každý řádek se připojí k určitému ProductModel)
+ **Popis produktu**: popis
+ **ProductModelProductDescription**: národní prostředí (každý řádek spojuje ProductModel na konkrétní ProductDescription pro konkrétní jazyk)
+ **ProductCategory**: název, nadřazená kategorie

Kombinace všech těchto dat do naložené sady řádků, které lze ingestovat do indexu vyhledávání je cílem tohoto příkladu. 

## <a name="considering-our-options"></a>Vzhledem k našim možnostem

Naivní přístup by bylo indexovat všechny řádky z tabulky Product (spojené, kde je to vhodné), protože tabulka Product obsahuje nejkonkrétnější informace. Tento přístup by však vystavit index vyhledávání vnímané duplikáty v resultset. Například model Road-650 je k dispozici ve dvou barvách a šesti velikostech. Dotazu na "silniční kola" by pak dominovalo dvanáct instancí stejného modelu, které se liší pouze velikostí a barvou. Zbylých šest modelů specifických pro silnice by bylo odsunuto do nejsvětí: strana dvě.

  ![Seznam produktů](./media/search-example-adventureworks/products-list.png "Seznam produktů")
 
Všimněte si, že model Road-650 má dvanáct možností. Řádky entit 1:N jsou nejlépe reprezentovány jako pole s více hodnotami nebo pole s předem agregotou hodnotou v indexu vyhledávání.

Řešení tohoto problému není tak jednoduché jako přesunutí cílového indexu do tabulky ProductModel. Tím by se ignorovat důležitá data v tabulce produkt, které by měly být stále zastoupeny ve výsledcích hledání.

## <a name="use-a-collection-data-type"></a>Použití datového typu kolekce

"Správný přístup" je využít funkce schéma hledání, která nemá přímou paralelu v modelu databáze: **Collection(Edm.String)**. Tato konstrukce je definována ve schématu indexu Azure Cognitive Search. Datový typ kolekce se používá, když potřebujete reprezentovat seznam jednotlivých řetězců, nikoli velmi dlouhý (jeden) řetězec. Pokud máte značky nebo klíčová slova, použijete pro toto pole datový typ Kolekce.

Definováním polí indexu s více hodnotami **kolekce (Edm.String)** pro "barvu", "velikost" a "obrázek" jsou doplňkové informace zachovány pro fazetování a filtrování bez znečištění indexu duplicitními položkami. Podobně použijte agregační funkce na číselná pole produktu, indexování **minListPrice** namísto každého **jednotlivého seznamu produktůCena**.

Vzhledem k tomu, index s těmito strukturami, hledání "horská kola" by se zobrazí diskrétní modely jízdních kol při zachování důležitých metadat, jako je barva, velikost a nejnižší cena. Následující snímek obrazovky poskytuje ilustraci.

  ![Příklad vyhledávání horských kol](./media/search-example-adventureworks/mountain-bikes-visual.png "Příklad vyhledávání horských kol")

## <a name="use-script-for-data-manipulation"></a>Použití skriptu pro manipulaci s daty

Bohužel tento typ modelování nelze snadno dosáhnout prostřednictvím příkazy SQL sám. Místo toho použijte jednoduchý skript NodeJS k načtení dat a namapujte je na entity JSON vhodné pro vyhledávání.

Konečné mapování hledání databáze vypadá takto:

+ model (Edm.String: prohledávatelný, filtrovatelný, retrievable) z "ProductModel.Name"
+ description_en (Edm.String: prohledávatelné) z "ProductDescription" pro model, kde culture ='en'
+ color (Collection(Edm.String): prohledávatelné, filtrovatelné, facetable, retrievable): jedinečné hodnoty z "Product.Color" pro model
+ velikost (Collection(Edm.String): prohledávatelné, filtrovatelné, facetable, retrievable): jedinečné hodnoty z "Product.Size" pro model
+ obrázek (Collection(Edm.String): retrievable): jedinečné hodnoty z "Product.ThumbnailPhoto" pro model
+ minStandardCost (Edm.Double: filtrovatelný, facetable, seřaditelný, retrievable): agregované minimum všech "Product.StandardCost" pro model
+ minListPrice (Edm.Double: filtrovatelný, facetable, seřaditelný, retrievable): agregované minimum všech "Product.ListPrice" pro model
+ minWeight (Edm.Double: filtrovatelný, facetable, seřaditelný, retrievable): agregované minimum všech "Product.Weight" pro model
+ produkty (Collection(Edm.String): prohledávatelné, filtrovatelné, retrievable): jedinečné hodnoty z "Product.Name" pro model

Po spojení ProductModel tabulka s ProductA a ProductDescription, použijte [lodash](https://lodash.com/) (nebo Linq v C#) rychle transformovat resultset:

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

Výsledný JSON vypadá takto:

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

Nakonec je zde dotaz SQL vrátit počáteční sadu záznamů. Použil jsem [modul mssql](https://www.npmjs.com/package/mssql) npm k načtení dat do aplikace NodeJS.

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Příklad: Víceúrovňové komodové taxonomie v Azure Cognitive Search](search-example-adventureworks-multilevel-faceting.md)