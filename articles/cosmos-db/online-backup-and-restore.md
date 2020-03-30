---
title: Zálohování online a obnovení dat na vyžádání v Azure Cosmos DB
description: Tento článek popisuje, jak automatické, online zálohování a obnovení dat na vyžádání funguje v Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: f5cc4339d7d6dce6d49c8d3eb744fca7fa5774d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240430"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Zálohování online a obnovení dat na vyžádání v Azure Cosmos DB

Azure Cosmos DB automaticky přebírá zálohy vašich dat v pravidelných intervalech. Automatické zálohování jsou prováděny bez ovlivnění výkonu nebo dostupnostdatabázových operací. Všechny zálohy jsou uloženy samostatně ve službě úložiště a tyto zálohy jsou globálně replikovány pro odolnost proti regionálním haváriím. Automatické zálohování jsou užitečné ve scénářích, kdy omylem odstraníte nebo aktualizujete svůj účet, databázi nebo kontejner Azure Cosmos a později budete vyžadovat obnovení dat.

## <a name="automatic-and-online-backups"></a>Automatické zálohování a zálohování online

S Azure Cosmos DB jsou nejen vaše data, ale také zálohy vašich dat vysoce redundantní a odolné vůči regionálním katastrofám. Následující kroky ukazují, jak Azure Cosmos DB provádí zálohování dat:

* Azure Cosmos DB automaticky vytváří zálohy vaší databáze každé 4 hodiny a vždy se uchovávají pouze poslední 2 zálohy. Pokud se však kontejner nebo databáze odstraní, Azure Cosmos DB zachová stávající snímky daného kontejneru nebo databáze po dobu 30 dnů.

* Azure Cosmos DB ukládá tyto zálohy v úložišti objektů blob Azure, zatímco skutečná data se nacházejí místně v rámci Azure Cosmos DB.

*  Chcete-li zaručit nízkou latenci, snímek zálohy se ukládá v úložišti objektů Blob Azure ve stejné oblasti jako aktuální oblast zápisu (nebo jedna z oblastí zápisu, v případě, že máte konfiguraci s více servery) vašeho databázového účtu Azure Cosmos. Z důvodu odolnosti proti regionální havárii je každý snímek dat zálohování v úložišti objektů Blob Azure znovu replikován do jiné oblasti prostřednictvím geograficky redundantního úložiště (GRS). Oblast, do které je replikována záloha je založena na zdrojové oblasti a regionální dvojice přidružené ke zdrojové oblasti. Další informace najdete v [článku seznam geograficky redundantních párů oblastí Azure.](../best-practices-availability-paired-regions.md) K této záloze nelze získat přímý přístup. Azure Cosmos DB použije tuto zálohu jenom v případě, že je zahájeno obnovení zálohování.

* Zálohy jsou převzaty bez ovlivnění výkonu nebo dostupnosti aplikace. Azure Cosmos DB provádí zálohování dat na pozadí bez využití jakékoli další zřízené propustnosti (RU) nebo ovlivnění výkonu a dostupnosti databáze.

