---
title: Online zálohování a obnovení dat na vyžádání v Azure Cosmos DB
description: Tento článek popisuje, jak funguje automatické obnovení dat online zálohování a na vyžádání v Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 4ca4fa8699d9bd4b35f26983f2f7004c63da180f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441545"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Online zálohování a obnovení dat na vyžádání v Azure Cosmos DB

Azure Cosmos DB automaticky provede zálohování vašich dat v pravidelných intervalech. Automatické zálohování se provádí bez vlivu na výkon nebo dostupnost databázových operací. Všechny zálohy se ukládají samostatně ve službě úložiště a tyto zálohy se globálně replikují z hlediska odolnosti proti regionálním katastrofám. Automatické zálohování jsou užitečná ve scénářích, kdy omylem odstraníte nebo aktualizujete svůj účet, databázi nebo kontejner Azure Cosmos a později vyžadujete obnovení dat.

## <a name="automatic-and-online-backups"></a>Automatické zálohování a zálohování online

Díky Azure Cosmos DB, nejen k datům, ale také zálohování dat je vysoce redundantní a odolné vůči regionálním katastrofám. Následující kroky ukazují, jak Azure Cosmos DB provádí zálohování dat:

* Azure Cosmos DB automaticky vytváří zálohy vaší databáze každé 4 hodiny a vždy se uchovávají pouze poslední 2 zálohy. Pokud se však kontejner nebo databáze odstraní, Azure Cosmos DB zachová stávající snímky daného kontejneru nebo databáze po dobu 30 dnů.

* Azure Cosmos DB ukládá tyto zálohy do úložiště objektů BLOB v Azure, zatímco skutečná data se nacházejí lokálně v rámci Azure Cosmos DB.

*  Aby se zajistila nízká latence, je snímek zálohy uložený ve službě Azure Blob Storage ve stejné oblasti jako aktuální oblast zápisu (nebo jedna z oblastí zápisu pro případ, že máte k dispozici více hlavních konfigurací) účtu databáze Azure Cosmos. Z důvodu odolnosti proti regionální havárii se každý snímek zálohovaných dat ve službě Azure Blob Storage znovu replikuje do jiné oblasti prostřednictvím geograficky redundantního úložiště (GRS). Oblast, do které je záloha replikována, je založena na zdrojové oblasti a na místní páru přidružené ke zdrojové oblasti. Další informace najdete v článku [seznam geograficky redundantních párů oblastí Azure](../best-practices-availability-paired-regions.md) . K této záloze nemůžete přistupovat přímo. Azure Cosmos DB použije tuto zálohu pouze v případě, že je zahájeno obnovení zálohy.

* Zálohy budou provedeny, aniž by to ovlivnilo výkon nebo dostupnost vaší aplikace. Azure Cosmos DB provádí zálohování dat na pozadí bez toho, aby využívala žádnou další zřízenou propustnost (ru) nebo ovlivnila výkon a dostupnost vaší databáze.

