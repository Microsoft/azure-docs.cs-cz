---
title: "Migrace databáze SQL Serveru do služby Azure SQL Database | Dokumentace Microsoftu"
description: "V této části se dozvíte, jak migrovat místní databázi SQL Serveru do služby Azure SQL Database v cloudu."
keywords: "migrace databáze, migrace databáze systému sql server, nástroje pro migraci databáze, migrace databáze, migrace sql database"
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: article
ms.date: 03/07/2018
ms.author: carlrab
ms.openlocfilehash: 7892c74f03647e289ed96055fb9db5c2f08f0234
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migrace databáze SQL serveru do Azure SQL Database

V tomto článku informace o primární metody pro migraci systému SQL Server 2005 nebo novější databáze na databázi jednoho nebo ve fondu ve službě Azure SQL Database. Informace o migraci do Instance spravované najdete v tématu [migrace do instance systému SQL Server do Azure SQL Database spravované Instance (preview)](sql-database-managed-instance-migrate.md). 

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrace do jedné databáze nebo databáze ve fondu
Existují dvě primární metody pro migraci databázi systému SQL Server 2005 nebo vyšší na jednom nebo ve fondu databáze ve službě Azure SQL Database. První způsob je jednodušší, ale vyžaduje nějaké, potenciálně dlouhé, prostoje během migrace. Druhý způsob je složitější, ale podstatným způsobem eliminuje prostoje během migrace.

