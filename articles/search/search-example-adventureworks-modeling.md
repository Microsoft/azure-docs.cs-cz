---
title: 'Příklad: Modelování databáze inventáře AdventureWorks – Azure Search'
description: Naučte se modelovat relační data, transformovat je na sloučenou datovou sadu pro indexování a fulltextové vyhledávání v Azure Search.
author: cstone
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 52ccf3edfca5b3481b038bd5d3449c1dd6354179
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649921"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Příklad: Modelování databáze inventáře AdventureWorks pro Azure Search

Modelování obsahu strukturované databáze do efektivního indexu hledání je výjimečným cvičením. Pro plánování a změnu v rámci správy existuje výzva k denormalizaci zdrojových řádků z jejich stavu připojeného k tabulkám na uživatelsky přívětivé entity pro vyhledávání. Tento článek používá ukázková data společnosti AdventureWorks, která jsou k dispozici online, k zdůraznění běžných zkušeností v přechodu z databáze k hledání. 

## <a name="about-adventureworks"></a>O AdventureWorks

Pokud máte instanci SQL Server, možná budete obeznámeni s ukázkovou databází AdventureWorks. Mezi tabulkami, které jsou součástí této databáze, patří pět tabulek, které zveřejňují informace o produktu.

+ **ProductModel**: název
+ **Produkt**: název, barva, náklady, velikost, váha, obrázek, kategorie (každý řádek se připojí ke konkrétnímu ProductModel)
+ **ProductDescription**: Popis
+ **ProductModelProductDescription**: locale (každý řádek spojuje ProductModel ke konkrétnímu jazyku ProductDescription pro určitý jazyk)
+ **ProductCategory**: název, Nadřazená kategorie

Kombinování všech těchto dat do ploché sady řádků, které lze ingestovat do indexu vyhledávání, je úkol na ruce. 

## <a name="considering-our-options"></a>Zvážení našich možností

Přístup Naive by měl naindexovat všechny řádky z tabulky produktů (tam, kde je to vhodné), protože tabulka produktu má nejvíce specifické informace. Tento přístup však vystaví index vyhledávání pro zjištěné duplicity v sadě výsledků. Model silničního 650 je například k dispozici ve dvou barvách a šesti velikostech. Dotaz pro "silniční kola" by pak byl podléhat dvanáct instancím stejného modelu, odlišeny pouze podle velikosti a barvy. Ostatní šest modelů, které jsou specifické pro silnici, by se relegated na netherou svět hledání: Druhá stránka.

  ![Seznam produktů](./media/search-example-adventureworks/products-list.png "Seznam produktů")
 
Všimněte si, že model Road-650 má dvanáct možností. Řádky entity 1: n se nejlépe prezentují jako pole s více hodnotami nebo předem agregovaná pole v indexu vyhledávání.

Vyřešení tohoto problému není tak jednoduché jako přesunutí cílového indexu do tabulky ProductModel. Tím by došlo k ignorování důležitých dat v tabulce produktů, která by měla být ve výsledcích hledání stále zastoupena.

## <a name="use-a-collection-data-type"></a>Použití datového typu kolekce

Správný přístup je využití funkce vyhledávacího schématu, která nemá přímý paralelní v modelu databáze: **Collection (EDM. String)** . Datový typ kolekce se používá, pokud máte seznam jednotlivých řetězců, nikoli velmi dlouhý (jeden) řetězec. Pokud máte značky nebo klíčová slova, měli byste pro toto pole použít datový typ kolekce.

Při definování polí indexu s více hodnotami **kolekce (EDM. String)** pro "Color", "size" a "image" jsou pomocné informace uchovány pro omezující vlastnosti a pro filtrování bez zneznečištění indexu s duplicitními položkami. Podobně použijte agregační funkce na pole číselného produktu a indexování **minListPrice** místo každého jednotlivého produktu **listPrice**.

Hledání "horských kol" podle indexu s těmito strukturami by zobrazilo diskrétní modely jízdních kol a zároveň zachovává důležitá metadata, jako je barva, velikost a nejnižší cena. Následující snímek obrazovky nabízí ilustraci.

  ![Příklad hledání horských kol](./media/search-example-adventureworks/mountain-bikes-visual.png "Příklad hledání horských kol")

## <a name="use-script-for-data-manipulation"></a>Použití skriptu pro manipulaci s daty

Tento typ modelování bohužel nelze snadno dosáhnout pouze pomocí příkazů SQL. Místo toho načtěte data pomocí jednoduchého NodeJS skriptu a pak ho namapujte na uživatelsky přívětivé entity JSON.

Konečné mapování hledání databáze vypadá takto:

+ Model (EDM. String: prohledávatelné, filtrovatelné, lze získat) z "ProductModel.Name"
+ description_en (EDM. String: prohledávatelný) z "ProductDescription" pro model, kde culture = ' en '
+ Color (Collection (EDM. String): prohledávatelné, filtrovatelné, plošky, získatelné): jedinečné hodnoty z "Product. Color" pro model
+ Size (Collection (EDM. String): prohledávatelné, filtrovatelné, plošky, získatelné): jedinečné hodnoty z "Product. Size" pro model
+ Image (Collection (EDM. String): disThumbnailPhoto): jedinečné hodnoty z "Product." pro model
+ minStandardCost (EDM. Double: Filtered, Faced,,): Aggregate minima ze všech hodnot "Product. StandardCost" pro model
+ minListPrice (EDM. Double: Filtered, Faced,,): agregované minimum všech hodnot "produkt. ListPrice" pro model
+ minWeight (EDM. Double: Filtered, Faced,,): Aggregate minima ze všech "produktu. váhy" pro model
+ Products (Collection (EDM. String): můžete prohledávatelné, filtrovatelné, získatelné): jedinečné hodnoty z "Product.Name" pro model

Po spojení tabulky ProductModel s produktem a ProductDescription použijte [lodash](https://lodash.com/) (nebo LINQ v C#) k rychlé transformaci sady výsledků:

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

Výsledný formát JSON vypadá takto:

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

Nakonec tady je dotaz SQL pro návrat počáteční sady záznamů. Použil jsem modul [MSSQL](https://www.npmjs.com/package/mssql) npm k načtení dat do aplikace NodeJS.

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
> [Příklad: Taxonomie omezující vlastnosti na více úrovních v Azure Search](search-example-adventureworks-multilevel-faceting.md)


