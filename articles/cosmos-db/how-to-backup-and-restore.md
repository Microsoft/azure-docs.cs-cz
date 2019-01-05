---
title: Postup obnovení dat služby Azure Cosmos DB ze zálohy
description: Tento článek popisuje, jak obnovit data služby Azure Cosmos DB ze zálohy, jak kontaktovat podporu Azure o obnovení dat, kroky po obnovení dat.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e21a3ae23ca6a856c9524e52e80f33362f53cae1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040842"
---
# <a name="how-to-restore-azure-cosmos-db-data-from-a-backup"></a>Postup obnovení dat služby Azure Cosmos DB ze zálohy

Pokud omylem odstraníte, databáze nebo kontejner, můžete si [lístek podpory]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) nebo [obraťte se na podporu Azure]( https://azure.microsoft.com/support/options/) data obnovit z automatických záloh online. Podpora Azure je dostupný pro vybrané plány například pouze **standardní**, ** pro vývojáře a vyšší, než je plány. Podpora Azure není k dispozici **základní** plánu. Další informace o plánech podpory různých, najdete v článku [plánů podpory Azure](https://azure.microsoft.com/support/plans/) stránky. 

K obnovení konkrétní snímek zálohy, Azure Cosmos DB vyžaduje, že data jsou k dispozici po dobu trvání cyklu zálohování tohoto snímku.

## <a name="request-a-restore"></a>Žádost o obnovení

Než požádáte o obnovení byste měli mít následující podrobnosti:

* Máte připravený ID vašeho předplatného.

* Založené na tom, jak byla odstraněna nebo upravena data náhodně, měli byste mít další informace. Doporučuje se mít k dispozici informace o dopředu a zpět, které mohou být škodlivé v některých případech citlivé čas minimalizovat.

* Pokud se odstraní celý účet služby Azure Cosmos DB, musíte zadat název odstraněný účet. Pokud vytvořte další účet se stejným názvem jako odstraněný účet, sdílejte, které se na tým podpory pomáhá určit správný účet k výběru. Doporučujeme pro lístky žádostí o podporu různých souboru pro každý odstraněný účet, protože minimalizuje zmatek způsobený stavu obnovení.

* Pokud jeden nebo více databází se odstraní, by měla poskytnout účet Azure Cosmos, stejně jako názvy databází Azure Cosmos a určete, jestli nové databáze se stejným názvem existuje.

* Pokud jeden nebo více kontejnerů se odstraní, by měla poskytnout název účtu Azure Cosmos, názvy databáze a názvu kontejneru. A určete, zda existuje kontejner se stejným názvem.

Pokud dojde k poškození dat, a pokud se změnily nebo odstranily, dokumenty v rámci kontejneru **co nejdříve odstranit kontejner**. Tak, že odstraníte kontejner, se můžete vyhnout služby Azure Cosmos DB přepsání zálohy. Pokud z nějakého důvodu není možné odstranění, jste měli lístek co nejdříve. Kromě názvu účtu Azure Cosmos, názvy databází, názvy kolekcí měli byste určit bod v čase, ke kterému lze obnovit data. Je důležité, aby bylo co nejpřesnější, které pomáhají určit nejlepší dostupné zálohy v daném čase. Je také důležité určit čas ve standardu UTC. 

Následující snímek obrazovky ukazuje, jak vytvořit žádost o podporu pro container(collection/graph/table) k obnovení dat pomocí webu Azure portal. Poskytují další podrobnosti, jako je typ dat, cílem obnovení, čas při data byla odstraněna, aby nám pomohou určit prioritu žádosti.

![Vytvoření žádosti o podporu zálohování pomocí webu Azure portal](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Po obnovení akce

Po obnovení dat, dostanete oznámení o název nového účtu (je obvykle ve formátu `<original-name>-restored1`) a čas, kdy se obnovilo účet. Obnovené účtu budou mít stejné zřízená propustnost zásadám indexování a je ve stejné oblasti jako původní účet. Uživatel, který je správcem předplatného nebo coadmin můžete zobrazit obnovené účtu.

Po obnovení dat by měla kontrolovat a ověřování dat v obnovené účtu a ujistěte se, že obsahuje verze, které jste očekávali. Pokud vše vypadá v pořádku, by měl migrovat data zpět na původní účet pomocí [kanálu změn služby Azure Cosmos DB](change-feed.md) nebo [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

Doporučujeme odstranit kontejner nebo databáze okamžitě po provedení migrace data. Pokud neprovedete odstranění obnovenou databází nebo kontejnery, bude vám být naúčtovány náklady za jednotky žádostí, úložiště a výchozí přenos.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak migrovat data zpět do původního účtu pomocí následujících článcích:

* Chcete-li obnovení žádostí, obraťte se na podporu Azure, [lístek na webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Cosmos DB pomocí kanálu změn](change-feed.md) pro přesun dat do služby Azure Cosmos DB.

* [Použití Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) pro přesun dat do služby Azure Cosmos DB.
