---
title: Postup obnovení dat Azure Cosmos DB ze zálohy
description: Tento článek popisuje, jak obnovit Azure Cosmos DB data ze zálohy, jak kontaktovat podporu Azure za účelem obnovení dat, kroky, které je potřeba provést po obnovení dat.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1a0075f9b4fc3ff919d4db4bd440a5435d711c83
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261728"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Obnovení dat ze zálohy v Azure Cosmos DB 

Pokud databázi nebo kontejner omylem odstraníte, můžete si [vytvořit lístek podpory]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) nebo [zavolat podporu Azure]( https://azure.microsoft.com/support/options/) a obnovit data z automatických online zálohování. Podpora Azure je dostupná jenom pro vybrané plány, jako je **Standard**, **vývojář**a plány vyšší než. Podpora Azure není k dispozici u plánu **Basic** . Další informace o různých plánech podpory najdete na stránce [plány podpory Azure](https://azure.microsoft.com/support/plans/) . 

Aby bylo možné obnovit konkrétní snímek zálohy, Azure Cosmos DB vyžaduje, aby data byla k dispozici po dobu trvání cyklu zálohování pro daný snímek.

## <a name="request-a-restore"></a>Požadavek na obnovení

Před vyžádáním obnovení byste měli mít následující podrobnosti:

* Připravte si ID předplatného.

* Na základě toho, jak se data omylem odstranila nebo změnila, byste měli připravit na Další informace. Doporučuje se, abyste měli k dispozici informace pro minimalizaci back-in, které by mohly být v některých případech v případě citlivého času škodlivé.

* Pokud se odstraní celý účet Azure Cosmos DB, musíte zadat název odstraněného účtu. Pokud vytvoříte další účet se stejným názvem jako odstraněný účet, sdílejte ho s týmem podpory, protože pomáhá určit správný účet pro výběr. Doporučuje se pro každý odstraněný účet zaregistrovat různé lístky podpory, protože minimalizují nejasnost stavu obnovení.

* Pokud odstraníte jednu nebo více databází, měli byste poskytnout účet Azure Cosmos a také názvy databází Azure Cosmos a určit, jestli existuje nová databáze se stejným názvem.

* Pokud se odstraní aspoň jeden kontejner, měli byste zadat název účtu Azure Cosmos, názvy databází a názvy kontejnerů. A určete, zda kontejner se stejným názvem existuje.

* Pokud jste data omylem odstranili nebo jste poškodili, měli byste kontaktovat [podporu Azure](https://azure.microsoft.com/support/options/) do 8 hodin, aby tým Azure Cosmos DB vám mohl pomoci obnovit data ze zálohy.
  
  * Pokud jste databázi nebo kontejner omylem odstranili, otevřete případ podpory Azure závažnost B nebo závažnost C. 
  * Pokud jste omylem odstranili nebo poškodili některé dokumenty v rámci kontejneru, otevřete závažnost A případ podpory. 

Pokud dojde k poškození dat a pokud se dokumenty v rámci kontejneru upravují nebo odstraňují, **odstraňte kontejner co nejdříve**. Odstraněním kontejneru se můžete vyhnout Azure Cosmos DB přepsání záloh. Pokud z nějakého důvodu není odstranění možné, měli byste považovat lístek za co nejdříve. Kromě názvu účtu Azure Cosmos, názvů databází a názvů kontejnerů byste měli určit bod v čase, do kterého lze data obnovit. Je důležité, aby bylo co nejpřesněji možné určit nejlepší dostupné zálohy v daném čase. Je také důležité zadat čas ve standardu UTC. 

Následující snímek obrazovky ukazuje, jak vytvořit žádost o podporu pro kontejner (kolekce/graf/tabulka) pro obnovení dat pomocí Azure Portal. Zadejte další podrobnosti, jako je například typ dat, účel obnovení, čas odstranění dat, který nám pomůžete určit prioritu žádosti.

:::image type="content" source="./media/how-to-backup-and-restore/backup-support-request-portal.png" alt-text="Vytvoření žádosti o podporu zálohování pomocí Azure Portal":::

## <a name="post-restore-actions"></a>Akce po obnovení

Po obnovení dat se zobrazí oznámení o názvu nového účtu (obvykle ve formátu `<original-name>-restored1` ) a času, kdy byl účet obnoven. Obnovený účet bude mít stejnou zřízenou propustnost, zásady indexování a je ve stejné oblasti jako původní účet. Obnovený účet může zobrazit uživatel, který je správcem předplatného nebo spolusprávce.

Po obnovení dat byste měli zkontrolovat a ověřit data v obnoveném účtu a ujistit se, že obsahuje verzi, kterou očekáváte. Pokud vše vypadá dobře, měli byste migrovat data zpátky na původní účet pomocí [Azure Cosmos DB změnit informační kanál](change-feed.md) nebo [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

Doporučuje se odstranit kontejner nebo databázi hned po migraci dat. Pokud obnovené databáze nebo kontejnery neodstraníte, budou se vám účtovat náklady na jednotky, úložiště a výstupy žádostí.

## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte, jak migrovat data zpátky na původní účet pomocí následujících článků:

* Pokud chcete vytvořit žádost o obnovení, obraťte se na podporu Azure, poznamenejte si [lístek z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* K přesunu dat do Azure Cosmos DB [použijte Cosmos DB změnit informační kanál](change-feed.md) .

* K přesunu dat do Azure Cosmos DB [použijte Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) .
