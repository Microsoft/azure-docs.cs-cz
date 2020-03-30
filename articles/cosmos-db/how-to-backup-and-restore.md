---
title: Jak obnovit data Azure Cosmos DB ze zálohy
description: Tento článek popisuje, jak obnovit data Azure Cosmos DB ze zálohy, jak kontaktovat podporu Azure k obnovení dat, kroky, které je třeba provést po obnovení dat.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 19ca835ca8211202cd358ac2ec3695675183a372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70240767"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Obnovení dat ze zálohy v Azure Cosmos DB 

Pokud omylem odstraníte databázi nebo kontejner, můžete [soubor lístek podpory]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) nebo volání podpory [Azure]( https://azure.microsoft.com/support/options/) obnovit data z automatického zálohování online. Podpora Azure je dostupná pro vybrané plány, jako je **Standard**, **Developer**a plány vyšší než oni. Podpora Azure není k dispozici se **základním** plánem. Další informace o různých plánech podpory najdete na stránce [plány podpory Azure.](https://azure.microsoft.com/support/plans/) 

Chcete-li obnovit konkrétní snímek zálohy, Azure Cosmos DB vyžaduje, aby data je k dispozici po dobu trvání cyklu zálohování pro tento snímek.

## <a name="request-a-restore"></a>Žádost o obnovení

Před žádostí o obnovení byste měli mít následující podrobnosti:

* Připravte si ID předplatného.

* Na základě toho, jak byla vaše data omylem odstraněna nebo změněna, byste se měli připravit na další informace. Doporučuje se, že máte informace, které jsou k dispozici dopředu, aby se minimalizovalo tam a zpět, které mohou být škodlivé v některých případech citlivé na čas.

* Pokud se odstraní celý účet Azure Cosmos DB, musíte zadat název odstraněného účtu. Pokud vytvoříte jiný účet se stejným názvem jako odstraněný účet, sdílejte jej s týmem podpory, protože pomáhá určit správný účet, který si vybrat. Doporučujese soubor různých lístků podpory pro každý odstraněný účet, protože minimalizuje nejasnosti stavu obnovení.

* Pokud se odstraní jedna nebo více databází, měli byste poskytnout účet Azure Cosmos, stejně jako názvy databází Azure Cosmos a určit, pokud existuje nová databáze se stejným názvem.

* Pokud se odstraní jeden nebo více kontejnerů, měli byste zadat název účtu Azure Cosmos, názvy databází a názvy kontejnerů. A určete, zda existuje kontejner se stejným názvem.

* Pokud jste omylem odstranili nebo poškodili vaše data, měli byste kontaktovat [podporu Azure](https://azure.microsoft.com/support/options/) do 8 hodin, aby vám tým Azure Cosmos DB mohl pomoct obnovit data ze záloh.
  
  * Pokud jste omylem odstranili databázi nebo kontejner, otevřete případ podpory Sev B nebo Sev C Azure. 
  * Pokud jste omylem odstranili nebo poškodili některé dokumenty v kontejneru, otevřete případ podpory Sev A. 

Dojde-li k poškození dat a pokud jsou dokumenty v kontejneru změněny nebo odstraněny, **odstraňte kontejner co nejdříve**. Odstraněním kontejneru můžete zabránit Azure Cosmos DB z přepsání záloh. Pokud z nějakého důvodu odstranění není možné, měli byste co nejdříve podat lístek. Kromě názvu účtu Azure Cosmos, názvů databází, názvů kontejnerů byste měli určit bod v čase, do kterého lze data obnovit. Je důležité být co nejpřesnější, aby nám pomohli určit nejlepší dostupné zálohy v té době. Je také důležité zadat čas v UTC. 

Následující snímek obrazovky ukazuje, jak vytvořit žádost o podporu pro kontejner (kolekce/graf/tabulka) k obnovení dat pomocí portálu Azure. Poskytněte další podrobnosti, jako je typ dat, účel obnovení, čas, kdy byla data odstraněna, abychom pomohli určit prioritu požadavku.

![Vytvoření žádosti o podporu zálohování pomocí portálu Azure Portal](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Akce po obnovení

Po obnovení dat se zobrazí oznámení o názvu nového účtu (obvykle ve `<original-name>-restored1`formátu) a o době, kdy byl účet obnoven. Obnovený účet bude mít stejnou zřízenou propustnost, zásady indexování a je ve stejné oblasti jako původní účet. Obnovený účet může zobrazit uživatel, který je správcem předplatného nebo coadminem.

Po obnovení dat byste měli zkontrolovat a ověřit data v obnoveném účtu a ujistěte se, že obsahují verzi, kterou očekáváte. Pokud vše vypadá dobře, měli byste migrovat data zpět do původního účtu pomocí [zdroje změn Azure Cosmos DB](change-feed.md) nebo Azure Data [Factory](../data-factory/connector-azure-cosmos-db.md).

Doporučujeme odstranit kontejner nebo databázi ihned po migraci dat. Pokud obnovené databáze nebo kontejnery neodstraníte, budou jim účtovány náklady na jednotky požadavku, úložiště a odchozí přenos.

## <a name="next-steps"></a>Další kroky

Dále se dozvíte, jak migrovat data zpět do původního účtu pomocí následujících článků:

* Pokud chcete podat žádost o obnovení, kontaktujte podporu Azure, [nasuzte lístek z webu Azure Portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Pomocí informačního kanálu o změně Cosmos DB](change-feed.md) přesuňte data do Služby Azure Cosmos DB.

* [Pomocí Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) přesuňte data do Azure Cosmos DB.
