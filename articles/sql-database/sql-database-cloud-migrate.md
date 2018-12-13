---
title: Migrace databáze SQL Serveru do služby Azure SQL Database | Dokumentace Microsoftu
description: V této části se dozvíte, jak migrovat místní databázi SQL Serveru do služby Azure SQL Database v cloudu.
keywords: migrace databáze, migrace databáze systému sql server, nástroje pro migraci databáze, migrace databáze, migrace sql database
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: e87f8a0d168db02bf0e93216991767f5a9be584c
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863512"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migrace databáze SQL serveru do Azure SQL Database

V tomto článku se dozvíte o základních způsobech migrace systému SQL Server 2005 nebo novější do jedné nebo součástí fondu databáze ve službě Azure SQL Database. Informace o migraci do spravované Instance najdete v tématu [migrace na instanci systému SQL Server do Azure SQL Database Managed Instance](sql-database-managed-instance-migrate.md).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrace na jednu databázi nebo databázi ve fondu

Existují dvou základních způsobech migrace systému SQL Server 2005 nebo novější do jedné nebo součástí fondu databáze ve službě Azure SQL Database. První způsob je jednodušší, ale vyžaduje nějaké, potenciálně dlouhé, prostoje během migrace. Druhý způsob je složitější, ale podstatným způsobem eliminuje prostoje během migrace.

V obou případech je potřeba zajistit, že zdrojová databáze je kompatibilní s Azure SQL Database pomocí [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). SQL Database V12 se blíží [paritě funkcí](sql-database-features.md) s SQL serverem, jiné než problémy související s operacemi na úrovni serveru a mezi databázemi. Databáze a aplikace, které spoléhají na [částečně podporované nebo nepodporované funkce](sql-database-transact-sql-information.md), potřebují určité [zpracování, aby se tyto nekompatibility opravily](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues) před migrací databáze SQL Serveru.

