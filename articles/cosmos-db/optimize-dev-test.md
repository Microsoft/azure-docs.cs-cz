---
title: Optimalizace pro vývoj a testování ve službě Azure Cosmos DB
description: Tento článek vysvětluje, jak službu Azure Cosmos DB nabízí více možností pro vývoj a testování služby zdarma.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.openlocfilehash: 7e75ee6e7c9cd30911dd00a81cdd50b688346985
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036473"
---
# <a name="optimizing-for-development-and-testing-in-azure-cosmos-db"></a>Optimalizace pro vývoj a testování ve službě Azure Cosmos DB

Tento článek popisuje různé možnosti pro vývoj a testování zdarma nákladů pomocí služby Azure Cosmos DB.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulátor služby Azure Cosmos DB (místně ke stažení verze)

[Emulátor služby Azure Cosmos DB](local-emulator.md) je místní verzi ke stažení, která napodobuje cloudovou službu Azure Cosmos DB. Můžete napsat a testovat kód, který používá rozhraní API služby Azure Cosmos DB, i když máte žádné síťové připojení a bez bez jakýchkoli nákladů. Emulátor služby Azure Cosmos DB poskytuje místní prostředí pro účely vývoje s vysokou věrností ke cloudové službě. Můžete vyvíjet a testovat aplikace místně bez vytváření předplatného Azure. Jakmile budete připraveni k nasazení aplikace do cloudu, aktualizujte připojovací řetězec pro připojení ke koncovému bodu služby Azure Cosmos DB v cloudu, nejsou potřeba žádné úpravy. Můžete také [nastavení kanálu CI/CD pomocí emulátoru služby Azure Cosmos DB](tutorial-setup-ci-cd.md) vytvářet úlohy v Azure DevOps pro spouštění testů. Můžete začít návštěvou [emulátor služby Azure Cosmos DB](local-emulator.md) článku.

## <a name="try-azure-cosmos-db-for-free"></a>Vyzkoušejte si Azure Cosmos DB zdarma

[Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) je bezplatná poplatek prostředí, která umožňuje vytvoření databáze a kolekce a Experimentujte s Azure Cosmos DB v cloudu. Nemáte k registraci pro Azure nebo platit žádné poplatky. Účty zkuste Azure Cosmos DB jsou k dispozici po omezenou dobu, v současnosti 30 dní. Kdykoli můžete obnovit je. Vyzkoušejte si Azure Cosmos DB účtů usnadňuje vyhodnotit službu Azure Cosmos DB, sestavení a testování aplikace pomocí kurzů nebo rychlých startů. Můžete vytvořit na ukázku nebo provádět testování bez bez jakýchkoli nákladů. S využitím vyzkoušejte si Azure Cosmos DB pro bezplatné účty, můžete si vyzkoušet prémiové možnosti služby Azure Cosmos DB zdarma, včetně na klíč globální distribuce, smlouvy o úrovni služeb a konzistence modelů. Vytvořit databázi s maximálně 25 kontejnery Azure Cosmos a propustnosti 10 000 RU/s. Spuštění ukázkové aplikace bez přihlášení k účtu Azure nebo pomocí platební karty. S vyzkoušejte Azure Cosmos DB zdarma, můžete vytvořit účet více oblastí Azure Cosmos a spuštění aplikace na ní za několik minut. Abyste mohli začít, najdete v článku [vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) stránky.

## <a name="azure-free-account"></a>Bezplatný účet Azure

Je součástí služby Azure Cosmos DB [bezplatný účet Azure](https://azure.microsoft.com/free), který nabízí kredity Azure a prostředky zdarma pro určité časové období. Konkrétně pro Azure Cosmos DB nabízí bezplatný účet 5 GB úložiště a zřízenou propustnost 400 ru za celý rok. Toto prostředí umožňuje vývojáři ocení snadno testovat funkce služby Azure Cosmos DB nebo ji integrovat s dalšími službami Azure nulovými náklady. S bezplatným účtem Azure získejte kredit 200 USD, který můžou investovat do prvních 30 dnů. Vám nebude účtovat, i když začnete používat služby, dokud se nerozhodnete pro upgrade. Abyste mohli začít, navštivte [bezplatný účet Azure](https://azure.microsoft.com/free) stránky.

## <a name="next-steps"></a>Další postup

Můžete začít s pomocí emulátoru nebo bezplatných účtů služby Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizace pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vašeho vyúčtování Azure Cosmos DB](understand-your-bill.md)
* Další informace o [optimalizace propustnosti náklady](optimize-cost-throughput.md)
* Další informace o [optimalizovat náklady na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizovat náklady na operace čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizovat náklady na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizovat náklady na účty ve více oblastech Azure Cosmos](optimize-cost-regions.md)

