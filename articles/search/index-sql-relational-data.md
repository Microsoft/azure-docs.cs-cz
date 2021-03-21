---
title: Modelování relačních dat SQL pro import a indexování
titleSuffix: Azure Cognitive Search
description: Naučte se modelovat relační data, Denormalizovaná do ploché sady výsledků, pro indexování a fulltextové vyhledávání v Azure Kognitivní hledání.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c70b42e7d0f647a3b2b60d29b5098a791e4975f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88924516"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Postup modelování relačních dat SQL pro import a indexování v Azure Kognitivní hledání

Azure Kognitivní hledání přijímá jako vstup do [kanálu indexování](search-what-is-an-index.md)plochou sadu řádků. Pokud zdrojová data pocházejí z připojených tabulek v relační databázi SQL Server, Tento článek vysvětluje, jak sestavit sadu výsledků a jak modelovat vztah nadřazenosti-podřízenosti v indexu Azure Kognitivní hledání.

Obrázek odkazuje na hypotetickou databázi hotelů na základě [ukázkových dat](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels). Předpokládejme, že se databáze skládá z tabulky s Hotely $ s 50 hotely a místnostmi $ tabulkami s místnostmi různých typů, sazeb a možností rekreačního celkem 750 místností. Mezi tabulkami existuje vztah 1: n. V našem přístupu vám zobrazení poskytne dotaz, který vrátí 50 řádků, jeden řádek na Hotel s přiřazenými podrobnostmi o místnostech vložených do každého řádku.

   ![Tabulky a zobrazení v databázi hotelů](media/index-sql-relational-data/hotels-database-tables-view.png "Tabulky a zobrazení v databázi hotelů")


## <a name="the-problem-of-denormalized-data"></a>Problém denormalizovaných dat

Jedním z problémů při práci s relacemi 1:1 je, že standardní dotazy sestavené na připojených tabulkách budou vracet Denormalizovaná data, která nefungují dobře ve scénáři Kognitivní hledání v Azure. Vezměte v úvahu následující příklad, který spojuje hotely a místnosti.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Výsledky z tohoto dotazu vrátí všechna pole hotelu, za kterými následuje všechna pole místností. předběžné informace o hotelu se opakují pro každou hodnotu místnosti.

   ![Denormalizovaná data, redundantní data o hotelu, když se přidají pole místnosti](media/index-sql-relational-data/denormalize-data-query.png "Denormalizovaná data, redundantní data o hotelu, když se přidají pole místnosti")


I když tento dotaz proběhne na povrchu (poskytnutí všech dat v ploché sadě řádků), selže při poskytování správné struktury dokumentu pro očekávané prostředí vyhledávání. Při indexování se v Azure Kognitivní hledání vytvoří jeden vyhledávací dokument pro každý řádek, který se bude obhodnotit. Pokud vaše dokumenty hledání vypadají jako výše uvedené výsledky, měli byste se podívat na duplicity – sedm samostatných dokumentů pro jediný hotel s dvojitou čočky. Dotaz na "hotely v Florida" vrátí sedm výsledků jenom pro zdvojený Hotel, který doplní další relevantní hotely do výsledků hledání.

Chcete-li získat očekávané zkušenosti jednoho dokumentu na jeden Hotel, měli byste poskytnout sadu řádků v pravé členitosti, ale s úplnými informacemi. Naštěstí to můžete provést snadno pomocí postupů v tomto článku.

## <a name="define-a-query-that-returns-embedded-json"></a>Definujte dotaz, který vrátí vložený JSON.

Pro zajištění očekávaného vyhledávacího prostředí by se měla sada dat skládat z jednoho řádku pro každý dokument hledání ve službě Azure Kognitivní hledání. V našem příkladu chceme mít jeden řádek pro každý Hotel, ale chceme, aby naši uživatelé mohli prohledávat další pole související s místnostmi, která vás zajímají, jako je noční sazba, velikost a počet lůžek nebo zobrazení pláže, přičemž všechny jsou součástí podrobností místnosti.

