---
title: Migrovat instance systému SQL Server do Azure spravované Instance databáze SQL | Microsoft Docs
description: Zjistěte, jak migrovat instance systému SQL Server do Azure SQL Database spravované Instance.
keywords: migrace databáze, migrace databáze systému sql server, nástroje pro migraci databáze, migrace databáze, migrace sql database
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: bonova
ms.openlocfilehash: 5b8a2ec7e0401ac239acdefdd77a13b522f73960
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrace instance systému SQL Server na instanci spravované databáze Azure SQL

V tomto článku informace o metodách pro migraci systému SQL Server 2005 nebo novější verze instance do Azure SQL Database spravované Instance (preview). 

MI je rozšíření stávající služby SQL Database, které nabízí třetí možnost nasazení vedle jedné databáze a elastických fondů.  Je navržená tak, jak povolit databázi navýšení a posunu plně spravovaná PaaS, bez přepracování aplikace. SQL Database Managed Instance poskytuje vysokou kompatibilitu díky místnímu programovacímu modelu SQL Serveru a integrované podpoře valné většiny funkcí SQL Serveru a doprovodných nástrojů a služeb.

Na vysoké úrovni proces migrace aplikace vypadá na následující diagram:

![Proces migrace](./media/sql-database-managed-instance-migration/migration-process.png)

