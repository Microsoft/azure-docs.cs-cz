---
title: SQL Server SQL Server na virtuálních počítačích Azure (Průvodce migrací)
description: Pomocí tohoto průvodce můžete migrovat jednotlivé databáze SQL Server a SQL Server na Azure Virtual Machines (virtuálních počítačů).
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: cc2a641cb017edace24db5df69bc4adf3a607524
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797876"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>Průvodce migrací: SQL Server SQL Server na virtuálních počítačích Azure 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Tato příručka k migraci vás seznámí s tím, jak **zjišťovat**, **vyhodnocovat** a **migrovat** vaše uživatelské databáze z SQL Server do instance SQL Server v Azure Virtual Machines (virtuálních počítačích) pomocí zálohování a obnovení a přenosu protokolu s využitím [Pomocník s migrací databáze (DMA)](/sql/dma/dma-overview) k posouzení. 

Můžete migrovat SQL Server spuštěná místně nebo na:

- SQL Server na virtuálních počítačích  
- Amazon Web Services (AWS) EC2 
- Služba Amazon relačních databází (AWS RDS) 
- Výpočetní modul (Google Cloud Platform – GCP)

Informace o dalších strategiích migrace najdete v tématu [Přehled migrace SQL Server virtuálních počítačů](sql-server-to-sql-on-azure-vm-migration-overview.md).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Tok procesu migrace":::

## <a name="prerequisites"></a>Požadavky

Migrace na SQL Server na virtuálních počítačích Azure vyžaduje následující: 

