---
title: Optimalizace pro vývoj a testování v Azure Cosmos DB
description: Tento článek vysvětluje, jak Azure Cosmos DB nabízí několik možností pro vývoj a testování služby zdarma.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246679"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na vývoj a testování ve službě Azure Cosmos DB

Tento článek popisuje různé možnosti použití Azure Cosmos DB pro vývoj a testování zdarma, stejně jako techniky pro optimalizaci nákladů ve vývoji nebo testovací účty.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulátor Azure Cosmos DB (místně stahovatelná verze)

[Emulátor Azure Cosmos DB](local-emulator.md) je místní verze ke stažení, která napodobuje cloudovou službu Azure Cosmos DB. Můžete psát a testovat kód, který používá Azure Cosmos DB API i v případě, že nemáte žádné připojení k síti a bez jakýchkoli nákladů. Emulátor Azure Cosmos DB poskytuje místní prostředí pro účely vývoje s vysokou věrností cloudové službě. Můžete vyvíjet a testovat aplikaci místně, bez vytvoření předplatného Azure. Až budete připraveni nasadit aplikaci do cloudu, aktualizujte připojovací řetězec pro připojení ke koncovému bodu Azure Cosmos DB v cloudu, žádné další úpravy jsou potřeba. Můžete také [nastavit kanál CI/CD s úlohou sestavení emulátoru Azure Cosmos DB](tutorial-setup-ci-cd.md) v Azure DevOps pro spuštění testů. Můžete začít na stránce [Azure Cosmos DB emulátor](local-emulator.md) článku.

## <a name="azure-cosmos-db-free-tier"></a>Úroveň Azure Cosmos DB 
Bezplatná vrstva Azure Cosmos DB usnadňuje zahájení, vývoj a testování aplikací nebo dokonce bezplatné spuštění malých produkčních úloh. Pokud je na účtu povolena bezplatná úroveň, získáte prvních 400 RU/s a 5 GB úložiště na účtu zdarma. Můžete také vytvořit sdílenou databázi propustností s 25 kontejnery, které sdílejí 400 RU/s na úrovni databáze, všechny se vztahuje na úroveň free (limit 5 sdílené propustnost databází v účtu volné vrstvy). Úroveň free trvá po dobu neurčitou po dobu životnosti účtu a je dodávána se [všemi výhodami a funkcemi](introduction.md#key-benefits) běžného účtu Azure Cosmos DB, včetně neomezeného úložiště a propustností (RU/s), sla, vysoké dostupnosti, globální distribuce na klíč ve všech oblastech Azure a další. Můžete mít až jeden účet úrovně free na jedno předplatné Azure a musíte se přihlásit při vytváření účtu. Chcete-li začít, [vytvořte nový účet s povolenou bezplatnou úrovní](create-cosmosdb-resources-portal.md). Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/). 

## <a name="try-azure-cosmos-db-for-free"></a>Vyzkoušejte si Azure Cosmos DB zdarma

[Vyzkoušejte Azure Cosmos DB zdarma,](https://azure.microsoft.com/try/cosmosdb/) což vám umožní experimentovat s Azure Cosmos DB v cloudu, aniž byste si museli přihlašovat účet Azure nebo používat vaši platební kartu. Účty Try Azure Cosmos DB jsou k dispozici po omezenou dobu, aktuálně 30 dní. Můžete je kdykoli obnovit. Vyzkoušejte účty Azure Cosmos DB usnadňuje vyhodnocení Azure Cosmos DB, sestavení a testování aplikace nebo použití rychlých startů nebo kurzů. Můžete také vytvořit ukázku, provést testování částí nebo dokonce vytvořit účet s více oblastmi a spustit na něm aplikaci, aniž byste museli uvádět žádné náklady. V try Azure Cosmos DB účtu můžete mít jednu sdílenou propustnost databáze s maximálně 25 kontejnerů a 20 000 RU/s propustnosti nebo jeden kontejner s až 5000 RU/s. Pokud chcete začít, [přečtěte si článek Vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) stránku.

## <a name="azure-free-account"></a>Bezplatný účet Azure

Azure Cosmos DB je součástí [bezplatného účtu Azure](https://azure.microsoft.com/free), který nabízí kredity azure a prostředky zdarma po určitou dobu. Konkrétně pro Azure Cosmos DB, tento bezplatný účet nabízí úložiště 5 GB a 400 RU zřízené propustnostpro celý rok. Toto prostředí umožňuje každému vývojáři snadno otestovat funkce Azure Cosmos DB nebo integrovat s jinými službami Azure s nulovými náklady. S bezplatným účtem Azure získáte kredit $200, který můžete utratit v prvních 30 dnech. Nebudou vám účtovány poplatky, a to ani v případě, že služby začnete používat, dokud se nerozhodnete upgradovat. Chcete-li začít, navštivte stránku [účtu Azure zdarma.](https://azure.microsoft.com/free)

## <a name="use-shared-throughput-databases"></a>Použití databází sdílené propustností

V [databázi sdílené propustnosti](set-throughput.md#set-throughput-on-a-database)všechny kontejnery uvnitř databáze sdílejí zřízenou propustnost (RU/s) databáze. Pokud například zřídíte databázi se 400 RU/s a máte čtyři kontejnery, všechny čtyři kontejnery budou sdílet 400 RU/s. Ve vývojovém nebo testovacím prostředí, kde každý kontejner může být přístupná méně často a proto vyžadují nižší než minimálně 400 RU/s, umístění kontejnerů do databáze sdílené propustnosti může pomoci optimalizovat náklady. 

Předpokládejme například, že váš vývojový nebo testovací účet má čtyři kontejnery. Pokud vytvoříte čtyři kontejnery s vyhrazenou propustností (minimálně 400 RU/s), vaše celkové RU/s bude 1600 RU/s. Naproti tomu pokud vytvoříte sdílenou databázi propustností (minimálně 400 RU/s) a vložíte tam své kontejnery, vaše celkové RU/s bude pouze 400 RU/s. Obecně platí, že sdílené databáze propustnost jsou skvělé pro scénáře, kde nepotřebujete zaručenou propustnost na jednotlivých kontejnerech.  Další informace o [sdílených databázích propustností.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Další kroky

Můžete začít s použitím emulátoru nebo bezplatných účtů Azure Cosmos DB v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vaší faktury z DB Služby Azure Cosmos](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos s více oblastmi](optimize-cost-regions.md)

