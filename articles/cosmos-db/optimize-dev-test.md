---
title: Optimalizace pro vývoj a testování v Azure Cosmos DB
description: Tento článek vysvětluje, jak Azure Cosmos DB nabízí více možností pro vývoj a testování služby zdarma.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 8c2194476dc6e640f9c4b93d564728478de41ef7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475528"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na vývoj a testování ve službě Azure Cosmos DB

Tento článek popisuje různé možnosti použití Azure Cosmos DB pro vývoj a testování bez jakýchkoli nákladů a také techniky pro optimalizaci nákladů ve vývojových nebo testovacích účtech.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulátor Azure Cosmos DB (místně zaváděná verze)

[Emulátor Azure Cosmos DB](local-emulator.md) je místní verze ke stažení, která napodobuje cloudovou službu Azure Cosmos DB. Kód, který používá rozhraní API Azure Cosmos DB, můžete napsat a otestovat i v případě, že nemáte žádné síťové připojení a neúčtujete náklady. Emulátor Azure Cosmos DB poskytuje místní prostředí pro účely vývoje s vysokou přesností pro cloudovou službu. Svou aplikaci můžete vyvíjet a testovat místně, aniž byste museli vytvářet předplatné Azure. Až budete připraveni k nasazení aplikace do cloudu, aktualizujte připojovací řetězec pro připojení ke koncovému bodu Azure Cosmos DB v cloudu, nepotřebujete žádné další úpravy. Můžete také [nastavit kanál CI/CD s úlohou sestavení emulátoru Azure Cosmos DB](tutorial-setup-ci-cd.md) ve službě Azure DevOps pro spouštění testů. Můžete začít tím, že navštívíte článek [Azure Cosmos DB emulátoru](local-emulator.md) .

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB úroveň Free

Azure Cosmos DB úroveň Free usnadňuje začátek, vývoj a testování aplikací nebo dokonce i spouštění malých produkčních úloh zdarma. Pokud je na účtu povolená úroveň Free, získáte v bezplatném účtu prvních 400 RU/s a 5 GB úložiště. Můžete také vytvořit sdílenou databázi propustnosti s 25 kontejnery, které sdílejí 400 RU/s na úrovni databáze, a to vše zahrnuté do bezplatné úrovně (omezení 5 sdílených databází propustnosti v účtu bezplatné úrovně). Pokud při použití bezplatné úrovně zřídíte sdílenou databázi s minimální propustností 400 RU/s, můžou všechny kontejnery v této databázi sdílet propustnost. Všechny nové databáze se sdílenou propustností nebo kontejnery s vyhrazenou propustností se účtují běžnými cenami.

> [!NOTE]
> Úroveň Free je dostupná jenom v režimu zřízené propustnosti.