- [Pomocník s migrací databáze (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
- [Azure Migrate projekt](../../../migrate/create-manage-projects.md).
- Připravený cílový [SQL Server na virtuálním počítači Azure](../../virtual-machines/windows/create-sql-vm-portal.md) , který má stejnou nebo větší verzi než zdrojový SQL Server.
- [Připojení mezi Azure a](/azure/architecture/reference-architectures/hybrid-networking)místním prostředím.
- [Výběr vhodné strategie migrace](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>Před migrací

Než začnete s migrací, zjistěte topologii prostředí SQL a vyhodnoťte proveditelnost zamýšlené migrace. 

### <a name="discover"></a>Zjišťování

Azure Migrate posuzuje vhodnost migrace místních počítačů, provádí změnu velikosti na základě výkonu a poskytuje odhad nákladů na místní provoz. K naplánování migrace použijte Azure Migrate k [identifikaci stávajících zdrojů dat a podrobností o funkcích](../../../migrate/concepts-assessment-calculation.md) , které vaše SQL Server instance používají. Tento proces zahrnuje kontrolu sítě a identifikaci všech SQL Server instancí ve vaší organizaci pomocí používané verze a funkcí. 

> [!IMPORTANT]
> Při výběru cílového virtuálního počítače Azure pro vaši instanci SQL Server nezapomeňte zvážit [pravidla výkonu pro SQL Server na virtuálních počítačích Azure](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Další nástroje pro zjišťování najdete v tématu [služby a nástroje](../../../dms/dms-tools-matrix.md#business-justification-phase) dostupné pro scénáře migrace dat.


### <a name="assess"></a>Posouzení

Po zjištění všech zdrojů dat použijte [Data Migration Assistant (DMA)](/sql/dma/dma-overview) k vyhodnocení místních instancí SQL Server migrace na instanci SQL Server na virtuálním počítači Azure, abyste pochopili mezery mezi zdrojovou a cílovou instancí. 


> [!NOTE]
> Pokud _neprovádíte upgrade verze_ SQL Server, přeskočte tento krok a přejděte do oddílu [migrace](#migrate) . 


#### <a name="assess-user-databases"></a>Posouzení uživatelských databází 

Data Migration Assistant (DMA) pomáhá migrovat na moderní datovou platformu tím, že detekuje problémy s kompatibilitou, které mohou ovlivnit funkčnost databáze v nové verzi SQL Server. DMA doporučuje vylepšení výkonu a spolehlivosti pro cílové prostředí a také umožňuje přesunout vaše schéma, data a objekty přihlášení ze zdrojového serveru na cílový server.

Další informace najdete v tématu věnovaném [posouzení](/sql/dma/dma-migrateonpremsql) . 


> [!IMPORTANT]
>Na základě typu posouzení se oprávnění požadovaná na zdrojovém SQL Server můžou lišit. 
   > - Pro nástroj pro **vyparitení funkcí** se přihlašovací údaje zadané pro připojení ke zdrojové SQL Server databázi musí nacházet jako členové role serveru *sysadmin* .
   > - Pro poradce pro problémy s kompatibilitou musí poskytnutá pověření obsahovat alespoň `CONNECT SQL` `VIEW SERVER STATE` a `VIEW ANY DEFINITION` oprávnění.
   > - Před spuštěním posouzení zvýrazní rozhraní DMA oprávnění požadovaná pro vybraný poradce.


#### <a name="assess-applications"></a>Posouzení aplikací

Vrstva aplikace obvykle přistupuje k uživatelským databázím, aby zachovala a upravila data.  DMA umožňuje vyhodnotit vrstvu přístupu k datům aplikace dvěma způsoby: 

- Použití zachycených [rozšířených událostí](/sql/relational-databases/extended-events/extended-events) nebo [SQL Server Profiler trasování ](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) vašich uživatelských databází. Můžete také použít [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-capture-trace) k vytvoření protokolu trasování, který lze použít také pro testování a/B.

- Pomocí sady [nástrojů pro migraci Data Access](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) (DAMT), která poskytuje zjišťování a hodnocení dotazů SQL v rámci kódu a slouží k migraci zdrojového kódu aplikace z jedné databázové platformy do jiné. Tento nástroj podporuje různé oblíbené typy souborů, včetně jazyků C# a Java, XML a prostého textu. Návod, jak provést posouzení DAMT, najdete v blogu [Use dmat](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) .

Pomocí DMA [importujte](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) zachycené trasovací soubory nebo soubory DAMT během posuzování uživatelských databází. 


#### <a name="scale-assessments"></a>Hodnocení škálování

Pokud máte více serverů, které vyžadují posouzení DMA, můžete proces automatizovat prostřednictvím [rozhraní příkazového řádku](/sql/dma/dma-commandline). Pomocí rozhraní můžete pro každou instanci SQL Server v oboru migrace předem připravit příkazy posouzení. 

Pro Shrnutí sestav v rámci rozsáhlých Estates je teď možné [do Azure Migrate konsolidovat](/sql/dma/dma-assess-sql-data-estate-to-sqldb)posouzení data MIGRATION Assistant (DMA).

#### <a name="refactor-databases-with-dma"></a>Refaktorovat databáze s využitím DMA

Na základě výsledků posouzení DMA máte k dispozici řadu doporučení pro zajištění, že vaše uživatelské databáze budou fungovat a správně fungovat po migraci. Přímý přístup do paměti (DMA) poskytuje informace o ovlivněných objektech a prostředcích, jak tento problém vyřešit. Doporučuje se, aby všechny zásadní změny a změny chování byly vyřešeny před migrací do produkčního prostředí.

U zastaralých funkcí se můžete rozhodnout pro spuštění vašich uživatelských databází v původním režimu [kompatibility](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) , pokud se chcete vyhnout provádění těchto změn a urychlení migrace. Tím se ale zabrání v [upgradu kompatibility databáze](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) , dokud se nevyřeší vyřazené položky.

Důrazně doporučujeme, abyste při [následné migraci](#post-migration)nastavili a nastavili všechny opravy DMA na cílovou SQL Server databázi.

> [!CAUTION]
> Ne všechny verze SQL Server podporují všechny režimy kompatibility. Ověřte, zda vaše [cílová SQL Server verze](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) podporuje vaši zvolenou kompatibilitu databáze. Například SQL Server 2019 nepodporuje databáze s kompatibilitou úrovně 90 (což je SQL Server 2005). Tyto databáze by vyžadovaly alespoň upgrade na úroveň kompatibility 100.
>

## <a name="migrate"></a>Migrate

Po dokončení kroků před migrací jste připraveni k migraci uživatelských databází a součástí. Migrujte své databáze pomocí preferované [metody migrace](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).  

Následující článek popisuje kroky pro provedení migrace pomocí zálohování a obnovení nebo migrace s minimálními výpadky pomocí zálohování a obnovení spolu s přesouváním protokolu. 

### <a name="backup-and-restore"></a>Zálohování a obnovení

K provedení standardní migrace pomocí zálohování a obnovení použijte následující postup: 

1. Na základě vašich požadavků nastavte připojení k cílovému SQL Server na VIRTUÁLNÍm počítači Azure. Viz [připojení k virtuálnímu počítači s SQL Server v Azure (Správce prostředků)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Pozastavte nebo zastavte všechny aplikace, které používají databáze určené k migraci. 
1. Zajistěte, aby byly uživatelské databáze neaktivní pomocí [režimu Single User](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
1. Proveďte úplnou zálohu databáze do místního umístění.
1. Zkopírujte místní záložní soubory do virtuálního počítače pomocí funkce Vzdálená plocha, [Azure Průzkumník dat](/azure/data-explorer/data-explorer-overview)nebo [nástroje příkazového řádku AzCopy](../../../storage/common/storage-use-azcopy-v10.md) (doporučeno zálohování > 2 TB).
1. Obnovte úplné zálohy databáze na SQL Server na virtuálním počítači Azure.

### <a name="log-shipping--minimize-downtime"></a>Přesouvání protokolu (minimalizace výpadků)

Chcete-li provést migraci s minimálními výpadky pomocí zálohování, obnovení a přesouvání protokolů, postupujte následovně: 

1. Na základě vašich požadavků nastavte připojení k cíli SQL Server na VIRTUÁLNÍm počítači Azure. Viz [připojení k virtuálnímu počítači s SQL Server v Azure (Správce prostředků)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Zajistěte, aby místní databáze uživatelů, které mají být migrovány, byly v plném nebo hromadně protokolovaném modelu obnovení.
1. Proveďte úplnou zálohu databáze do místního umístění a upravte všechny stávající úlohy zálohování databáze tak, aby používaly klíčové slovo [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) pro zachování řetězce protokolu.
1. Zkopírujte místní záložní soubory do virtuálního počítače pomocí funkce Vzdálená plocha, [Azure Průzkumník dat](/azure/data-explorer/data-explorer-overview)nebo [nástroje příkazového řádku AzCopy](../../../storage/common/storage-use-azcopy-v10.md) (doporučeno zálohování >1 TB).
1. Obnovte úplné zálohy databáze na SQL Server na virtuálním počítači Azure.
1. Nastavte [přesouvání protokolů](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) mezi místní databází a cílovými SQL Server na virtuálním počítači Azure. Nezapomeňte znovu inicializovat databáze, protože již byly dokončeny v předchozích krocích.
1. **Přeříznout** na cílový server. 
   1. Pozastavte nebo zastavte aplikace využívající databáze k migraci. 
   1. Zajistěte, aby byly uživatelské databáze neaktivní pomocí [režimu Single User](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
   1. Až budete připraveni, proveďte při [převzetí služeb při selhání](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) místní SQL Server databáze na virtuálním počítači Azure řízený přenosem protokolu.



### <a name="migrating-objects-outside-user-databases"></a>Migrace objektů mimo uživatelské databáze

K dispozici mohou být další SQL Server objekty, které jsou požadovány pro bezproblémové fungování migrace po migraci vašich uživatelských databází. 

Následující tabulka uvádí komponenty seznamu a Doporučené metody migrace, které je možné dokončit před nebo po migraci vašich uživatelských databází: 


| **Funkce** | **Komponenta** | **Metody migrace** |
| --- | --- | --- |
| **Databáze** | Modelování  | Skript s SQL Server Management Studio |
|| Databáze | Naplánujte přesunutí databáze TempDB na [dočasný disk s virtuálním počítačem Azure (SSD](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)). Nezapomeňte vybrat velikost virtuálního počítače, která má dostatečnou místní jednotku SSD, která bude vyhovovat vaší databázi TempDB. |
|| Uživatelské databáze s FILESTREAM |  Použijte metody [zálohování a obnovení](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) pro migraci. Přímý přístup do paměti (DMA) nepodporuje databáze s FILESTREAM. |
| **Zabezpečení** | Přihlášení SQL Server a Windows | K [migraci přihlášení uživatelů](/sql/dma/dma-migrateserverlogins)použijte technologii DMA. |
|| Role SQL Server | Skript s SQL Server Management Studio |
|| Zprostředkovatelé kryptografických služeb | Doporučujeme [převod na používání služby Azure Key Vault](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Tento postup používá [poskytovatele prostředků virtuálního počítače SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md). |
| **Objekty serveru** | Zálohovací zařízení | Nahraďte zálohou databáze pomocí [služby Azure Backup](../../../backup/backup-sql-server-database-azure-vms.md) nebo zapište zálohy do [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 CU2 +). Tento postup používá [poskytovatele prostředků virtuálního počítače SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).|
|| Odkazované servery | Skript s SQL Server Management Studio. |
|| Aktivační události serveru | Skript s SQL Server Management Studio. |
| **Replikace** | Místní publikace | Skript s SQL Server Management Studio. |
|| Místní předplatitelé | Skript s SQL Server Management Studio. |
| **Polybase** | Polybase | Skript s SQL Server Management Studio. |
| **správy** | Databázová pošta | Skript s SQL Server Management Studio. |
| **Agent SQL Server** | Úlohy | Skript s SQL Server Management Studio. |
|| Výstrahy | Skript s SQL Server Management Studio. |
|| Operátory | Skript s SQL Server Management Studio. |
|| Proxy | Skript s SQL Server Management Studio. |
| **Operační systém** | Soubory, sdílené složky | Poznamenejte si všechny další soubory nebo sdílené složky, které používají vaše SQL servery a replikujte na cíl virtuálního počítače Azure. |


## <a name="post-migration"></a>Po migraci

Po úspěšném dokončení fáze migrace Projděte řadu úkolů po migraci, abyste zajistili, že vše funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. To může v některých případech vyžadovat změny v aplikacích.

Použijte všechny databáze Pomocník s migrací Doporučené opravy pro uživatelské databáze. Doporučuje se, abyste zajistili konzistenci a povolili automatizaci.

### <a name="perform-tests"></a>Provést testy

Testovací přístup pro migraci databáze se skládá z následujících aktivit:

1. **Vývoj ověřovacích testů.**  K testování migrace databáze použijte dotazy SQL. Vytváření ověřovacích dotazů pro spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.
2. **Nastavte testovací prostředí.**  Testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.
3. **Spusťte ověřovací testy.**  Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.
4. **Spusťte testy výkonu.**  Spusťte test výkonnosti proti zdroji a cíli a pak Analyzujte a porovnejte výsledky.

> [!TIP]
> Použití [Pomocník pro experimentování s databázemi (DEA)](/sql/dea/database-experimentation-assistant-overview) pro pomoc s vyhodnocením cílového SQL Serverho výkonu.
>

### <a name="optimize"></a>Optimalizace

Fáze po migraci je zásadní pro sjednocení všech problémů s přesností a úplností dat a také s řešením potenciálních problémů s výkonem s úlohou.

Další informace o těchto problémech a konkrétních krocích, jak je zmírnit, najdete v následujících zdrojích informací:

- [Průvodce pro ověřování po migraci a optimalizaci.](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Ladění výkonu v Azure SQL Virtual Machines](../../virtual-machines/windows/performance-guidelines-best-practices.md).
- [Centrum optimalizace nákladů Azure](https://azure.microsoft.com/overview/cost-optimization/).

## <a name="next-steps"></a>Další kroky

- Postup kontroly dostupnosti služeb použitelných pro SQL Server najdete v tématu [Azure Global Infrastructure Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database) .

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat a speciálních úlohách, najdete v článku [služba a nástroje pro migraci dat.](../../../dms/dms-tools-matrix.md)

- Další informace o Azure SQL najdete tady:
   - [Možnosti nasazení](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server na virtuálních počítačích Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete v tématu.
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a úloh migrace do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Informace o licencování najdete v tématu.
   - [Využijte vlastní licenci s Zvýhodněné hybridní využití Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Získejte bezplatnou rozšířenou podporu pro SQL Server 2008 a SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Postup vyhodnocení vrstvy přístupu k aplikaci najdete v tématu [sada Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) .
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům A/B, najdete [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).