* Pokud jste data omylem odstranili nebo jste poškodili, měli byste kontaktovat [podporu Azure](https://azure.microsoft.com/support/options/) do 8 hodin, aby tým Azure Cosmos DB vám mohl pomoci obnovit data ze zálohy.

Následující obrázek ukazuje, jak se kontejner Azure Cosmos se všemi třemi primárními fyzickými oddíly v Západní USA zálohuje do vzdáleného účtu Azure Blob Storage v Západní USA a pak se replikuje do Východní USA:

![Periodické úplné zálohování všech entit Cosmos DB v GRS Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Možnosti správy vlastních záloh

Pomocí Azure Cosmos DB účtů rozhraní SQL API můžete také spravovat vlastní zálohy pomocí jednoho z následujících přístupů:

* Pomocí [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) můžete data pravidelně přesouvat do úložiště podle vašeho výběru.

* Pomocí Azure Cosmos DBho [kanálu změny](change-feed.md) můžete pravidelně číst data pro úplné zálohování a také pro přírůstkové změny a ukládat je do vlastního úložiště.

## <a name="backup-retention-period"></a>Doba uchování zálohy

Azure Cosmos DB přebírá snímky dat každé čtyři hodiny. V jednom okamžiku se zachová jenom poslední dva snímky. Pokud se však kontejner nebo databáze odstraní, Azure Cosmos DB zachová stávající snímky daného kontejneru nebo databáze po dobu 30 dnů.

## <a name="restoring-data-from-online-backups"></a>Obnovení dat z online zálohování

Náhodné odstranění nebo změna dat může nastat v jednom z následujících scénářů:  

* Celý účet Azure Cosmos se odstraní.

* Nejméně jedna databáze Azure Cosmos se odstranila.

* Jeden nebo více kontejnerů Azure Cosmos se odstranilo.

* Položky Azure Cosmos (například dokumenty) v rámci kontejneru se odstraní nebo upraví. Tento konkrétní případ se obvykle označuje jako "poškození dat".

* Sdílená databáze nabídek nebo kontejnery v rámci sdílené databáze nabídek se odstranily nebo jsou poškozené.

Azure Cosmos DB může obnovit data ve všech výše uvedených scénářích. V rámci procesu obnovení se vždy vytvoří nový účet služby Azure Cosmos DB pro uložení obnovených dat. Název nového účtu, pokud není zadán, bude mít formát `<Azure_Cosmos_account_original_name>-restored1`. Poslední číslice se zvýší, pokud dojde k pokusu o více obnovení. Nemůžete obnovit data na předem vytvořený účet Azure Cosmos.

Když se odstraní účet Azure Cosmos, můžeme data obnovit na účet se stejným názvem, za předpokladu, že se název účtu nepoužívá. V takových případech se po odstranění doporučuje účet znovu vytvořit, protože to neumožňuje, aby obnovená data používala stejný název, ale také usnadňuje zjišťování správného účtu pro obnovení z obtížnější. 

Po odstranění databáze Azure Cosmos je možné obnovit celou databázi nebo podmnožinu kontejnerů v této databázi. Je také možné vybrat kontejnery napříč databázemi a obnovit je a všechna obnovená data se umístí do nového účtu Azure Cosmos.

Když se jedna nebo víc položek v rámci kontejneru omylem odstraní nebo změní (případ poškození dat), budete muset zadat dobu, po kterou se má obnovit. Čas je pro tento případ podstatou. Vzhledem k tomu, že je kontejner živý, je zálohování stále spuštěné, takže pokud počkáte mimo dobu uchování (výchozí nastavení je osm hodin), zálohy se přepíší. V případě odstranění již nejsou data nadále ukládána, protože nejsou přepsána cyklem zálohování. Zálohy pro odstraněné databáze nebo kontejnery se ukládají po dobu 30 dnů.

Pokud zřizujete propustnost na úrovni databáze (tj. kde sada kontejnerů sdílí zřízenou propustnost), proces zálohování a obnovení v tomto případě proběhne na úrovni celé databáze, a ne na úrovni jednotlivých kontejnerů. V takových případech výběr podmnožiny kontejnerů k obnovení není možnost.

## <a name="migrating-data-to-the-original-account"></a>Migrace dat do původního účtu

Primárním cílem obnovování dat je poskytnout způsob, jak obnovit jakákoli neúmyslná data, která odstraníte nebo upravíte. Proto doporučujeme, abyste nejdřív zkontrolovali obsah obnovených dat, abyste se ujistili, že obsahuje, co očekáváte. Pak můžete pracovat na migraci dat zpět do primárního účtu. I když je možné použít obnovený účet jako živý účet, není doporučená možnost, pokud máte produkční úlohy.  

Níže jsou různé způsoby, jak migrovat data zpátky na původní účet Azure Cosmos:

* Použití [Nástroje pro migraci dat Cosmos DB](import-data.md)
* Použití [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Použití [kanálu změn](change-feed.md) v Azure Cosmos DB 
* Napsat vlastní kód

Odstraňte obnovené účty hned po dokončení migrace, protože budou platit průběžné poplatky.

## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte, jak obnovit data z účtu Azure Cosmos, nebo se dozvíte, jak migrovat data na účet Azure Cosmos.

* Pokud chcete vytvořit žádost o obnovení, obraťte se na podporu Azure, poznamenejte si [lístek z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Jak obnovit data z účtu Azure Cosmos](how-to-backup-and-restore.md)
* K přesunu dat do Azure Cosmos DB [použijte Cosmos DB změnit informační kanál](change-feed.md) .
* K přesunu dat do Azure Cosmos DB [použijte Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) .

