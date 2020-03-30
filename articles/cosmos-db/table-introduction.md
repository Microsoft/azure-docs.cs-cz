---
title: Úvod do rozhraní API tabulky Azure Cosmos DB
description: Zjistěte, jak můžete pomocí Azure Cosmos DB ukládat a dotazovat se na obrovské objemy dat s hodnotou klíčů s nízkou latencí pomocí rozhraní AZURE Tables API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 07/26/2019
ms.author: sngun
ms.openlocfilehash: 5b2e2c51eaa878ba0ce8bc31c001575acebe6919
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240153"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Úvod do Azure Cosmos DB: API tabulka

[Azure Cosmos DB](introduction.md) poskytuje rozhraní API tabulky pro aplikace napsané pro službu Azure Table Storage, které vyžadují prémiové funkce, jako například:

* [Globální distribuce na klíč](distribute-data-globally.md).
* [Vyhrazená propustnost](partition-data.md) po celém světě.
* Latence v řádu milisekund na 99. percentilu.
* Záruka vysoké dostupnosti.
* Automatické sekundární indexování.

Pomocí rozhraní API tabulky je možné migrovat aplikace napsané pro Azure Table Storage beze změn kódu na službu Azure Cosmos DB a využít tak prémiové funkce. Rozhraní API tabulky má klientské sady SDK dostupné pro .NET, Java, Python a Node.js.

> [!IMPORTANT]
> Sada .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) je v režimu údržby a brzy se zastaralá. Chcete-li pokračovat v získání nejnovějších funkcí podporovaných rozhraním Table API, upgradujte na novou knihovnu Standard .NET Standard [Microsoft.Azure.Cosmos.Table.](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)

## <a name="table-offerings"></a>Nabídky Table
Pokud aktuálně používáte službu Azure Table Storage, získáte přechodem na rozhraní API tabulky Azure Cosmos DB následující výhody:

| | Azure Table Storage | Rozhraní API tabulky Azure Cosmos DB |
| --- | --- | --- |
| Latence | Rychlá, bez horních omezení latence. | Jednociferná milisekundová latence pro čtení a zápisy, podporovaná <latencí 10 ms pro čtení a zápisy v percentilu 99, v libovolném měřítku, kdekoli na světě. |
| Propustnost | Model variabilní propustnosti. Tabulky mají omezení škálovatelnosti 20 000 operací za sekundu. | Vysoká škálovatelnost s [vyhrazenou rezervovanou propustností na tabulku](request-units.md), podložená smlouvami SLA. Účty nemají žádné horní omezení propustnosti a podporují více než 10 milionů operací za sekundu na tabulku. |
| Globální distribuce | Jedna oblast s jednou volitelnou čitelnou sekundární oblastí čtení pro vysokou dostupnost. Nemůžete zahájit převzetí služeb při selhání. | [Globální distribuce na klíč](distribute-data-globally.md) z jednoho do libovolného počtu oblastí. Podpora [automatického a ručního převzetí služeb při selhání](high-availability.md) kdykoli a kdekoli na světě. Multi-master schopnost nechat libovolnou oblast přijímat operace zápisu. |
| Indexování | PartitionKey a RowKey používají pouze primární index. Žádné sekundární indexy. | Automatické a úplné indexování všech vlastností ve výchozím nastavení bez správy indexu. |
| Dotaz | Při provádění dotazu se používá index pro primární klíč, jinak dochází k prohledávání. | Dotazy mohou ke zrychlení použít výhod automatického indexování vlastností. |
| Konzistence | Silná v rámci primární oblasti. Nahodilá v rámci sekundární oblasti. | [Pět dobře definované úrovně konzistence](consistency-levels.md) k vypnutí dostupnost, latence, propustnost a konzistence na základě potřeb vaší aplikace. |
| Ceny | Optimalizované úložiště. | Optimalizovaná propustnost. |
| Smlouvy SLA | 99,9% až 99,99% dostupnost, v závislosti na strategii replikace. | 99.999% dostupnost čtení, 99.99% dostupnost zápisu na účet s jednou oblastí a 99.999% dostupnost zápisu na účtech s více oblastmi. [Komplexní smlouvy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) zahrnující dostupnost, latenci, propustnost a konzistenci. |

## <a name="get-started"></a>Začínáme

Vytvořte si účet služby Azure Cosmos DB na webu [Azure Portal](https://portal.azure.com). Pak začněte pomocí našeho [Rychlého startu pro rozhraní API tabulky pomocí rozhraní .NET](create-table-dotnet.md). 

> [!IMPORTANT]
> Pokud jste vytvořili účet Table API během období Preview, vytvořte [nový účet Table API](create-table-dotnet.md#create-a-database-account) pro práci s obecně dostupnými sadami Table API SDK.
>

## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na informace, které vám pomůžou začít:
* [Vytvoření aplikace .NET pomocí rozhraní API tabulky](create-table-dotnet.md)
* [Vývoj pomocí rozhraní API tabulky v .NET](tutorial-develop-table-dotnet.md)
* [Dotazování tabulkových dat pomocí rozhraní API tabulky](tutorial-query-table.md)
* [Informace o nastavení globální distribuce služby Azure Cosmos DB pomocí rozhraní API tabulky](tutorial-global-distribution-table.md)
* [Tabulka Azure Cosmos DB .NET Standard SDK](table-sdk-dotnet-standard.md)
* [Tabulka Db Azure Cosmos .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB Tabulka Java SDK](table-sdk-java.md)
* [Sada SDK tabulky Azure Cosmos DB](table-sdk-nodejs.md)
* [Sada Azure Cosmos DB Table SDK pro Python](table-sdk-python.md)