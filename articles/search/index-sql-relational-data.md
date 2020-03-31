---
title: Modelová relační data SQL pro import a indexování
titleSuffix: Azure Cognitive Search
description: Naučte se modelovat relační data, denormalizované do sady plochých výsledků, pro indexování a fulltextové vyhledávání v Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790097"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Jak modelovat relační sql data pro import a indexování v Azure Cognitive Search

Azure Cognitive Search přijímá plochý řádek jako vstup do [kanálu indexování](search-what-is-an-index.md). Pokud vaše zdrojová data pocházejí z spojených tabulek v relační databázi SQL Server, tento článek vysvětluje, jak vytvořit sadu výsledků a jak modelovat vztah nadřazený podřízený v indexu Azure Cognitive Search.

Jako obrázek budeme odkazovat na hypotetické hotely databáze, na základě [demo data](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels). Předpokládejme, že databáze se skládá z hotels$ tabulky s 50 hotely a stolu Rooms$ s pokoji různých typů, sazeb a vybavení pro celkem 750 pokojů. Mezi tabulkami existuje relace 1:N. V našem přístupu bude zobrazení poskytovat dotaz, který vrátí 50 řádků, jeden řádek na hotel, s přidruženými detaily místnosti vloženými do každého řádku.

   ![Tabulky a zobrazení v databázi hotelů](media/index-sql-relational-data/hotels-database-tables-view.png "Tabulky a zobrazení v databázi hotelů")


## <a name="the-problem-of-denormalized-data"></a>Problém nenormalizovaných dat

Jednou z výzev při práci s relacemi 1:N je, že standardní dotazy postavené na spojených tabulkách vrátí nenormalizovaná data, která ve scénáři Azure Cognitive Search nefunguje dobře. Vezměme si následující příklad, který spojuje hotely a pokoje.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Výsledky tohoto dotazu vrátí všechna pole hotelu následovaná všemi poli Pokoje, přičemž pro každou hodnotu pokoje se opakují předběžné informace o hotelu.

   ![Nenormalizovaná data, redundantní data hotelu při přidání polí místnosti](media/index-sql-relational-data/denormalize-data-query.png "Nenormalizovaná data, redundantní data hotelu při přidání polí místnosti")


Zatímco tento dotaz uspěje na povrchu (poskytuje všechna data v sadě plochých řádků), se nezdaří při poskytování správné struktury dokumentu pro očekávané prostředí vyhledávání. Během indexování Azure Cognitive Search vytvoří jeden dokument hledání pro každý řádek požití. Pokud by vaše vyhledávací dokumenty vypadaly jako výše uvedené výsledky, vnímal i duplikáty - sedm samostatných dokumentů pouze pro hotel Twin Dome. Dotaz na "hotely na Floridě" by vrátil sedm výsledků pouze pro hotel Twin Dome, což by posunulo další relevantní hotely hluboko do výsledků vyhledávání.

Chcete-li získat očekávané zkušenosti z jednoho dokumentu na hotel, měli byste poskytnout sadu řádků v pravém rozlišovacíschopnosti, ale s úplnými informacemi. Naštěstí to můžete udělat snadno přijetím technik v tomto článku.

## <a name="define-a-query-that-returns-embedded-json"></a>Definování dotazu, který vrací vložený json

Chcete-li poskytovat očekávané prostředí vyhledávání, vaše sada dat by se měla skládat z jednoho řádku pro každý dokument hledání v Azure Cognitive Search. V našem příkladu chceme jeden řádek pro každý hotel, ale také chceme, aby naši uživatelé mohli vyhledávat v jiných oblastech souvisejících s místnostmi, na kterých jim záleží, jako je noční sazba, velikost a počet lůžek nebo výhled na pláž, které jsou součástí detailu místnosti.

Řešením je zachytit podrobnosti místnosti jako vnořené JSON a potom vložit strukturu JSON do pole v zobrazení, jak je znázorněno v druhém kroku. 

1. Předpokládejme, že máte dva spojené stoly, Hotels$ a Rooms$, které obsahují podrobnosti o 50 hotelech a 750 pokojích a jsou spojeny na poli HotelID. Jednotlivě tyto tabulky obsahují 50 hotelů a 750 souvisejících pokojů.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Vytvořte zobrazení složené ze všech polí`SELECT * from dbo.Hotels$`v nadřazené tabulce ( ), s přidáním nového pole *Místnosti,* které obsahuje výstup vnořeného dotazu. A **FOR JSON** `SELECT * from dbo.Rooms$` AUTO klauzule na struktury výstup jako JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   Následující snímek obrazovky ukazuje výsledné zobrazení s polem *Místnosti* nvarchar v dolní části. Pole *Pokoje* existuje pouze v zobrazení HotelRooms.

   ![Výhled na hotely](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms zobrazení")

1. Spuštěním `SELECT * FROM dbo.HotelRooms` načtěte sadu řádků. Tento dotaz vrátí 50 řádků, jeden na hotel, s informacemi o přidružené místnosti jako kolekce JSON. 

   ![Sada řádků ze zobrazení HotelRooms](media/index-sql-relational-data/hotelrooms-rowset.png "Sada řádků ze zobrazení HotelRooms")

Tato sada řádků je teď připravená pro import do Azure Cognitive Search.

> [!NOTE]
> Tento přístup předpokládá, že vložené JSON je pod [maximální omezení velikosti sloupce SQL Server](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server). Pokud se vaše data nevejdou, můžete zkusit programový přístup, jak je znázorněno v [příkladu: Model adventureworks inventářdatabáze pro Azure Cognitive Search](search-example-adventureworks-modeling.md).

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Použití komplexní kolekce pro stranu N relace 1:N

Na straně Azure Cognitive Search vytvořte schéma indexu, které modeluje vztah 1:N pomocí vnořeného JSON. Sada výsledků, kterou jste vytvořili v předchozí části, obecně odpovídá níže uvedenému schématu indexu (některá pole pro stručnost).

Následující příklad je podobný příkladu v [how to model ovat komplexní datové typy](search-howto-complex-data-types.md#creating-complex-fields). Rooms *Rooms* struktura, která byla zaměřena na tento článek, je v polích kolekce indexu s názvem *hotely*. Tento příklad také ukazuje komplexní typ *adresy*, který se liší od *místnosti* v tom, že se skládá z pevné sady položek, na rozdíl od více, libovolný počet položek povolených v kolekci.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
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
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Vzhledem k předchozí sadě výsledků a výše uvedenému schématu indexu máte všechny požadované součásti pro úspěšnou operaci indexování. Členitá sada dat splňuje požadavky na indexování, ale zachovává podrobné informace. V indexu Azure Cognitive Search se výsledky hledání snadno propadají do entit založených na hotelu při zachování kontextu jednotlivých místností a jejich atributů.

## <a name="next-steps"></a>Další kroky

Pomocí vlastní sady dat můžete index vytvořit a načíst pomocí [Průvodce importem dat.](search-import-data-portal.md) Průvodce zjistí vloženou kolekci JSON, například kolekci obsaženou v *rooms*, a odvodí schéma indexu, které obsahuje kolekci komplexních typů. 

  ![Index odvozený průvodcem importem dat](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Index odvozený průvodcem importem dat")

Vyzkoušejte následující rychlý start a přečtěte si základní kroky Průvodce importem dat.

> [!div class="nextstepaction"]
> [Úvodní příručka: Vytvoření vyhledávacího indexu pomocí portálu Azure](search-get-started-portal.md)