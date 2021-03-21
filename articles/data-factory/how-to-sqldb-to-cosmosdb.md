---
title: Migrace Azure SQL Database tabulek do Azure CosmosDB s využitím Azure Data Factory
description: Vezměte existující normalizované schéma databáze z Azure SQL Database a migrujte do denormalizovaného kontejneru Azure CosmosDB pomocí Azure Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 3d67ac9474704fac39dbe7eb91aead5c4babc4ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383938"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>Migrace normalizovaného schématu databáze z Azure SQL Database do denormalizovaného kontejneru Azure CosmosDB

V této příručce se dozvíte, jak v Azure SQL Database přijmout existující normalizované schéma databáze a jak ho převést do Azure CosmosDB denormalizovaného schématu pro načítání do Azure.

Schémata SQL jsou obvykle modelovaná pomocí třetího normálního tvaru, což vede k normalizovaným schématům, která poskytují vysokou úroveň integrity dat a méně duplicitních hodnot dat. Dotazy se můžou spojit mezi entitami za účelem čtení. CosmosDB je optimalizovaná pro velmi rychlé transakce a dotazování v rámci kolekce nebo kontejneru prostřednictvím denormalizovaných schémat s daty, která jsou v dokumentu samostatně obsažená.

Pomocí Azure Data Factory vytvoříme kanál, který používá jediný tok dat mapování ke čtení ze dvou Azure SQL Database normalizovaných tabulek, které jako vztah entity obsahují primární a cizí klíče. ADF se spojí s těmito tabulkami s jedním datovým proudem pomocí modulu Spark toku dat, shromáždí spojené řádky do polí a vytvoří jednotlivé vyčištěné dokumenty pro vložení do nového kontejneru Azure CosmosDB.

V tomto průvodci se vytvoří nový kontejner s názvem Orders, který bude používat ```SalesOrderHeader``` ```SalesOrderDetail``` tabulky a ze standardní ukázkové databáze SQL Server AdventureWorks. Tyto tabulky reprezentují prodejní transakce spojené s ```SalesOrderID``` . Každý jedinečný podrobný záznam má svůj vlastní primární klíč ```SalesOrderDetailID``` . Vztah mezi hlavičkou a podrobnostmi je ```1:M``` . Připojíme se k ```SalesOrderID``` programu ADF a pak všechny související záznamy podrobností načteme do pole s názvem "Detail".

Zástupce dotazu SQL pro tuto příručku:

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

Výsledný kontejner CosmosDB vloží vnitřní dotaz do jediného dokumentu a bude vypadat takto:

