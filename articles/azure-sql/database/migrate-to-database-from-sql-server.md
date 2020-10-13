---
title: Migrace databáze z SQL Serveru do Azure SQL Database
description: Přečtěte si o SQL Server migrace databáze do Azure SQL Database.
keywords: migrace databáze, migrace databáze systému sql server, nástroje pro migraci databáze, migrace databáze, migrace sql database
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/11/2019
ms.openlocfilehash: 06763624231fde344990da6d0a4639bcccdedf00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448877"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migrace databáze z SQL Serveru do Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto článku se dozvíte o primárních metodách migrace databáze SQL Server 2005 nebo novější na Azure SQL Database. Informace o migraci do spravované instance Azure SQL najdete v tématu [migrace Instance SQL Server do spravované instance Azure SQL](../managed-instance/migrate-to-instance-from-sql-server.md). Informace o migraci z jiných platforem najdete v tématu [Průvodce migrací databáze Azure](https://datamigration.microsoft.com/).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrace do izolovaných databází nebo databáze ve fondu

Existují dvě primární metody migrace Azure SQL Database databáze SQL Server 2005 nebo novější. První způsob je jednodušší, ale vyžaduje nějaké, potenciálně dlouhé, prostoje během migrace. Druhý způsob je složitější, ale podstatným způsobem eliminuje prostoje během migrace.

V obou případech je potřeba zajistit, aby byla zdrojová databáze kompatibilní s Azure SQL Database pomocí [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). SQL Database se blíží [paritě funkcí](features-comparison.md) s SQL Server, kromě problémů souvisejících s operacemi na úrovni serveru a mezi databázemi. Databáze a aplikace, které spoléhají na [částečně podporované nebo nepodporované funkce](transact-sql-tsql-differences-sql-server.md), potřebují určité [zpracování, aby se tyto nekompatibility opravily](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues) před migrací databáze SQL Serveru.

