---
title: Úvod do Azure Cosmos DB rozhraní API pro tabulky
description: Přečtěte si, jak můžete použít Azure Cosmos DB k ukládání a dotazování velkých objemů dat klíč-hodnota s nízkou latencí pomocí rozhraní API Azure Tables.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 01/08/2021
ms.author: sngun
ms.openlocfilehash: 1cf3bf30b37a09b5dfe94bf1e754a7f8e9dcd82c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045661"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Úvod do Azure Cosmos DB: API tabulka
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[Azure Cosmos DB](introduction.md) poskytuje rozhraní API tabulky pro aplikace napsané pro službu Azure Table Storage, které vyžadují prémiové funkce, jako například:

* [Globální distribuce klíč](distribute-data-globally.md).
* [Vyhrazená propustnost](partitioning-overview.md) po celém světě (při použití zřízené propustnosti).
* Latence v řádu milisekund na 99. percentilu.
* Záruka vysoké dostupnosti.
* Automatické sekundární indexování.

Pomocí rozhraní API tabulky je možné migrovat aplikace napsané pro Azure Table Storage beze změn kódu na službu Azure Cosmos DB a využít tak prémiové funkce. Rozhraní API tabulky má klientské sady SDK dostupné pro .NET, Java, Python a Node.js.

> [!NOTE]
> [Režim kapacity bez serveru](serverless.md) je teď k dispozici v rozhraní API pro tabulky Azure Cosmos DB.

> [!IMPORTANT]
> Sada .NET Framework SDK [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) je v režimu údržby a bude brzy zastaralá. Upgradujte prosím na novou .NET Standard knihovny [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , abyste mohli dál získávat nejnovější funkce, které rozhraní API pro tabulky podporuje.

## <a name="table-offerings"></a>Nabídky Table
Pokud aktuálně používáte službu Azure Table Storage, získáte přechodem na rozhraní API tabulky Azure Cosmos DB následující výhody:

| Příznak | Azure Table Storage | Rozhraní API tabulky Azure Cosmos DB |
| --- | --- | --- |
| Latence | Rychlá, bez horních omezení latence. | Latence pro čtení a zápis v řádu milisekund, která se zálohuje <10 MS, pro čtení a zápisy na 99 percentilu v jakémkoli měřítku, kdekoli na světě. |
| Propustnost | Model variabilní propustnosti. Tabulky mají omezení škálovatelnosti 20 000 operací za sekundu. | Vysoká škálovatelnost s [vyhrazenou rezervovanou propustností na tabulku](request-units.md), podložená smlouvami SLA. Účty nemají žádné horní omezení propustnosti a podporují více než 10 milionů operací za sekundu na tabulku. |
| Globální distribuce | Jedna oblast s jednou volitelnou čitelnou sekundární oblastí čtení pro vysokou dostupnost. | [Klíč globální distribuce](distribute-data-globally.md) z jednoho na libovolný počet oblastí. Podpora [automatického a ručního převzetí služeb při selhání](high-availability.md) kdykoli a kdekoli na světě. Více oblastí pro zápis, které umožňují, aby kterákoli oblast přijímala operace zápisu. |
| Indexování | PartitionKey a RowKey používají pouze primární index. Žádné sekundární indexy. | Automatické a úplné indexování všech vlastností ve výchozím nastavení bez správy indexů. |
| Dotaz | Při provádění dotazu se používá index pro primární klíč, jinak dochází k prohledávání. | Dotazy mohou ke zrychlení použít výhod automatického indexování vlastností. |
| Konzistence | Silná v rámci primární oblasti. Nahodilá v rámci sekundární oblasti. | [Pět jasně definovaných úrovní konzistence](consistency-levels.md) pro zajištění dostupnosti, latence, propustnosti a konzistence v závislosti na potřebách vaší aplikace. |
| Ceny | Založené na spotřebě. | K dispozici v režimech [na základě spotřeby](serverless.md) i [zřízené kapacity](set-throughput.md) . |
| Smlouvy SLA | 99,9% až 99,99% dostupnost v závislosti na strategii replikace. | 99,999% dostupnost pro čtení, 99,99% dostupnost zápisu v účtu s jednou oblastí a 99,999% dostupnosti zápisu pro účty ve více oblastech. [Komplexní SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) zahrnující dostupnost, latenci, propustnost a konzistenci. |

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
* [Sada SDK pro .NET Standard Azure Cosmos DB Table](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB tabulka .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB tabulka Java SDK](table-sdk-java.md)
* [Sada SDK pro Node.js Azure Cosmos DB Table](table-sdk-nodejs.md)
* [Sada SDK pro Azure Cosmos DB tabulky pro Python](table-sdk-python.md)