> [!NOTE]
> Pokud chcete migrovat databázi jiného typu než SQL Server, včetně databází Microsoft Access, Sybase, MySQL Oracle a DB2, na databázi Azure SQL Database, získáte informace v tématu [Pomocník s migrací na SQL Server](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Způsob 1: Migrace s prostoji během migrace

 Tuto metodu použijte k migraci k jednomu nebo databázi ve fondu, pokud si můžete nějaké prostoje dovolit nebo testujete migraci produkční databáze fungují pro pozdější migraci. Podívejte se kurz [migrace databáze SQL serveru](sql-database-migrate-your-sql-server-database.md).

Následující seznam obsahuje obecný pracovní postup pro migraci databáze SQL serveru z jediného nebo databázi ve fondu pomocí této metody. Pro migraci do spravované Instance, naleznete v tématu [migraci do spravované Instance](sql-database-managed-instance-migrate.md).

  ![Diagram migrace VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Vyhodnocení](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) databáze z důvodu kompatibility s použitím nejnovější verzi [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Příprava všech nezbytných oprav ve formě skriptů Transact-SQL.
3. Vytvoření transakčně konzistentní kopie migrované zdrojové databáze nebo zastavení nové transakce z ke kterým dochází ve zdrojové databázi, zatímco probíhá migrace. Metody k provedení této druhou možnost zahrnout zakázání připojení klientů nebo vytváření [snímku databáze](https://msdn.microsoft.com/library/ms175876.aspx). Po migraci je možné k aktualizaci migrovaných databází se změnami, ke kterým dochází po přerušení bodu migrace použít transakční replikaci. Zobrazit [migrovat pomocí migrace transakčních](sql-database-cloud-migrate.md#method-2-use-transactional-replication).  
4. Nasazení skriptů Transact-SQL a aplikování oprav na kopii databáze.
5. [Migrace](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) kopie databáze na novou službu Azure SQL Database s použitím nástroj Data Migration Assistant.

> [!NOTE]
> Místo použití DMA, můžete také použít souboru BACPAC. Zobrazit [Import souboru BACPAC do nové databáze SQL Azure](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optimalizace výkonu přenosu dat během migrace

Následující seznam obsahuje doporučení pro zajištění nejlepšího výkonu během procesu importu.

- Zvolte nejvyšší úroveň služby a vypočítat velikost, jaké vám rozpočet dovolí jak maximalizovat výkon přenosu. Po dokončení migrace můžete vertikálně snížit kapacitu a ušetřit tak peníze.
- Minimalizujte vzdálenost mezi soubor BACPAC a cílovým datovým centrem.
- Zakažte během migrace automatické statistiky.
- Rozdělte tabulky a indexy na oddíly.
- Zrušte indexovaná zobrazení a po dokončení je znovu vytvořte.
- Odeberte zřídka dotazovaná historická data do jiné databáze a migrujte je do samostatné databáze SQL Azure. Potom můžete historická data dotazovat pomocí [elastických dotazů](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Optimalizace výkonu po dokončení migrace

[Aktualizujte statistiky](https://msdn.microsoft.com/library/ms187348.aspx) pomocí úplného prohledávání, jakmile se migrace dokončí.

## <a name="method-2-use-transactional-replication"></a>Způsob 2: Použití transakční replikace

Pokud si během migrace nemůžete dovolit stažení databáze SQL Serveru z produkčního prostředí, můžete jako řešení migrace použít transakční replikaci SQL Serveru. Abyste tento způsob mohli použít, zdrojová databáze musí splňovat [požadavky pro transakční replikaci](https://msdn.microsoft.com/library/mt589530.aspx) a musí být kompatibilní se službou Azure SQL Database. Informace o replikaci SQL s Always On najdete v tématu [konfigurace replikace pro vždy na skupiny dostupnosti (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Toto řešení použijete tak, že nakonfigurujete službu Azure SQL Database jako odběratele instance SQL Serveru, kterou chcete migrovat. Distributor transakční replikace synchronizuje data z databáze, která se má synchronizovat (vydavatel), zatímco se stále provádějí nové transakce.

U transakční replikace se všechny změny dat nebo schématu objeví i ve službě Azure SQL Database. Jakmile se synchronizace dokončí a budete připraveni k migraci, změňte připojovací řetězec vašich aplikací tak, aby je směroval do služby Azure SQL Database. Až transakční replikace vytáhne všechny zbývající změny ze zdrojové databáze a všechny vaše aplikace budou směrovat do databáze Azure, můžete transakční replikaci odinstalovat. Vaším produkčním systémem je nyní služba Azure SQL Database.

 ![Diagram přidání počátečních hodnot do cloudu pomocí transakční replikace](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> Transakční replikaci můžete také použít k migraci podmnožiny vaší zdrojové databáze. Publikaci, kterou replikujete do služby Azure SQL Database, můžete omezit na podmnožinu tabulek v replikované databázi. Pro každou replikovanou tabulku můžete data omezit na podmnožinu řádků nebo podmnožinu sloupců.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Pracovní postup migrace do služby SQL Database pomocí transakční replikace

> [!IMPORTANT]
> Používejte nejnovější verzi aplikace SQL Server Management Studio, abyste zajistili synchronizaci s aktualizacemi systému Microsoft Azure a služby SQL Database. Starší verze aplikace SQL Server Management Studio neumožňují nastavení služby SQL Database jako odběratele. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

1. Nastavení distribuce
   - [Pomocí aplikace SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   - [Pomocí jazyka Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)

2. Vytvoření publikace
   - [Pomocí aplikace SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   - [Pomocí jazyka Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Vytvoření předplatného
   - [Pomocí aplikace SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   - [Pomocí jazyka Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

Některé tipy a rozdíly pro migraci do služby SQL Database

- Použití místního distributora
  - To způsobí, že dopad na výkon na serveru.
  - Pokud je dopad na výkon nepřijatelný, můžete použít jiný server. Tím ale přispějete ke složitosti správy.
- Při výběru složky snímků se ujistěte, že je vybraná složka dostatečně velká pro uložení souborů .BACPAC všech tabulek, které chcete replikovat.
- Vytváření snímku až do svého dokončení zamkne přidružené tabulky, proto vytvoření snímku řádně naplánujte.
- Služba Azure SQL Database podporuje jenom nabízené odběry. Odběratele můžete přidat pouze ze zdrojové databáze.

## <a name="resolving-database-migration-compatibility-issues"></a>Řešení problémů s kompatibilitou při migrování databáze

Můžete narazit na spoustu problémů s kompatibilitou. To závisí na verzi systému SQL Server ve zdrojové databázi i na složitosti databáze, kterou migrujete. Starší verze systému SQL Server mají více problémů s kompatibilitou. Kromě cíleného prohledávání internetu s vybraným vyhledávacím strojem použijte následující prostředky:

- [Funkce databáze systému SQL Server nepodporované ve službě Azure SQL Database](sql-database-transact-sql-information.md)
- [Ukončená funkce databázového stroje v systému SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Ukončená funkce databázového stroje v systému SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Ukončená funkce databázového stroje v systému SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Ukončená funkce databázového stroje v systému SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Ukončená funkce databázového stroje v systému SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Kromě hledání na internetu a využití těchto prostředků použijte [fóra komunity systému MSDN SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) nebo [StackOverflow](http://stackoverflow.com/).

> [!IMPORTANT]
> SQL Database Managed Instance umožňuje migrovat existující instanci systému SQL Server a jeho databázím s minimálními žádné problémy s kompatibilitou. Zobrazit [co je Managed Instance](sql-database-managed-instance.md).

## <a name="next-steps"></a>Další postup

- Pomocí skriptu na blogu inženýrů Azure SQL EMEA můžete [monitorovat využití databáze tempdb během migrace](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Pomocí skriptu na blogu inženýrů Azure SQL EMEA můžete [monitorovat volné místo pro protokoly transakcí ve vaší databázi, zatímco probíhá migrace](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Informace o práci s časem UTC po migraci najdete v článku o [úpravě výchozího časového pásma na místní časové pásmo](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
- Informace o změně výchozího jazyka databáze po migraci najdete v článku o [změně výchozího jazyka služby Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).