V obou případech je potřeba zajistit, že zdrojová databáze je kompatibilní s Azure SQL Database pomocí [pomocníka pro migraci dat (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). Databáze SQL verze 12 dosahuje [parita funkce](sql-database-features.md) se systémem SQL Server, než problémy týkající se operací na úrovni serveru a databáze mezi. Databáze a aplikace, které spoléhají na [částečně podporované nebo nepodporované funkce](sql-database-transact-sql-information.md), potřebují určité [zpracování, aby se tyto nekompatibility opravily](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues) před migrací databáze SQL Serveru.

> [!NOTE]
> Pokud chcete migrovat databázi jiného typu než SQL Server, včetně databází Microsoft Access, Sybase, MySQL Oracle a DB2, na databázi Azure SQL Database, získáte informace v tématu [Pomocník s migrací na SQL Server](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).
> 

### <a name="method-1-migration-with-downtime-during-the-migration"></a>Způsob 1: Migrace s prostoji během migrace

 Tuto metodu použijte k migraci na jednu, nebo databázi ve fondu, pokud si může dovolit výpadky nebo novější migraci provádíte testovací migraci provozní databáze. Podívejte se kurz [migrovat databázi systému SQL Server](sql-database-migrate-your-sql-server-database.md).

Následující seznam obsahuje obecné pracovní postup pro migraci databáze systému SQL Server je jedna nebo databázi ve fondu pomocí této metody. Migrace na spravované Instance, najdete v části [migrace do Instance spravované](sql-database-cloud-migrate.md#migration-to-azure-sql-database-managed-instance).

  ![Diagram migrace VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Vyhodnocení](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) databázi pro kompatibilitu s použitím nejnovější verzi [Pomocníka pro migraci dat (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Příprava všech nezbytných oprav ve formě skriptů Transact-SQL.
3. Vytvořit kopii migrovaného zdrojové databáze stavu transakční konzistence nebo zastavení nové transakce z ke kterým dochází v databázi zdrojové během migrace je. Metody k provedení této druhé možnosti patří zakázání připojení klientů nebo vytváření [snímek databáze](https://msdn.microsoft.com/library/ms175876.aspx). Po migraci bude pravděpodobně možné použít k aktualizaci migrované databáze s změny, ke kterým došlo po přerušení bodu migrace transakční replikace. V tématu [migrovat pomocí migrace transakcí](sql-database-cloud-migrate.md#method-2-use-transactional-replication).  
4. Nasazení skriptů Transact-SQL a aplikování oprav na kopii databáze.
5. [Migrace](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) pro novou databázi SQL Azure pomocí Pomocníka pro migraci dat kopie databáze.

> [!NOTE]
> Místo pomocí DMA, můžete také použít soubor souboru BACPAC. V tématu [importovat soubor souboru BACPAC pro novou databázi SQL Azure](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optimalizace výkonu přenosu dat během migrace 

Následující seznam obsahuje doporučení pro zajištění nejlepšího výkonu během procesu importu.

* Pokud chcete maximalizovat výkon přenosu, zvolte nejvyšší úroveň služby a úroveň výkonu, jaké vám rozpočet dovolí. Po dokončení migrace můžete vertikálně snížit kapacitu a ušetřit tak peníze. 
* Minimalizaci vzdálenosti mezi souboru souboru BACPAC a cílového datového centra.
* Zakažte během migrace automatické statistiky.
* Rozdělte tabulky a indexy na oddíly.
* Zrušte indexovaná zobrazení a po dokončení je znovu vytvořte.
* Odeberte zřídka dotazovaná historická data do jiné databáze a migrujte je do samostatné databáze SQL Azure. Potom můžete historická data dotazovat pomocí [elastických dotazů](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Optimalizace výkonu po dokončení migrace

[Aktualizujte statistiky](https://msdn.microsoft.com/library/ms187348.aspx) pomocí úplného prohledávání, jakmile se migrace dokončí.

### <a name="method-2-use-transactional-replication"></a>Způsob 2: Použití transakční replikace

Pokud si během migrace nemůžete dovolit stažení databáze SQL Serveru z produkčního prostředí, můžete jako řešení migrace použít transakční replikaci SQL Serveru. Abyste tento způsob mohli použít, zdrojová databáze musí splňovat [požadavky pro transakční replikaci](https://msdn.microsoft.com/library/mt589530.aspx) a musí být kompatibilní se službou Azure SQL Database. Informace o replikaci SQL s funkcí AlwaysOn najdete v tématu [konfiguraci replikace pro skupin dostupnosti Always On (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Toto řešení použijete tak, že nakonfigurujete službu Azure SQL Database jako odběratele instance SQL Serveru, kterou chcete migrovat. Distributor transakční replikace synchronizuje data z databáze, která se má synchronizovat (vydavatel), zatímco se stále provádějí nové transakce. 

U transakční replikace se všechny změny dat nebo schématu objeví i ve službě Azure SQL Database. Jakmile se synchronizace dokončí a budete připraveni k migraci, změňte připojovací řetězec vašich aplikací tak, aby je směroval do služby Azure SQL Database. Až transakční replikace vytáhne všechny zbývající změny ze zdrojové databáze a všechny vaše aplikace budou směrovat do databáze Azure, můžete transakční replikaci odinstalovat. Vaším produkčním systémem je nyní služba Azure SQL Database.

 ![Diagram přidání počátečních hodnot do cloudu pomocí transakční replikace](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> Transakční replikaci můžete také použít k migraci podmnožiny vaší zdrojové databáze. Publikaci, kterou replikujete do služby Azure SQL Database, můžete omezit na podmnožinu tabulek v replikované databázi. Pro každou replikovanou tabulku můžete data omezit na podmnožinu řádků nebo podmnožinu sloupců.
>

### <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Pracovní postup migrace do služby SQL Database pomocí transakční replikace

> [!IMPORTANT]
> Používejte nejnovější verzi aplikace SQL Server Management Studio, abyste zajistili synchronizaci s aktualizacemi systému Microsoft Azure a služby SQL Database. Starší verze aplikace SQL Server Management Studio neumožňují nastavení služby SQL Database jako odběratele. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

1. Nastavení distribuce
   -  [Pomocí aplikace SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Pomocí jazyka Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Vytvoření publikace
   -  [Pomocí aplikace SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Pomocí jazyka Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Vytvoření předplatného
   -  [Pomocí aplikace SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Pomocí jazyka Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

Některé tipy a rozdíly pro migraci do služby SQL Database

- Použití místního distributora 
   - To způsobí, že dopad na výkon na serveru. 
   - Pokud je dopad na výkon nepřijatelný, můžete použít jiný server. Tím ale přispějete ke složitosti správy.
- Při výběru složky snímků se ujistěte, že je vybraná složka dostatečně velká pro uložení souborů .BACPAC všech tabulek, které chcete replikovat. 
- Vytváření snímku až do svého dokončení zamkne přidružené tabulky, proto vytvoření snímku řádně naplánujte. 
- Služba Azure SQL Database podporuje jenom nabízené odběry. Odběratele můžete přidat pouze ze zdrojové databáze.

### <a name="resolving-database-migration-compatibility-issues"></a>Řešení problémů s kompatibilitou při migrování databáze
Můžete narazit na spoustu problémů s kompatibilitou. To závisí na verzi systému SQL Server ve zdrojové databázi i na složitosti databáze, kterou migrujete. Starší verze systému SQL Server mají více problémů s kompatibilitou. Kromě cíleného prohledávání internetu s vybraným vyhledávacím strojem použijte následující prostředky:

* [Funkce databáze systému SQL Server nepodporované ve službě Azure SQL Database](sql-database-transact-sql-information.md)
* [Ukončená funkce databázového stroje v systému SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Ukončená funkce databázového stroje v systému SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Ukončená funkce databázového stroje v systému SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Ukončená funkce databázového stroje v systému SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Ukončená funkce databázového stroje v systému SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Kromě hledání na internetu a využití těchto prostředků použijte [fóra komunity systému MSDN SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) nebo [StackOverflow](http://stackoverflow.com/).

> [!IMPORTANT]
> Spravované Instance databáze SQL umožňuje migrovat existující instanci systému SQL Server a její databáze s minimálním k žádné problémy s kompatibilitou. V tématu [co je Instance spravované](sql-database-managed-instance.md).


## <a name="next-steps"></a>Další kroky
* Pomocí skriptu na blogu inženýrů Azure SQL EMEA můžete [monitorovat využití databáze tempdb během migrace](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
* Pomocí skriptu na blogu inženýrů Azure SQL EMEA můžete [monitorovat volné místo pro protokoly transakcí ve vaší databázi, zatímco probíhá migrace](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
* Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Informace o práci s časem UTC po migraci najdete v článku o [úpravě výchozího časového pásma na místní časové pásmo](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
* Informace o změně výchozího jazyka databáze po migraci najdete v článku o [změně výchozího jazyka služby Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).