Řešením je zachytit podrobnosti místnosti jako vnořený kód JSON a pak vložit strukturu JSON do pole v zobrazení, jak je znázorněno v druhém kroku. 

1. Předpokládejme, že máte dvě spojené tabulky, hotely $ a místnosti $, které obsahují podrobnosti o 50 hotelů a 750 místnostech a jsou připojené k poli HotelID. Tyto tabulky obsahují jednotlivě 50 hotelů a 750 souvisejících místností.

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

2. Vytvoří zobrazení složené ze všech polí v nadřazené tabulce ( `SELECT * from dbo.Hotels$` ) s přidáním nového pole *místností* , které obsahuje výstup vnořeného dotazu. Klauzule **for JSON auto** u `SELECT * from dbo.Rooms$` strukturuje výstup jako JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   Následující snímek obrazovky ukazuje výsledné zobrazení, které obsahuje pole *místnosti* nvarchar v dolní části. Pole *místností* existuje pouze v zobrazení HotelRooms.

   ![Zobrazení HotelRooms](media/index-sql-relational-data/hotelsrooms-view.png "Zobrazení HoteRooms")

1. Spusťte `SELECT * FROM dbo.HotelRooms` , aby se načetla sada řádků. Tento dotaz vrátí 50 řádků, jeden pro každý Hotel, s přidruženými informacemi o místnostech jako kolekce JSON. 

   ![Sada řádků ze zobrazení HotelRooms](media/index-sql-relational-data/hotelrooms-rowset.png "Sada řádků ze zobrazení HotelRooms")

Tato sada řádků je teď připravená na import do Azure Kognitivní hledání.

> [!NOTE]
> Tento přístup předpokládá, že vložený kód JSON je pod [omezením maximální velikosti sloupce SQL Server](/sql/sql-server/maximum-capacity-specifications-for-sql-server). 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Použití komplexní kolekce pro stranu typu "many" pro relaci 1: n

Na straně Azure Kognitivní hledání vytvořte schéma indexu, které vytváří vztah 1:1 pomocí vnořeného JSON. Sada výsledků dotazu, kterou jste vytvořili v předchozí části, obecně odpovídá schématu indexu uvedenému níže (pro zkrácení jsou k dispozici nějaká pole).

Následující příklad je podobný příkladu v tématu [jak modelovat komplexní datové typy](search-howto-complex-data-types.md#creating-complex-fields). Struktura *místností* , která se zaměřuje na tento článek, je v kolekci polí indexu s názvem *hotely*. Tento příklad také ukazuje komplexní typ pro *adresu*, který se liší od *místností* v tom, že se skládá z pevné sady položek, na rozdíl od násobku, libovolný počet položek povolených v kolekci.

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

Vzhledem k předchozí sadě výsledků a výše uvedenému schématu indexu máte všechny požadované součásti pro úspěšnou operaci indexování. Sloučená datová sada splňuje požadavky na indexování, které ještě zachovává podrobné informace. V indexu služby Azure Kognitivní hledání se výsledky hledání snadno začlení do hotelových entit a zároveň zachovává kontext jednotlivých místností a jejich atributů.

## <a name="next-steps"></a>Další kroky

Pomocí vlastní datové sady můžete index vytvořit a načíst pomocí [Průvodce importem dat](search-import-data-portal.md) . Průvodce detekuje vloženou kolekci JSON, jako je ta obsažená v *místnostech*, a odvodí schéma indexu, které obsahuje kolekci komplexních typů. 

  ![Index odvozený pomocí Průvodce importem dat](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Index odvozený pomocí Průvodce importem dat")

Pokud se chcete seznámit se základními kroky Průvodce importem dat, zkuste použít následující rychlý Start.

> [!div class="nextstepaction"]
> [Rychlý Start: vytvoření indexu vyhledávání pomocí Azure Portal](search-get-started-portal.md)