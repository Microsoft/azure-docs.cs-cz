---
title: Úvod do služby Azure Cosmos DB Table API
description: Zjistěte, jak můžete používat Azure Cosmos DB k ukládání a dotazování velkých objemů dat s klíčovými hodnotami s nízkou latencí pomocí oblíbených rozhraní API OSS MongoDB.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.topic: overview
ms.date: 11/20/2017
ms.author: sngun
ms.openlocfilehash: 2647065d251caea571e2bfcd5703bbb3bec08345
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074299"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Úvod do Azure Cosmos DB: API tabulka

[Azure Cosmos DB](introduction.md) poskytuje rozhraní API tabulky pro aplikace napsané pro službu Azure Table Storage, které vyžadují prémiové funkce, jako například:

* [Globální distribuce na klíč](distribute-data-globally.md).
* [Vyhrazená propustnost](partition-data.md) po celém světě.
* Latence v řádu milisekund na 99. percentilu.
* Záruka vysoké dostupnosti.
* [Automatické sekundární indexování](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

Pomocí rozhraní API tabulky je možné migrovat aplikace napsané pro Azure Table Storage beze změn kódu na službu Azure Cosmos DB a využít tak prémiové funkce. Rozhraní API tabulky má klientské sady SDK dostupné pro .NET, Java, Python a Node.js.

## <a name="table-offerings"></a>Nabídky Table
Pokud aktuálně používáte službu Azure Table Storage, získáte přechodem na rozhraní API tabulky Azure Cosmos DB následující výhody:

| | Azure Table Storage | Rozhraní API tabulky Azure Cosmos DB |
| --- | --- | --- |
| Latence | Rychlá, bez horních omezení latence. | Latence pro čtení a zápis v řádu milisekund, podložená latencí pro čtení menší než 10 ms a latencí pro zápis menší než 15 ms na 99. percentilu, při libovolném škálování, kdekoli na světě. |
| Propustnost | Model variabilní propustnosti. Tabulky mají omezení škálovatelnosti 20 000 operací za sekundu. | Vysoká škálovatelnost s [vyhrazenou rezervovanou propustností na tabulku](request-units.md), podložená smlouvami SLA. Účty nemají žádné horní omezení propustnosti a podporují více než 10 milionů operací za sekundu na tabulku. |
| Globální distribuce | Jedna oblast s jednou volitelnou čitelnou sekundární oblastí čtení pro vysokou dostupnost. Nemůžete zahájit převzetí služeb při selhání. | [Globální distribuce na klíč](distribute-data-globally.md) od jedné po 30 a více oblastí. Podpora [automatického a ručního převzetí služeb při selhání](high-availability.md) kdykoli a kdekoli na světě. |
| Indexování | PartitionKey a RowKey používají pouze primární index. Žádné sekundární indexy. | Automatické a úplné indexování u všech vlastností, žádná správa indexů. |
| Dotaz | Při provádění dotazu se používá index pro primární klíč, jinak dochází k prohledávání. | Dotazy mohou ke zrychlení použít výhod automatického indexování vlastností. |
| Konzistence | Silná v rámci primární oblasti. Nahodilá v rámci sekundární oblasti. | [Pět jasně definovaných úrovní konzistence](consistency-levels.md) pro využití dostupnosti, latence, propustnosti a konzistence na základě potřeb vašich aplikací. |
| Ceny | Optimalizované úložiště. | Optimalizovaná propustnost. |
| Smlouvy SLA | 99,99 % dostupnost. | Smlouva SLA o 99,99% dostupnosti pro všechny účty v jedné oblasti a všechny účty ve více oblastech s mírnější konzistencí a [Nejlepší komplexní smlouvy SLA v oboru](https://azure.microsoft.com/support/legal/sla/cosmos-db/) týkající se obecné dostupnosti zajišťující 99,999% dostupnost čtení pro všechny účty databáze ve více oblastech. |

## <a name="get-started"></a>Začínáme

Vytvořte si účet služby Azure Cosmos DB na webu [Azure Portal](https://portal.azure.com). Pak začněte pomocí našeho [Rychlého startu pro rozhraní API tabulky pomocí rozhraní .NET](create-table-dotnet.md). 

> [!IMPORTANT]
> Pokud jste vytvořili účet Table API během období Preview, vytvořte [nový účet Table API](create-table-dotnet.md#create-a-database-account) pro práci s obecně dostupnými sadami Table API SDK.
>

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na informace, které vám pomůžou začít:
* [Vytvoření aplikace .NET pomocí rozhraní API tabulky](create-table-dotnet.md)
* [Vývoj pomocí rozhraní API tabulky v .NET](tutorial-develop-table-dotnet.md)
* [Dotazování tabulkových dat pomocí rozhraní API tabulky](tutorial-query-table.md)
* [Informace o nastavení globální distribuce služby Azure Cosmos DB pomocí rozhraní API tabulky](tutorial-global-distribution-table.md)
* [Rozhraní .NET API tabulky Azure Cosmos DB](table-sdk-dotnet.md)
* [Rozhraní Java API tabulky Azure Cosmos DB](table-sdk-java.md)
* [Rozhraní Node.js API tabulky Azure Cosmos DB](table-sdk-nodejs.md)
* [Sada SDK tabulky Azure Cosmos DB pro Python](table-sdk-python.md)