- [Vyhodnocení kompatibility spravované Instance](sql-database-managed-instance-migrate.md#assess-managed-instance-compatibility)
- [Vyberte možnost připojení k aplikaci](sql-database-managed-instance-migrate.md#choose-app-connectivity-option)
- [Nasazení do optimálně velikostí spravované Instance](sql-database-managed-instance-migrate.md#deploy-to-an-optimally-sized-managed-instance)
- [Vyberte metodu migrace a migrujte](sql-database-managed-instance-migrate.md#select-migration-method-and-migrate)
- [Monitorování aplikací](sql-database-managed-instance-migrate.md#monitor-applications)

> [!NOTE]
> Migrace jedné databáze do jedné databáze nebo elastického fondu, najdete v části [migrovat do databáze SQL serveru do Azure SQL Database](sql-database-cloud-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Vyhodnocení kompatibility spravované Instance

Nejdřív zjistěte, zda je kompatibilní s požadavky na databázi aplikace spravované Instance. Spravované Instance určená k poskytování snadné navýšení shift migrace a pro většinu existujících aplikací, které používají místní SQL Server nebo na virtuálních počítačích. Ale možná budete někdy potřebovat funkce nebo funkce, které ještě nejsou podporované a náklady na implementaci řešení jsou příliš vysoké. 

Použití [pomocníka pro migraci dat (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) zjistit potenciální problémy kompatibility ovlivňuje funkce databáze v databázi SQL Azure. DMA zatím nepodporuje spravované Instance jako cíl migrace, ale doporučuje se spustit vyhodnocení proti databázi SQL Azure a pečlivě projděte si seznam parity hlášené funkcí a problémy s kompatibilitou proti dokumentaci k produktu. Většina blokující problémy brání migrace do Azure SQL Database byly odebrány s spravované Instance. Pro instance, funkce, jako jsou dotazy mezidatabázové, mezidatabázové transakce v rámci stejné instance odkazovaný server, aby jiných zdrojů, CLR, globální dočasné tabulky SQL, jsou k dispozici v spravované instance zobrazení na úrovni instance, služby Service Broker a podobně. 

Nicméně jsou případy, pokud je potřeba zvážit alternativní možnosti, jako například [systému SQL Server na virtuálních počítačích v Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Zde je několik příkladů:

- Pokud budete potřebovat přímý přístup do operačního systému nebo systém souborů, například při instalaci třetích stran nebo vlastní agenty na stejný virtuální počítač se systémem SQL Server.
- Pokud máte striktní závislost na funkce, které se ještě nepodporují, jako je například FileStream nebo objektu FileTable, PolyBase a křížové instance transakce.
- Pokud absolutně budete muset zůstat na konkrétní verzi systému SQL Server (2012, například).
- Pokud vaše požadavky na výpočetní jsou mnohem nižší dané spravované Instance nabízí ve verzi public preview (jeden vCore pro instanci) a databáze konsolidace není přijatelné možnost.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Nasazení do optimálně velikostí spravované Instance

Spravované Instance je přizpůsobit pro místní úlohy, které jsou v úmyslu přesunout do cloudu. Zavádí nové nákupní model, který poskytuje větší flexibilitu při výběru správnou úroveň prostředky pro úlohy. Na světě místní jste zvyklí pravděpodobně dimenzování tyto úlohy pomocí fyzických jader. Nový model nákupu pro spravované Instance je založena na virtuální jader, nebo "vCores" s další úložiště a vstupně-výstupní operace k dispozici samostatně. VCore model je jednodušší způsob, jak porozumět požadavkům vašeho výpočetního v cloudu můžete použít místní ještě dnes. Tento nový model umožňuje optimální velikost prostředí cílového v cloudu.

Můžete vybrat výpočetní a prostředky úložiště v nasazení čas a potom ho později změnit bez zavedení výpadku pro vaši aplikaci.

![nastavení velikosti spravované instance](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Naučte se vytvářet virtuální sítě infrastruktury a spravovat instanci, najdete v tématu [vytvořit instanci spravované](sql-database-managed-instance-create-tutorial-portal.md).

> [!IMPORTANT]
> Je důležité udržovat vaše cílové virtuální síť a podsíť vždy v souladu s [požadavky spravované Instance VNET](sql-database-managed-instance-vnet-configuration.md#requirements). Nekompatibilita můžete zabránit vám v vytvoření nové instance služby nebo pomocí ty, které jste už vytvořili.

## <a name="select-migration-method-and-migrate"></a>Vyberte metodu migrace a migrujte

Spravované Instance cíle uživatele scénářům, které vyžadují velkokapacitní databáze migrace z místní nebo implementace databáze IaaS. Pokud budete potřebovat k navýšení a posunutí back-end aplikace, které pravidelně použít úrovni instance nebo mezidatabázové funkce jsou optimální volbou. Pokud je to váš scénář, můžete přesunout celou instanci odpovídající prostředí v Azure, aniž by bylo nutné rearchitecture vaší aplikace. 

Přesunutí instance SQL, je třeba pečlivě naplánovat:

-   Migrace všech databází, které musí být společně umístěných (ty, které jsou ve stejné instanci spuštěna)
-   Migrace objektů úrovni instance, které závisí aplikace, včetně přihlášení, přihlašovací údaje, úlohy agenta serveru SQL a operátory a aktivačních událostí na úrovni serveru. 

Spravované Instance je plně spravovaná služba, která umožňuje delegovat některé regulární DBA aktivit pro platformu, jako jsou součástí. Některé dat na úrovni instance proto není nutné migrovat, jako je například úlohy údržby pro pravidelné zálohování nebo Always On konfigurace jako [vysokou dostupnost](sql-database-high-availability.md) je součástí.

Spravované Instance podporuje následující možnosti migrace databáze (aktuálně to jsou pouze podporovaných migračních metody):

- Služba Azure migrace databáze - migrace s téměř nulové výpadek
- Nativní obnovení z adresy URL - využívá nativní zálohy z SQL serveru a vyžaduje výpadky
- Migrace pomocí souboru BACPAC souboru – používá soubor souboru BACPAC z SQL serveru nebo v databázi SQL a vyžaduje výpadky

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure databáze migrace služby (DMS)](../dms/dms-overview.md) je plně spravovaná služba navržených k povolení bezproblémové migrace z více zdrojů databáze do platformy Azure dat s minimálními výpadky. Tato služba zjednodušuje úkoly vyžadované pro přesun existujících třetích stran a databáze SQL serveru do Azure. Možnosti nasazení ve verzi Public Preview zahrnují Azure SQL Database, spravované Instance a SQL Server v virtuální počítač Azure. Služba DMS patří mezi doporučené metody migrace pro vaše podnikové úlohy. 

Další informace o krocích, tento scénář a konfiguraci pro DMS najdete v tématu [migrací místní databázi k instanci spravované pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Nativní obnovení z adresy URL

OBNOVENÍ z nativní zálohy (soubory .bak) vytvořené z místního serveru SQL nebo [systému SQL Server na virtuálních počítačích](https://azure.microsoft.com/services/virtual-machines/sql-server/), k dispozici na [Azure Storage](https://azure.microsoft.com/services/storage/), je jedním z klíčových funkcí na spravované Instance databáze SQL, umožňuje rychlé a jednoduché offline databáze migrace. 

Následující diagram popisuje proces na vysoké úrovni:

![migrace toku](./media/sql-database-managed-instance-migration/migration-flow.png)

Následující tabulka obsahuje další informace o metodě, které můžete použít v závislosti na verzi systému SQL Server zdrojové, že používáte:

|Krok|Stroj SQL a verze|Zálohování a obnově – metoda|
|---|---|---|
|Uveďte zálohování do úložiště Azure|Předchozí SQL 2012 SP1 CU2|Nahrát soubor .bak přímo do úložiště Azure|
||2012 SP1 CU2 - 2016|Přímé zálohování pomocí zastaralé [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) syntaxe|
||2016 a vyšší|Přímé zálohování pomocí [s pověření SAS](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Obnovení z úložiště Azure do spravované Instance|[OBNOVENÍ z adresy URL pověření SAS](sql-database-managed-instance-restore-from-backup-tutorial.md)|

> [!IMPORTANT]
> Obnovení databází systému není podporována. Pokud chcete migrovat instance objektů úrovně (uložené v databázi master a databázi msdb databázích), doporučujeme je skriptování a spouštění skriptů T-SQL v cílové instanci.

Úplné kurz, který zahrnuje obnovení ze zálohy databáze do Instance spravované pomocí pověření SAS, najdete v části [obnovit ze zálohy do Instance spravované](sql-database-managed-instance-restore-from-backup-tutorial.md).

### <a name="migrate-using-bacpac-file"></a>Migrace pomocí souboru BACPAC souboru

Můžete importovat do Azure SQL Database a spravované Instance z vytvořit kopii původní databáze, s daty v souboru BACPAC souboru. V tématu [importovat soubor souboru BACPAC pro novou databázi SQL Azure](sql-database-import.md).

## <a name="monitor-applications"></a>Monitorování aplikací

Sledování chování aplikace a výkonu po migraci. Ve spravované Instance některé změny jsou povolena pouze jednou [změnil úroveň kompatibility databáze](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Migrace databáze do Azure SQL Database udržuje jeho původní úroveň kompatibility ve většině případů. Pokud je úroveň kompatibility databáze uživatelů byla 100 nebo vyšší před migrací, zůstává stejná po migraci. Pokud byla úroveň kompatibility databáze uživatelů 90 před migrací, v databázi upgradovaný úroveň kompatibility nastavená na 100, což je úroveň kompatibility s nejnižší podporované ve spravované Instance. Úroveň kompatibility databáze systému je 140.

Snížení rizika migrace, změňte úroveň kompatibility databáze až po sledování výkonu. Úložiště dotazů použít jako optimální nástroj pro získání informací o zatížení výkonu před a po Změna úrovně kompatibility databáze, jak je popsáno v [zachovat stabilitu výkonu během upgradu na novější verzi systému SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Jakmile se ve plně spravovaná platforma trvat výhody, které jsou k dispozici automaticky v rámci služby SQL Database. Pro instanci nemusíte vytvářet zálohy na spravované Instance – služba provádět zálohování pro vás automaticky. Jste již musí starat o plánování, trvá a správu záloh. Spravované Instance poskytuje možnost obnovit do libovolného bodu v čase v rámci doby uchování pomocí [bodu v možnosti doba obnovení (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Ve verzi public preview doba uchování stanoví na sedm dní.
Kromě toho není nutné se obávat nastavení vysoké dostupnosti jako [vysokou dostupnost](sql-database-high-availability.md) je součástí.

K posílení zabezpečení, zvažte použití některých funkcí, které jsou k dispozici:
- Azure Active Directory ověřování na úrovni databáze
- Auditování a detekce hrozeb sledovat aktivity
- Řízení přístupu k datům citlivé a privilegovaných ([zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) a [dynamického maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)).

## <a name="next-steps"></a>Další postup

- Informace o instancích spravované najdete v tématu [co je Instance spravované?](sql-database-managed-instance.md).
- Kurz, který zahrnuje obnovení ze zálohy, najdete v části [vytvořit instanci spravované](sql-database-managed-instance-create-tutorial-portal.md).
- Kurz zobrazující migraci pomocí DMS, najdete v části [migrací místní databázi k instanci spravované pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
