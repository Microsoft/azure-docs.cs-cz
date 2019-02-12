---
title: Migrace instance SQL serveru do spravované instance Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak migrovat instance SQL serveru do spravované instance Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 1460b595e8887fc932d5be335ae51b07a000b9fb
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098353"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrace instance SQL serveru do služby Azure SQL Database managed instance

V tomto článku se dozvíte o metodách migrace systému SQL Server 2005 nebo novější verze instance [Azure SQL Database managed instance](sql-database-managed-instance.md). Informace o migraci na elastický fond nebo izolovanou databázi, naleznete v tématu [migrovat jednu, nebo součástí fondu databáze](sql-database-cloud-migrate.md). Informace o migraci o migraci z jiných platforem, naleznete v tématu [Průvodce migrací databází Azure](https://datamigration.microsoft.com/).

Na vysoké úrovni procesu migrace databáze vypadá takto:

![Proces migrace](./media/sql-database-managed-instance-migration/migration-process.png)

- [Vyhodnocení kompatibility spravované instance](#assess-managed-instance-compatibility)
- [Možnost připojení k aplikaci](sql-database-managed-instance-connect-app.md)
- [Nasazení do optimální velikosti spravované instance](#deploy-to-an-optimally-sized-managed-instance)
- [Vyberte metodu migrace a migrace](#select-migration-method-and-migrate)
- [Monitorování aplikací](#monitor-applications)

> [!NOTE]
> Migrace jednotlivých databází do izolované databáze nebo elastického fondu, naleznete v tématu [migrace databáze SQL serveru do služby Azure SQL Database](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Vyhodnocení kompatibility spravované instance

Nejprve určete, zda managed instance je kompatibilní s požadavky na databázi aplikace. Možnost nasazení spravované instance je navržené pro poskytování snadno výtah a posunout migrace pro většinu z existující aplikace, které používají místní SQL Server nebo na virtuálních počítačích. Však můžete někdy potřebovat funkce nebo funkce, které se zatím nepodporují a náklady na implementaci řešení jsou příliš vysoká.

Použití [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) kompatibility detekovat potenciální problémy s ovlivňuje funkčnost databází na Azure SQL Database. DMA nepodporuje, ale podpora spravovaná instance jako cíl migrace, ale doporučuje spustit posouzení na databázi SQL Azure a pečlivě zkontrolovat seznam paritu funkcí ohlášené a problémy s kompatibilitou proti dokumentaci k produktu. Zobrazit [funkce služby Azure SQL Database](sql-database-features.md) ke kontrole existují některé hlášené omezujícím problémům, které nejsou blockers ve spravované instanci, protože většina blokující problémy, které brání migrace do Azure SQL Database byly odebrány s spravované instance. Pro instanci, funkce, jako jsou dotazy napříč databázemi, mezidatabázové transakce v rámci stejné instance, propojený server do jiných zdrojů, CLR, globální dočasné tabulky SQL jsou k dispozici ve spravované instance úrovně zobrazení instance, služba Service Broker a podobně.

Pokud jsou některé hlášené omezujícím problémům, které se neodeberou s možností nasazení spravované instance, můžete potřebovat zvážit alternativní možnosti, jako například [systému SQL Server na virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Zde je několik příkladů:

- Pokud budete vyžadovat přímý přístup k operačnímu systému nebo systému souborů, například instalace třetích stran nebo vlastních agentů na stejný virtuální počítač s SQL serverem.
- Pokud máte striktní závislost na funkcích, které se ještě nepodporují, jako je například FileStream a FileTable, PolyBase a transakce mezi instance.
- Pokud vůbec budete muset zůstat na konkrétní verzi systému SQL Server (2012, například).
- Pokud vaše požadavky na výpočetní výkon jsou mnohem nižší nabízí spravované instance (jedno virtuální jádro pro instanci) a konsolidace databáze není nepřijatelnou možností.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Nasazení do optimálně dimenzované mi

Spravovaná instance je vytvořený na míru pro místní úlohy, které chcete přesunout do cloudu. Přináší [nové nákupní model](sql-database-service-tiers-vcore.md) , která poskytuje větší flexibilitu při výběru správnou úroveň prostředky pro vaše úlohy. Ve světě v místním jste zvyklí pravděpodobně velikosti tyto úlohy s použitím fyzických jader a šířku pásma vstupně-výstupních operací. Nákupní model pro spravovanou instanci je na základě virtuálních jader, nebo "virtuálních jader, za" s další úložiště a vstupně-výstupní operace k dispozici samostatně. Modelu virt. jader je jednodušší způsob, jak pochopit vaše požadavky na výpočetní výkon v cloudu můžete použít místní ještě dnes. Tento nový model vám umožní k nastavení správné velikosti vaše cílové prostředí v cloudu.

Můžete vybrat výpočetní prostředky a prostředky úložiště v nasazení čas a pak ji později změnit bez vnášení výpadku pro vaši aplikaci s použitím [webu Azure portal](sql-database-scale-resources.md):

![Změna velikosti spravované instance](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Zjistěte, jak vytvořit virtuální síť infrastruktury a managed instance, najdete v článku [vytvoření managed instance](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Je důležité udržovat vaše cílové virtuální síť a podsíť vždy v souladu s [managed instance požadavky na virtuální síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Jakékoli nekompatibility můžete zabránit vám v vytváření nových instancí nebo pomocí ty, které jste už vytvořili. Další informace o [vytváří se nová](sql-database-managed-instance-create-vnet-subnet.md) a [konfigurace stávající](sql-database-managed-instance-configure-vnet-subnet.md) sítě.

## <a name="select-migration-method-and-migrate"></a>Vyberte metodu migrace a migrace

Databáze spravované instance možnost cíle uživatelské scénáře nasazení které vyžadují velkokapacitní databáze migrace z místní nebo IaaS implementace. Když budete potřebovat k přenosu back-endu aplikací, které pravidelně použít úrovni instance a / nebo mezidatabázové funkce jsou optimální volbou. Pokud je to váš scénář, můžete přesunout celou instance na odpovídající prostředí v Azure, aniž byste museli upravovat architekturu, když vaše aplikace.

Přesunutí instance SQL, musíte pečlivě naplánovat:

- Migrace všech databází, které musí být společně umístěného (ty běžící na stejnou instanci)
- Migrace objektů na úrovni instance, které vaše aplikace závisí, včetně přihlášení, přihlašovací údaje, úlohy agenta serveru SQL a operátory a aktivační události na úrovni serveru.

Managed instance je spravovaná služba, která umožňuje přidělovat některé regulární aktivity DBA na platformu, jako jsou součástí. Proto některé dat na úrovni instance není potřeba migrovat, jako je například úlohy údržby pro pravidelné zálohování nebo Always On konfigurace jako [vysoké dostupnosti](sql-database-high-availability.md) je součástí.

Spravovaná instance podporuje následující možnosti migrace databáze (aktuálně to jsou metody pouze podporované migrace):

- Azure Database Migration Service – migrace s téměř nulovými výpadky
- Nativní `RESTORE DATABASE FROM URL` - využívá nativní zálohování SQL serveru a vyžaduje určitý výpadek.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service (DMS)](../dms/dms-overview.md) je plně spravovaná služba, která umožňují bezproblémovou migraci z několika databázových zdrojů na platformu Azure Data s minimálními výpadky. Tato služba zjednodušuje úlohy potřebné k migraci existujícího třetích stran a databáze SQL serveru do Azure. Možnosti nasazení ve verzi public preview zahrnují databáze v databázích Azure SQL Database a SQL Server ve virtuálním počítači Azure. DMS je doporučená metoda migrace pro podnikové procesy.

Pokud používáte SQL Server Integration Services (SSIS) na serveru SQL Server v místním prostředí, DMS zatím nepodporuje migrace katalogu služby SSIS (SSISDB), která ukládá balíčků služby SSIS, ale můžete zřídit prostředí Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF), který se bude Vytvoření nové databáze SSISDB do spravované instance a potom znovu nasaďte své balíčky do ní, naleznete v tématu [vytvořit prostředí Azure-SSIS IR ve službě ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Další informace o krocích, které tento scénář a konfiguraci pro systém DMS, najdete v tématu [migrovat místní databázi do spravované instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Nativní obnovení z adresy URL

OBNOVENÍ záloh nativní (soubory .bak) z místního SQL serveru nebo [systému SQL Server na virtuálních počítačích](https://azure.microsoft.com/services/virtual-machines/sql-server/), která je dostupná na [služby Azure Storage](https://azure.microsoft.com/services/storage/), je jedním z klíčových funkcí nasazení spravované instance možnost, která umožňuje rychlé a snadné databáze offline migraci.

Následující diagram představuje podrobný přehled procesu:

![Postup migrace](./media/sql-database-managed-instance-migration/migration-flow.png)

Následující tabulka obsahuje další informace týkající se metod, které můžete použít v závislosti na verzi zdrojového systému SQL Server, kterou používáte:

|Krok|Modul SQL a verze|Zálohování a obnově – metoda|
|---|---|---|
|Umístění zálohy do služby Azure Storage|Prior SQL 2012 SP1 CU2|Nahrajte soubor .bak přímo do služby Azure storage|
||2012 SP1 CU2 - 2016|Přímé zálohování pomocí zastaralé [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) syntaxe|
||2016 a vyšší|Přímé zálohování pomocí [pomocí pověření SAS](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Obnovení z Azure storage do spravované instance|[OBNOVENÍ z adresy URL pomocí pověření SAS](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Při migraci databáze chráněn [transparentního šifrování dat](transparent-data-encryption-azure-sql.md) do spravované instance pomocí možnosti nativní obnovení, je třeba migrovat před databáze odpovídající certifikát z místní nebo IaaS SQL Server obnovení. Podrobné pokyny najdete v článku [cert TDE migraci do spravované instance](sql-database-managed-instance-migrate-tde-certificate.md)
> - Obnovení databází systému se nepodporuje. Pokud chcete migrovat objekty na úrovni instance (uložené v databázi master a databázi msdb databáze), doporučujeme je skriptování a spouštění skriptů T-SQL v cílové instanci.

Rychlý start ukazuje, jak obnovit zálohu databáze do spravované instance pomocí pověření SAS, naleznete v tématu [obnovit ze zálohy do spravované instance](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Monitorování aplikací

Sledování chování aplikace a výkonu po migraci. Ve spravované instanci některé změny jsou povolena pouze jednou [došlo ke změně úrovně kompatibility databáze](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Migrace databází do Azure SQL Database uchovává jeho původní úroveň kompatibility ve většině případů. Pokud se úroveň kompatibility databáze 100 a vyšší před migrací, zůstává stejná po migraci. Pokud byla úroveň kompatibility databáze 90 před migrací, upgradovaná databáze úroveň kompatibility nastavená na 100, což je úroveň nejnižší podporované kompatibility ve spravované instanci. Úroveň kompatibility databáze systému je 140.

Ke snížení rizik migrace, změňte úroveň kompatibility databáze až po monitorování výkonu. Query Store použijte jako ideální nástroj pro získání informací o výkon úloh před a po změně úrovně kompatibility databáze, jak je popsáno v [na novější verzi systému SQL Server během upgradu zachovat výkon, stabilitu](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Jakmile budete na to plně spravovaná platforma, využijte výhody, které jsou poskytovány automaticky jako součást služby SQL Database. Například nemusíte vytvářet zálohy na spravované instanci – služba provádí zálohování za vás automaticky. Je již nutné starat o plánování, provádění a Správa zálohování. Spravovaná instance máte možnost obnovit do libovolného bodu v čase během období uchovávání dat pomocí [bodu v čase obnovení (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Kromě toho není nutné starat o nastavení vysoké dostupnosti jako [vysoké dostupnosti](sql-database-high-availability.md) je součástí.

K posílení zabezpečení, zvažte použití některé z funkcí, které jsou k dispozici:

- Ověřování Azure Active Directory na úrovni databáze
- Použití [rozšířené funkce zabezpečení](sql-database-security-overview.md) například [auditování](sql-database-managed-instance-auditing.md), [detekce hrozeb](sql-database-advanced-data-security.md), [zabezpečení na úrovní řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security), a [dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ) k zabezpečení vaší instance.

## <a name="next-steps"></a>Další postup

- Informace o spravované instance najdete v tématu [co je managed instance?](sql-database-managed-instance.md).
- Kurz zahrnuje obnovení ze zálohy, najdete v tématu [vytvoření managed instance](sql-database-managed-instance-get-started.md).
- Kurz zobrazující migrace pomocí DMS, naleznete v tématu [migrovat místní databázi do spravované instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
