---
title: Migrace instance SQL serveru do Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Zjistěte, jak migrovat instance systému SQL Server do Azure SQL Database Managed Instance.
keywords: migrace databáze, migrace databáze systému sql server, nástroje pro migraci databáze, migrace databáze, migrace sql database
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: bonova
ms.openlocfilehash: e152fa4bb439f1881dc9974bfdf1b3e8c77c434a
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42057482"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrace instance SQL serveru do Azure SQL Database Managed Instance

V tomto článku se dozvíte o metodách migrace systému SQL Server 2005 nebo novější verze instance [Azure SQL Database Managed Instance](sql-database-managed-instance.md) (preview).

Na vysoké úrovni procesu migrace databáze vypadá takto:

![Proces migrace](./media/sql-database-managed-instance-migration/migration-process.png)

- [Vyhodnocení kompatibility Managed Instance](sql-database-managed-instance-migrate.md#assess-managed-instance-compatibility)
- [Možnost připojení k aplikaci](sql-database-managed-instance-migrate.md#choose-app-connectivity-option)
- [Nasazení do optimální velikosti spravované Instance](sql-database-managed-instance-migrate.md#deploy-to-an-optimally-sized-managed-instance)
- [Vyberte metodu migrace a migrace](sql-database-managed-instance-migrate.md#select-migration-method-and-migrate)
- [Monitorování aplikací](sql-database-managed-instance-migrate.md#monitor-applications)

> [!NOTE]
> Pokud chcete migrovat do izolované databáze nebo elastického fondu izolované databáze, najdete v článku [migrace databáze SQL serveru do služby Azure SQL Database](sql-database-cloud-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Vyhodnocení kompatibility Managed Instance

Nejdřív zjistěte, jestli je kompatibilní s požadavky na databázi aplikace Managed Instance. Spravovaná Instance je navržené pro poskytování snadno výtah a posunout migrace pro většinu z existující aplikace, které používají místní SQL Server nebo na virtuálních počítačích. Však můžete někdy potřebovat funkce nebo funkce, které se zatím nepodporují a náklady na implementaci řešení jsou příliš vysoká. 

Použití [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) kompatibility detekovat potenciální problémy s ovlivňuje funkčnost databází na Azure SQL Database. DMA zatím nepodporuje Managed Instance jako cíl migrace, ale doporučuje se spustit posouzení na databázi SQL Azure a pečlivě projděte si seznam paritu funkcí ohlášené a problémy s kompatibilitou proti dokumentaci k produktu. Najdete v článku [rozdíly mezi Azure SQL Database Singleton a Managed Instance](sql-database-features.md) ke kontrole existují některé nahlášené blokující problémy, které blockers není ve spravované instanci, protože většina blokující problémy brání migrace do Byly odebrány Azure SQL Database Managed instance. Pro instanci, funkce, jako jsou dotazy napříč databázemi, mezidatabázové transakce v rámci stejné instance propojený server do jiných zdrojů, CLR, globální dočasné tabulky SQL, jsou k dispozici ve spravovaných instancí úrovně zobrazení instance, služba Service Broker a podobně. 

Pokud jsou některé hlášené omezujícím problémům, které se neodeberou ve spravované instanci SQL Azure, můžete zvážit alternativní možnosti, jako například potřebovat [systému SQL Server na virtuálních počítačích v Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Zde je několik příkladů:

- Pokud budete vyžadovat přímý přístup k operačnímu systému nebo systému souborů, například instalace třetích stran nebo vlastních agentů na stejný virtuální počítač s SQL serverem.
- Pokud máte striktní závislost na funkcích, které se ještě nepodporují, jako je například FileStream a FileTable, PolyBase a transakce mezi instance.
- Pokud vůbec budete muset zůstat na konkrétní verzi systému SQL Server (2012, například).
- Pokud vaše požadavky na výpočetní výkon jsou mnohem nižší ve veřejné verzi preview nabízí spravované Instance (jedno virtuální jádro pro instanci) a konsolidace databáze není nepřijatelnou možností.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Nasazení do optimální velikosti spravované Instance

Spravovaná Instance je vytvořený na míru pro místní úlohy, které chcete přesunout do cloudu. Přináší [nové nákupní model](sql-database-service-tiers-vcore.md) , která poskytuje větší flexibilitu při výběru správnou úroveň prostředky pro vaše úlohy. Ve světě v místním jste zvyklí pravděpodobně velikosti tyto úlohy s použitím fyzických jader a šířku pásma vstupně-výstupních operací. Nové nákupní model pro Managed Instance je na základě virtuálních jader, nebo "virtuálních jader, za" s další úložiště a vstupně-výstupní operace k dispozici samostatně. Modelu virt. jader je jednodušší způsob, jak pochopit vaše požadavky na výpočetní výkon v cloudu můžete použít místní ještě dnes. Tento nový model vám umožní k nastavení správné velikosti vaše cílové prostředí v cloudu.

Můžete vybrat výpočetní prostředky a prostředky úložiště v nasazení čas a pak ji později změnit bez vnášení výpadku pro vaši aplikaci s použitím [webu Azure portal](sql-database-scale-resources.md):

![Změna velikosti spravované instance](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Zjistěte, jak vytvořit virtuální síť infrastruktury a Managed Instance, najdete v článku [vytvoříte Managed Instance](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Je důležité udržovat vaše cílové virtuální síť a podsíť vždy v souladu s [požadavky na spravované instanci virtuální síť](sql-database-managed-instance-vnet-configuration.md#requirements). Jakékoli nekompatibility můžete zabránit vám v vytváření nových instancí nebo pomocí ty, které jste už vytvořili.

## <a name="select-migration-method-and-migrate"></a>Vyberte metodu migrace a migrace

Managed Instance cíle uživatelské scénáře, které vyžadují velkokapacitní databáze migrace z místní nebo implementace databáze IaaS. Když budete potřebovat k přenosu back-endu aplikací, které pravidelně použít úrovni instance a / nebo mezidatabázové funkce jsou optimální volbou. Pokud je to váš scénář, můžete přesunout celou instanci odpovídající prostředí v Azure bez nutnosti rearchitecture vašich aplikací. 

Přesunutí instance SQL, musíte pečlivě naplánovat:

-   Migrace všech databází, které musí být společně umístěného (ty běžící na stejnou instanci)
-   Migrace objektů na úrovni instance, které vaše aplikace závisí, včetně přihlášení, přihlašovací údaje, úlohy agenta serveru SQL a operátory a aktivační události na úrovni serveru. 

Managed Instance je plně spravovaná služba, která umožňuje přidělovat některé regulární aktivity DBA na platformu, jako jsou součástí. Proto některé dat na úrovni instance není potřeba migrovat, jako je například úlohy údržby pro pravidelné zálohování nebo Always On konfigurace jako [vysoké dostupnosti](sql-database-high-availability.md) je součástí.

Spravovaná Instance podporuje následující možnosti migrace databáze (aktuálně to jsou metody pouze podporované migrace):

- Azure Database Migration Service – migrace s téměř nulovými výpadky
- Nativní `RESTORE DATABASE FROM URL` - využívá nativní zálohování SQL serveru a vyžaduje určitý výpadek.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service (DMS)](../dms/dms-overview.md) je plně spravovaná služba, která umožňují bezproblémovou migraci z několika databázových zdrojů na platformu Azure Data s minimálními výpadky. Tato služba zjednodušuje úlohy potřebné k migraci existujícího třetích stran a databáze SQL serveru do Azure. Možnosti nasazení ve verzi Public Preview zahrnují Azure SQL Database Managed Instance a SQL Server ve virtuálním počítači Azure. DMS je doporučená metoda migrace pro podnikové procesy. 

Pokud používáte SQL Server Integration Services (SSIS) na serveru SQL Server v místním prostředí, DMS zatím nepodporuje migrace katalogu služby SSIS (SSISDB), která ukládá balíčků služby SSIS, ale můžete zřídit prostředí Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF), který se bude Vytvoření nové databáze SSISDB ve službě Azure SQL Database/spravované Instance a potom můžete znovu nasaďte své balíčky do ní, přečtěte si [vytvořit prostředí Azure-SSIS IR ve službě ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime).

Další informace o krocích, které tento scénář a konfiguraci pro systém DMS, najdete v tématu [migrovat místní databázi do Managed Instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Nativní obnovení z adresy URL

OBNOVENÍ záloh nativní (soubory .bak) z místního SQL serveru nebo [systému SQL Server na virtuálních počítačích](https://azure.microsoft.com/services/virtual-machines/sql-server/), která je dostupná na [služby Azure Storage](https://azure.microsoft.com/services/storage/), je jedním z klíčových funkcí na spravované instanci SQL DB, který Migrace databáze umožňuje rychlé a jednoduché offline. 

Následující diagram představuje podrobný přehled procesu:

![Postup migrace](./media/sql-database-managed-instance-migration/migration-flow.png)

Následující tabulka obsahuje další informace týkající se metod, které můžete použít v závislosti na verzi zdrojového systému SQL Server, kterou používáte:

|Krok|Modul SQL a verze|Zálohování a obnově – metoda|
|---|---|---|
|Umístění zálohy do služby Azure Storage|Předchozí SQL 2012 SP1 kumulativní aktualizaci 2|Nahrajte soubor .bak přímo do služby Azure storage|
||2012 V AKTUALIZACI SP1 KUMULATIVNÍ AKTUALIZACI 2 - 2016|Přímé zálohování pomocí zastaralé [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) syntaxe|
||2016 a vyšší|Přímé zálohování pomocí [pomocí pověření SAS](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Obnovení z Azure Storage do spravované Instance|[OBNOVENÍ z adresy URL pomocí pověření SAS](sql-database-managed-instance-restore-from-backup-tutorial.md)|

> [!IMPORTANT]
> - Při migraci databáze chráněné [transparentním šifrováním dat](transparent-data-encryption-azure-sql.md) (TDE) do spravované instance Azure SQL pomocí nativní možnosti obnovení je třeba před obnovením databáze migrovat odpovídající certifikát z místního nebo IaaS systému SQL Server. Podrobné pokyny najdete v článku [cert TDE migrovat do Managed Instance](sql-database-managed-instance-migrate-tde-certificate.md)
> - Obnovení databází systému se nepodporuje. Pokud chcete migrovat objekty na úrovni instance (uložené v databázi master a databázi msdb databáze), doporučujeme je skriptování a spouštění skriptů T-SQL v cílové instanci.

Úplný kurz, který zahrnuje obnovení zálohy databáze do Managed Instance pomocí pověření SAS najdete v tématu [obnovit ze zálohy do spravované Instance](sql-database-managed-instance-restore-from-backup-tutorial.md).

## <a name="monitor-applications"></a>Monitorování aplikací

Sledování chování aplikace a výkonu po migraci. Ve spravované instanci některé změny jsou povolena pouze jednou [došlo ke změně úrovně kompatibility databáze](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Migrace databází do Azure SQL Database uchovává jeho původní úroveň kompatibility ve většině případů. Pokud se úroveň kompatibility databáze 100 a vyšší před migrací, zůstává stejná po migraci. Pokud byla úroveň kompatibility databáze 90 před migrací, upgradovaná databáze úroveň kompatibility nastavená na 100, což je úroveň nejnižší podporované kompatibility ve spravované instanci. Úroveň kompatibility databáze systému je 140.

Ke snížení rizik migrace, změňte úroveň kompatibility databáze až po monitorování výkonu. Query Store použijte jako ideální nástroj pro získání informací o výkon úloh před a po změně úrovně kompatibility databáze, jak je popsáno v [na novější verzi systému SQL Server během upgradu zachovat výkon, stabilitu](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Jakmile budete na to plně spravovaná platforma, využijte výhody, které jsou poskytovány automaticky jako součást služby SQL Database. Například nemusíte vytvářet zálohy na Managed Instance – služba provádí zálohování za vás automaticky. Je již nutné starat o plánování, provádění a Správa zálohování. Managed Instance poskytuje možnost obnovit do libovolného bodu v čase během období uchovávání dat pomocí [bodu v čase obnovení (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Ve verzi public preview je doba uchování pevně sedm dní.
Kromě toho není nutné starat o nastavení vysoké dostupnosti jako [vysoké dostupnosti](sql-database-high-availability.md) je součástí.

K posílení zabezpečení, zvažte použití některé z funkcí, které jsou k dispozici:
- Ověřování Azure Active Directory na úrovni databáze
- Použití [rozšířené funkce zabezpečení](sql-database-security-overview.md) například [auditování](sql-database-managed-instance-auditing.md), [detekce hrozeb](sql-advanced-threat-protection.md), [zabezpečení na úrovní řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security), a [dynamické Maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ) k zabezpečení vaší instance.

## <a name="next-steps"></a>Další postup

- Informace o Managed instance najdete v tématu [co je Managed Instance?](sql-database-managed-instance.md).
- Kurz zahrnuje obnovení ze zálohy, najdete v tématu [vytvoříte Managed Instance](sql-database-managed-instance-get-started.md).
- Kurz zobrazující migrace pomocí DMS, naleznete v tématu [migrovat místní databázi do Managed Instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
