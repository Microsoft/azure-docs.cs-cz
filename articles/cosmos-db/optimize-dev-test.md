---
title: Optimalizace pro vývoj a testování v Azure Cosmos DB
description: Tento článek vysvětluje, jak Azure Cosmos DB nabízí více možností pro vývoj a testování služby zdarma.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 992d165d323aab79bb7b5475aa396d4432691530
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754904"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimalizujte náklady na vývoj a testování v Azure Cosmos DB

Tento článek popisuje různé možnosti použití Azure Cosmos DB pro vývoj a testování bez jakýchkoli nákladů.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulátor Azure Cosmos DB (místně zaváděná verze)

[Emulátor Azure Cosmos DB](local-emulator.md) je místní verze ke stažení, která napodobuje cloudovou službu Azure Cosmos DB. Kód, který používá rozhraní API Azure Cosmos DB, můžete napsat a otestovat i v případě, že nemáte žádné síťové připojení a neúčtujete náklady. Emulátor Azure Cosmos DB poskytuje místní prostředí pro účely vývoje s vysokou přesností pro cloudovou službu. Svou aplikaci můžete vyvíjet a testovat místně, aniž byste museli vytvářet předplatné Azure. Až budete připraveni k nasazení aplikace do cloudu, aktualizujte připojovací řetězec pro připojení ke koncovému bodu Azure Cosmos DB v cloudu, nepotřebujete žádné další úpravy. Můžete také [nastavit kanál CI/CD s úlohou sestavení emulátoru Azure Cosmos DB](tutorial-setup-ci-cd.md) ve službě Azure DevOps pro spouštění testů. Můžete začít tím, že navštívíte článek [Azure Cosmos DB emulátoru](local-emulator.md) .

## <a name="try-azure-cosmos-db-for-free"></a>Vyzkoušejte si Azure Cosmos DB zdarma

[Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) je bezplatné prostředí, které vám umožní vytvořit databázi a kolekce a experimentovat s Azure Cosmos DB v cloudu. Nemusíte se přihlašovat k Azure ani platit žádné náklady. Účty try Azure Cosmos DB jsou k dispozici po dobu omezeného času, aktuálně 30 dnů. Můžete je kdykoli obnovit. Zkuste Azure Cosmos DB účty usnadňují vyhodnocení Azure Cosmos DB, sestavení a otestování aplikace pomocí rychlých startů nebo kurzů. Můžete vytvořit ukázku nebo provést testování částí bez jakýchkoli nákladů. Pomocí možnosti vyzkoušet Azure Cosmos DB u bezplatných účtů můžete vyhodnotit Azure Cosmos DB prémiové funkce Premium zdarma, včetně globálních distribucí, SLA a modelů konzistence klíč. Můžete vytvořit databázi s maximálním počtem 25 Cosmosch kontejnerů Azure a propustností 10 000 RU/s. Ukázkovou aplikaci můžete spustit bez přihlášení k odběru účtu Azure nebo pomocí platební karty. Pomocí vyzkoušet Azure Cosmos DB zdarma můžete vytvořit účet Azure Cosmos s více oblastmi a v něm spustit aplikaci během několika minut. Chcete-li začít, přečtěte si téma [vyzkoušet Azure Cosmos DB pro bezplatnou](https://azure.microsoft.com/try/cosmosdb/) stránku.

## <a name="azure-free-account"></a>Bezplatný účet Azure

Azure Cosmos DB je součástí [bezplatného účtu Azure](https://azure.microsoft.com/free), který nabízí za určité časové období kredity a prostředky Azure zdarma. Tento bezplatný účet Azure Cosmos DB konkrétně nabízí 5 GB úložiště a 400 Ruy zajištěné propustnosti po celý rok. Toto prostředí umožňuje všem vývojářům snadno testovat funkce Azure Cosmos DB nebo je integrovat s jinými službami Azure za nulové náklady. S bezplatným účtem Azure získáte za prvních 30 dní kredit $200. Nebudeme vám nic účtovat, ani když začnete používat služby, dokud nebudete chtít upgradovat. Začněte tím, že přejdete na stránku [bezplatný účet Azure](https://azure.microsoft.com/free) .

## <a name="next-steps"></a>Další kroky

Můžete začít s používáním emulátoru nebo bezplatného Azure Cosmos DB účtů s následujícími články:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [Azure Cosmos DB vyúčtování](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos ve více oblastech](optimize-cost-regions.md)