* Pokud jste omylem odstranili nebo poškodili vaše data, měli byste kontaktovat [podporu Azure](https://azure.microsoft.com/support/options/) do 8 hodin, aby vám tým Azure Cosmos DB mohl pomoct obnovit data ze záloh.

Následující obrázek ukazuje, jak je kontejner Azure Cosmos se všemi třemi primárními fyzickými oddíly v západní ČÁSTI USA zálohován ve vzdáleném účtu úložiště objektů blob Azure v západní ČÁSTI USA a pak replikován do usa – východ:

![Pravidelné úplné zálohování všech entit Cosmos DB v úložišti GRS Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Možnosti správy vlastních záloh

S účty Azure Cosmos DB SQL API můžete také udržovat vlastní zálohy pomocí jednoho z následujících přístupů:

* Pomocí [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) můžete pravidelně přesouvat data do úložiště podle vašeho výběru.

* Pomocí [zdroje změn](change-feed.md) Azure Cosmos DB můžete pravidelně číst data pro úplné zálohy i pro přírůstkové změny a ukládat je do vlastního úložiště.

## <a name="backup-retention-period"></a>Doba uchovávání záloh

Azure Cosmos DB pořizuje snímky vašich dat každé čtyři hodiny. V daném okamžiku jsou zachovány pouze poslední dva snímky. Pokud se však kontejner nebo databáze odstraní, Azure Cosmos DB zachová stávající snímky daného kontejneru nebo databáze po dobu 30 dnů.

## <a name="restoring-data-from-online-backups"></a>Obnovení dat ze záloh online

K náhodnému odstranění nebo změně dat může dojít v jednom z následujících scénářů:  

* Celý účet Azure Cosmos se odstraní

* Jedna nebo více databází Azure Cosmos se odstraní

* Jeden nebo více kontejnerů Azure Cosmos se odstraní

* Položky Azure Cosmos (například dokumenty) v rámci kontejneru se odstraní nebo upraví. Tento konkrétní případ se obvykle označuje jako "poškození dat".

* Databáze sdílené nabídky nebo kontejnery v rámci databáze sdílených nabídek jsou odstraněny nebo poškozeny.

Azure Cosmos DB můžete obnovit data ve všech výše uvedených scénářích. V rámci procesu obnovení se vždy vytvoří nový účet služby Azure Cosmos DB pro uložení obnovených dat. Název nového účtu, pokud není zadán, `<Azure_Cosmos_account_original_name>-restored1`bude mít formát . Poslední číslice se zintážíte, pokud dojde k pokusu o více obnovení. Data nelze obnovit do předem vytvořeného účtu Azure Cosmos.

Když se odstraní účet Azure Cosmos, můžeme obnovit data do účtu se stejným názvem, za předpokladu, že název účtu není používán. V takových případech se doporučuje nevytvářet účet znovu po odstranění, protože nejen zabraňuje obnoveným datům používat stejný název, ale také ztěžuje zjišťování správného účtu, který chcete obnovit. 

Při odstranění databáze Azure Cosmos je možné obnovit celou databázi nebo podmnožinu kontejnerů v rámci této databáze. Je také možné vybrat kontejnery napříč databázemi a obnovit je a všechna obnovená data se umístí do nového účtu Azure Cosmos.

Pokud dojde k náhodnému odstranění nebo změně jedné nebo více položek v kontejneru (případ poškození dat), budete muset zadat čas, který chcete obnovit. Čas je pro tento případ důležitý. Vzhledem k tomu, že kontejner je aktivní, zálohování je stále spuštěna, takže pokud budete čekat po dobu uchování (výchozí hodnota je osm hodin), zálohy by být přepsány. V případě odstranění již nejsou data uložena, protože nebudou přepsána cyklem zálohování. Zálohy pro odstraněné databáze nebo kontejnery jsou uloženy po dobu 30 dnů.

Pokud zřídíte propustnost na úrovni databáze (to znamená, kde sada kontejnerů sdílí zřízenou propustnost), proces zálohování a obnovení v tomto případě proběhne na úrovni celé databáze a nikoli na úrovni jednotlivých kontejnerů. V takových případech není možné vybrat podmnožinu kontejnerů, které chcete obnovit.

## <a name="migrating-data-to-the-original-account"></a>Migrace dat do původního účtu

Primárním cílem obnovení dat je poskytnout způsob, jak obnovit všechna data, která odstraníte nebo omylem upravíte. Proto doporučujeme nejprve zkontrolovat obsah obnovených dat, abyste se ujistili, že obsahuje to, co očekáváte. Pak pracujte na migraci dat zpět na primární účet. I když je možné použít obnovený účet jako živý účet, není to doporučená možnost, pokud máte produkční úlohy.  

Níže jsou uvedeny různé způsoby migrace dat zpět do původního účtu Azure Cosmos:

* Použití [nástroje pro migraci dat Cosmos DB](import-data.md)
* Použití [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Použití [kanálu změn](change-feed.md) v Azure Cosmos DB 
* Napsat vlastní kód

Obnovené účty odstraňte ihned po dokončení migrace, protože jim budou účtovány průběžné poplatky.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak obnovit data z účtu Azure Cosmos nebo se dozvíte, jak migrovat data do účtu Azure Cosmos

* Pokud chcete podat žádost o obnovení, kontaktujte podporu Azure, [nasuzte lístek z webu Azure Portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Jak obnovit data z účtu Azure Cosmos](how-to-backup-and-restore.md)
* [Pomocí informačního kanálu o změně Cosmos DB](change-feed.md) přesuňte data do Služby Azure Cosmos DB.
* [Pomocí Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) přesuňte data do Azure Cosmos DB.