Úroveň Free pro celou dobu životnosti účtu trvá neomezenou dobu a přináší všechny [výhody a funkce](introduction.md#key-benefits) pravidelného Azure Cosmos DB účtu, včetně neomezené kapacity úložiště a propustnosti (ru/s), SLA, vysoké dostupnosti, klíč globální distribuce ve všech oblastech Azure a dalších. Můžete mít až jeden účet bezplatné úrovně na jedno předplatné Azure a při vytváření účtu musí být výslovný souhlas. Pokud chcete začít, [vytvořte nový účet v Azure Portal s povolenou úrovní Free](create-cosmosdb-resources-portal.md) nebo použijte [šablonu ARM](./manage-with-templates.md#free-tier). Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="try-azure-cosmos-db-for-free"></a>Vyzkoušejte si Azure Cosmos DB zdarma

[Vyzkoušejte si Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zdarma je bezplatné prostředí, které vám umožní experimentovat s Azure Cosmos DB v cloudu bez registrace účtu Azure nebo pomocí platební karty. Účty try Azure Cosmos DB jsou k dispozici po dobu omezeného času, aktuálně 30 dnů. Můžete je kdykoli obnovit. Vyzkoušejte si Azure Cosmos DB účty usnadňují vyhodnocení Azure Cosmos DB, sestavení a testování aplikace nebo použití rychlých startů nebo kurzů. Můžete také vytvořit ukázku, provést testování částí nebo dokonce vytvořit účet s více oblastmi a spustit aplikaci, aniž by to mělo za cenu. V účtu try Azure Cosmos DB můžete mít jednu sdílenou databázi propustnosti s maximálním počtem 25 kontejnerů a 20 000 RU/s propustností nebo jeden kontejner s až 5000 RU/s. Chcete-li začít, přečtěte si téma [vyzkoušet Azure Cosmos DB pro bezplatnou](https://azure.microsoft.com/try/cosmosdb/) stránku.

## <a name="azure-free-account"></a>Bezplatný účet Azure

Azure Cosmos DB je součástí [bezplatného účtu Azure](https://azure.microsoft.com/free), který nabízí za určité časové období kredity a prostředky Azure zdarma. Tento bezplatný účet Azure Cosmos DB konkrétně nabízí 5 GB úložiště a 400 Ruy zajištěné propustnosti po celý rok. Toto prostředí umožňuje všem vývojářům snadno testovat funkce Azure Cosmos DB nebo je integrovat s jinými službami Azure za nulové náklady. S bezplatným účtem Azure získáte za prvních 30 dní kredit $200. Nebudeme vám nic účtovat, ani když začnete používat služby, dokud nebudete chtít upgradovat. Začněte tím, že přejdete na stránku [bezplatný účet Azure](https://azure.microsoft.com/free) .

## <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB bez serveru

[Azure Cosmos DB bez serveru](serverless.md) vám umožní používat účet Azure Cosmos na základě spotřeby, kde se účtují jenom ty jednotky žádosti spotřebované vaší databázovou operací a úložiště spotřebované Vašimi daty. Při použití Azure Cosmos DB v režimu bez serveru se neúčtují žádné minimální poplatky. Vzhledem k tomu, že eliminuje pojem zřízené kapacity, je nejvhodnější pro vývojové nebo testovací aktivity konkrétně při nečinnosti databáze.

## <a name="use-shared-throughput-databases"></a>Použít sdílené databáze propustnosti

Ve [sdílené databázi propustnosti](set-throughput.md#set-throughput-on-a-database)jsou všechny kontejnery v databázi sdíleny zřízené propustnosti (ru/s) databáze. Pokud například zřizujete databázi s 400 RU/s a máte čtyři kontejnery, budou všechny čtyři kontejnery sdílet 400 RU/s. Ve vývojovém nebo testovacím prostředí, kde k každému kontejneru může být přistup méně často, a proto musí být nižší než minimum 400 RU/s, může při vkládání kontejnerů do sdílené databáze propustnosti docházet k optimalizaci nákladů.

Předpokládejme například, že váš vývojový nebo testovací účet má čtyři kontejnery. Pokud vytvoříte čtyři kontejnery s vyhrazenou propustností (minimálně 400 RU/s), vaše celkový počet RU/s bude 1600 RU/s. Naproti tomu, pokud vytvoříte sdílenou databázi propustnosti (minimálně 400 RU/s) a umístíte své kontejnery, vaše celkový počet RU/s bude pouze 400 RU/s. Obecně platí, že sdílené databáze propustnosti jsou skvělé pro scénáře, kdy nepotřebujete zajistit zaručenou propustnost pro jakýkoliv jednotlivý kontejner.  Přečtěte si další informace o [sdílených databázích propustnosti.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Další kroky

Můžete začít s používáním emulátoru nebo bezplatného Azure Cosmos DB účtů s následujícími články:

* Další informace o [Azure Cosmos DB vyúčtování](understand-your-bill.md)
* Další informace o [Azure Cosmos DB bez serveru](serverless.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](./optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos ve více oblastech](optimize-cost-regions.md)