> [!NOTE]
> Pokud chcete migrovat databázi jiného typu než SQL Server, včetně databází Microsoft Access, Sybase, MySQL Oracle a DB2, na databázi Azure SQL Database, získáte informace v tématu [Pomocník s migrací na SQL Server](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Způsob 1: Migrace s prostoji během migrace

 Tuto metodu použijte k migraci do jedné nebo databáze ve fondu, pokud si můžete dovolit nějaké výpadky nebo testujete migraci produkční databáze pro pozdější migraci. Kurz najdete v tématu [migrace databáze SQL Server](../../dms/tutorial-sql-server-to-azure-sql.md).

Následující seznam obsahuje obecný pracovní postup pro migraci SQL Server databáze jedné nebo ve fondu databáze pomocí této metody. Migrace do spravované instance SQL najdete v tématu [migrace do spravované instance SQL](../managed-instance/migrate-to-instance-from-sql-server.md).

  ![Diagram migrace VSSSDT](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. [Vyhodnotit](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) kompatibilitu databáze pomocí nejnovější verze [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Příprava všech nezbytných oprav ve formě skriptů Transact-SQL.
3. Vytvořte v průběhu migrace do zdrojové databáze převedenou konzistentní kopii zdrojové databáze, která se migruje, nebo zastavte nové transakce, ke kterým dochází ve zdrojové databázi. Metody pro dosažení této druhé možnosti zahrnují zakázání připojení klienta nebo vytvoření [snímku databáze](https://msdn.microsoft.com/library/ms175876.aspx). Po dokončení migrace můžete pomocí transakční replikace aktualizovat migrované databáze o změny, ke kterým dojde po bodu přerušení migrace. Viz migrace [pomocí transakční migrace](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication).  
4. Nasazení skriptů Transact-SQL a aplikování oprav na kopii databáze.
5. [Migrujte](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) kopii databáze do nové databáze v Azure SQL Database pomocí Data Migration Assistant.

> [!NOTE]
> Místo použití přímého přístupu do paměti (DMA) můžete také použít soubor BACPAC. Viz část [Import souboru BacPac do nové databáze v Azure SQL Database](database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optimalizace výkonu přenosu dat během migrace

Následující seznam obsahuje doporučení pro zajištění nejlepšího výkonu během procesu importu.

- Vyberte nejvyšší úroveň služby a výpočetní velikost, které váš rozpočet umožní maximalizovat výkon přenosu. Po dokončení migrace můžete vertikálně snížit kapacitu a ušetřit tak peníze.
- Minimalizujte vzdálenost mezi souborem BACPAC a cílovým datovým centrem.
- Zakázat automatický statistiku během migrace
- Rozdělte tabulky a indexy na oddíly.
- Zrušte indexovaná zobrazení a po dokončení je znovu vytvořte.
- Odeberte zřídka dotazovaná historická data do jiné databáze a migrujte Tato historická data do samostatné databáze v Azure SQL Database. Potom můžete historická data dotazovat pomocí [elastických dotazů](elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Optimalizace výkonu po dokončení migrace

[Aktualizujte statistiky](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql) pomocí úplného prohledávání, jakmile se migrace dokončí.

## <a name="method-2-use-transactional-replication"></a>Způsob 2: Použití transakční replikace

Když si nemůžete dovolit odebrat svou SQL Server databázi z produkčního prostředí, když k migraci dojde, můžete jako řešení migrace použít transakční replikaci SQL Server. Abyste tento způsob mohli použít, zdrojová databáze musí splňovat [požadavky pro transakční replikaci](https://msdn.microsoft.com/library/mt589530.aspx) a musí být kompatibilní se službou Azure SQL Database. Informace o replikaci SQL s Always On najdete v tématu [Konfigurace replikace pro skupiny dostupnosti Always On (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Chcete-li použít toto řešení, nakonfigurujete databázi v Azure SQL Database jako předplatitele instance SQL Server, kterou chcete migrovat. Distributor transakční replikace synchronizuje data z databáze, která se má synchronizovat (vydavatel), zatímco se stále provádějí nové transakce.

Díky transakční replikaci se všechny změny dat nebo schématu zobrazí v databázi v Azure SQL Database. Jakmile se synchronizace dokončí a budete připraveni na migraci, změňte připojovací řetězec vašich aplikací tak, aby odkazoval na vaši databázi. Až transakční replikace vytáhne všechny zbývající změny ze zdrojové databáze a všechny vaše aplikace budou směrovat do databáze Azure, můžete transakční replikaci odinstalovat. Vaše databáze v Azure SQL Database je teď vaším produkčním systémem.

 ![Diagram přidání počátečních hodnot do cloudu pomocí transakční replikace](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> Transakční replikaci můžete také použít k migraci podmnožiny vaší zdrojové databáze. Publikaci, kterou replikujete do služby Azure SQL Database, můžete omezit na podmnožinu tabulek v replikované databázi. Pro každou replikovanou tabulku můžete data omezit na podmnožinu řádků nebo podmnožinu sloupců.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Pracovní postup migrace do služby SQL Database pomocí transakční replikace

> [!IMPORTANT]
> Použijte nejnovější verzi SQL Server Management Studio a udržujte si synchronizaci s aktualizacemi do Azure a SQL Database. Starší verze aplikace SQL Server Management Studio neumožňují nastavení služby SQL Database jako odběratele. [Aktualizovat aplikaci SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Nastavení distribuce
   - [Pomocí aplikace SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Pomocí jazyka Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. Vytvoření publikace
   - [Pomocí aplikace SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Pomocí jazyka Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. Vytvoření předplatného
   - [Pomocí aplikace SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/create-a-push-subscription/)
   - [Pomocí jazyka Transact-SQL](/sql/relational-databases/replication/create-a-push-subscription/)

Některé tipy a rozdíly pro migraci do služby SQL Database

- Použití místního distributora
  - Tím dojde k ovlivnění výkonu serveru.
  - Pokud je dopad na výkon nepřijatelný, můžete použít jiný server. Tím ale přispějete ke složitosti správy.
- Při výběru složky snímků se ujistěte, že je vybraná složka dostatečně velká pro uložení souborů .BACPAC všech tabulek, které chcete replikovat.
- Vytvoření snímku zamkne přidružené tabulky, dokud se nedokončí, takže svůj snímek vhodně naplánujte.
- Služba Azure SQL Database podporuje jenom nabízené odběry. Odběratele můžete přidat pouze ze zdrojové databáze.

## <a name="resolving-database-migration-compatibility-issues"></a>Řešení problémů s kompatibilitou při migrování databáze

V závislosti na verzi SQL Server ve zdrojové databázi a složitosti databáze, kterou migrujete, může dojít k nejrůznějším problémům s kompatibilitou. Starší verze systému SQL Server mají více problémů s kompatibilitou. Kromě cíleného prohledávání internetu s vybraným vyhledávacím strojem použijte následující prostředky:

- [Funkce databáze systému SQL Server nepodporované ve službě Azure SQL Database](transact-sql-tsql-differences-sql-server.md)
- [Ukončená funkce databázového stroje v systému SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Ukončená funkce databázového stroje v systému SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Ukončená funkce databázového stroje v systému SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Ukončená funkce databázového stroje v systému SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Ukončená funkce databázového stroje v systému SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Kromě vyhledávání v Internetu a používání těchto prostředků použijte [stránku s otázkou Microsoft Q&pro Azure SQL Database](https://docs.microsoft.com/answers/topics/azure-sql-database.html) nebo [StackOverflow](https://stackoverflow.com/).

> [!IMPORTANT]
> Azure SQL Managed instance umožňuje migrovat stávající instanci SQL Server a její databáze s minimálním počtem problémů s kompatibilitou. Podívejte [se, co je spravovaná instance](../managed-instance/sql-managed-instance-paas-overview.md).

## <a name="next-steps"></a>Další kroky

- Pomocí skriptu na blogu inženýrů Azure SQL EMEA můžete [monitorovat využití databáze tempdb během migrace](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Pomocí skriptu na blogu inženýrů Azure SQL EMEA můžete [monitorovat volné místo pro protokoly transakcí ve vaší databázi, zatímco probíhá migrace](https://docs.microsoft.com/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Informace o práci s časem UTC po migraci najdete v článku o [úpravě výchozího časového pásma na místní časové pásmo](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
- Informace o změně výchozího jazyka databáze po migraci najdete v článku o [změně výchozího jazyka služby Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).
 