![Kolekce](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Vyberte **+ Nový kanál** a vytvořte nový kanál.

2. Přidání aktivity toku dat

3. V aktivitě toku dat vyberte **Nový tok dat mapování**.

4. Tento graf toku dat budeme sestavovat níže.

![Graf toku dat](media/data-flow/cosmosb1.png)

5. Definujte zdroj pro "SourceOrderDetails". Pro datovou sadu vytvořte novou Azure SQL Database datovou sadu, která odkazuje na ```SalesOrderDetail``` tabulku.

6. Definujte zdroj pro "SourceOrderHeader". Pro datovou sadu vytvořte novou Azure SQL Database datovou sadu, která odkazuje na ```SalesOrderHeader``` tabulku.

7. V horním zdroji přidejte transformaci odvozeného sloupce za "SourceOrderDetails". Zavolejte novou transformaci "přetypovat". Musíme sloupec zaokrouhlit ```UnitPrice``` a přetypovat na datový typ Double pro CosmosDB. Nastavte vzorec na: ```toDouble(round(UnitPrice,2))``` .

8. Přidejte další odvozený sloupec a zavolejte ho "MakeStruct". Tady vytvoříme hierarchickou strukturu, která bude uchovávat hodnoty z tabulky details. Pamatujte, že podrobnosti jsou ```M:1``` vztah k hlavičce. Pojmenujte novou strukturu ```orderdetailsstruct``` a tímto způsobem vytvořte hierarchii a nastavte jednotlivé podsloupce na název příchozího sloupce:

![Vytvořit strukturu](media/data-flow/cosmosb9.png)

9. Teď přejdeme na zdroj prodejní hlavičky. Přidejte transformaci JOIN. Pro pravou stranu vyberte "MakeStruct". Nechejte nastavenou na vnitřní spojení a vyberte ```SalesOrderID``` pro obě strany podmínky spojení.

10. V novém připojení, které jste přidali, klikněte na kartu náhled dat, abyste viděli výsledky až do tohoto okamžiku. Měli byste vidět všechny řádky záhlaví spojené s řádky podrobností. Jedná se o výsledek připojení, které je vytvořeno z ```SalesOrderID``` . Dále zkombinujeme podrobnosti z běžných řádků do struktury Details a agreguje společné řádky.

![Připojení](media/data-flow/cosmosb4.png)

11. Předtím, než můžeme vytvořit pole pro odnormalizování těchto řádků, musíme nejdřív odebrat nechtěné sloupce a ujistit se, že hodnoty dat budou odpovídat datovým typům CosmosDB.

12. Přidejte transformaci Select a pak nastavte mapování polí tak, aby vypadalo takto:

![Čištění sloupců](media/data-flow/cosmosb5.png)

13. Nyní se teď znovu přiblíží sloupec měny ```TotalDue``` . Stejně jako v kroku 7 jsme nastavili vzorec na: ```toDouble(round(TotalDue,2))``` .

14. V tomto umístění budeme normalizovat řádky seskupením podle společného klíče ```SalesOrderID``` . Přidejte agregační transformaci a nastavte skupinu na ```SalesOrderID``` .

15. V agregačním vzorci přidejte nový sloupec s názvem "Details" a pomocí tohoto vzorce Shromážděte hodnoty ve struktuře, kterou jsme dříve vytvořili s názvem ```orderdetailsstruct``` : ```collect(orderdetailsstruct)``` .

16. Agregovaná transformace bude mít pouze výstupní sloupce, které jsou součástí agregačních nebo skupinových vzorců. Proto je potřeba zahrnout i sloupce z hlavičky Sales. K tomu přidejte vzor sloupce ve stejné agregační transformaci. Tento model bude obsahovat všechny ostatní sloupce ve výstupu:

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. Použijte syntaxi "This" ve dalších vlastnostech, abyste zachovali stejné názvy sloupců a funkci používali ```first()``` jako agregaci:

![Agregace](media/data-flow/cosmosb6.png)

18. Jsme připraveni dokončit tok migrace přidáním transformace jímky. Klikněte na nový vedle datové sady a přidejte datovou sadu CosmosDB, která odkazuje na vaši databázi CosmosDB. Pro kolekci budeme volat "objednávky" a nebude mít žádné schéma a žádné dokumenty, protože se vytvoří průběžně.

19. V nastavení jímky, klíč oddílu na ```\SalesOrderID``` a akci kolekce na "znovu vytvořit". Zajistěte, aby karta mapování vypadala takto:

![Snímek obrazovky se zobrazí karta mapování.](media/data-flow/cosmosb7.png)

20. Klikněte na náhled dat a ujistěte se, že se tyto 32 řádky nastavily tak, aby se do nového kontejneru vložily jako nové dokumenty:

![Snímek obrazovky se zobrazí na kartě náhled dat.](media/data-flow/cosmosb8.png)

Pokud vše vypadá dobře, teď jste připraveni vytvořit nový kanál, přidejte tuto aktivitu toku dat do tohoto kanálu a spusťte ji. Můžete provést z ladění nebo aktivovaného spuštění. Po několika minutách byste měli mít v databázi CosmosDB nový denormalizovaný kontejner objednávek nazývaný "Orders".

## <a name="next-steps"></a>Další kroky

* Sestavte zbývající část logiky toku dat pomocí [transformací](concepts-data-flow-overview.md)mapování toků dat.
* [Stáhněte si dokončenou šablonu kanálu](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) pro tento kurz a importujte šablonu do továrny.
