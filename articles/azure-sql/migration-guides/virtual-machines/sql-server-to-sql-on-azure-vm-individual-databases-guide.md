---
title: 'SQL Server SQL Server v Azure Virtual Machines: Průvodce migrací'
description: V této příručce se dozvíte, jak migrovat jednotlivé databáze SQL Server a SQL Server na Azure Virtual Machines.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e7fc4bacd73cec0fdab3117ada190fb7964b4282
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550876"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-virtual-machines"></a>Průvodce migrací: SQL Server SQL Server v Azure Virtual Machines

[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

V této příručce se dozvíte, jak *zjišťovat*, *hodnotit* a *migrovat* uživatelské databáze z SQL Server do instance SQL Server v Azure Virtual Machines pomocí zálohování a obnovení a přesouvání protokolu, který používá [Data Migration Assistant](/sql/dma/dma-overview) k posouzení.

Můžete migrovat SQL Server spuštěná místně nebo na:

- SQL Server na virtuálních počítačích (VM).
- Amazon Web Services (AWS) EC2.
- Služba Amazon relačních databází (AWS RDS).
- Výpočetní modul (Google Cloud Platform [GCP]).

Informace o dalších strategiích migrace najdete v tématu [Přehled migrace virtuálních počítačů SQL Server](sql-server-to-sql-on-azure-vm-migration-overview.md). Další příručky k migraci najdete v tématu [Příručky k Azure Database Migration](https://docs.microsoft.com/data-migration).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Diagram znázorňující tok procesu migrace.":::

## <a name="prerequisites"></a>Požadavky

Migrace na SQL Server v Azure Virtual Machines vyžaduje následující zdroje:

- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- [Azure Migrate projekt](../../../migrate/create-manage-projects.md).
- Připravený cílový [SQL Server instance Azure Virtual Machines](../../virtual-machines/windows/create-sql-vm-portal.md) , která je stejná nebo vyšší verze než zdroj SQL Server.
- [Připojení mezi Azure a](/azure/architecture/reference-architectures/hybrid-networking)místním prostředím.
- [Výběr vhodné strategie migrace](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>Před migrací

Než začnete s migrací, je nutné zjistit topologii prostředí SQL a posoudit proveditelnost zamýšlené migrace.

### <a name="discover"></a>Zjišťování

Azure Migrate posuzuje vhodnost migrace místních počítačů, provádí změnu velikosti na základě výkonu a poskytuje odhad nákladů na místní provoz. K naplánování migrace použijte Azure Migrate k [identifikaci stávajících zdrojů dat a podrobností o funkcích](../../../migrate/concepts-assessment-calculation.md) , které vaše SQL Server instance používají. Tento proces zahrnuje kontrolu sítě a identifikaci všech SQL Server instancí ve vaší organizaci pomocí používané verze a funkcí.

> [!IMPORTANT]
> Když pro instanci SQL Server zvolíte cílový virtuální počítač Azure, nezapomeňte zvážit [pravidla výkonu pro SQL Server v Azure Virtual Machines](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Další nástroje pro zjišťování najdete v tématu [služby a nástroje](../../../dms/dms-tools-matrix.md#business-justification-phase) , které jsou k dispozici pro scénáře migrace dat.

### <a name="assess"></a>Posouzení

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Po zjištění všech zdrojů dat použijte [Data Migration Assistant](/sql/dma/dma-overview) k vyhodnocení místních SQL Server instancí, které migrují na instanci SQL Server ve službě Azure Virtual Machines pro pochopení rozdílů mezi zdrojovou a cílovou instancí.

> [!NOTE]
> Pokud _neprovádíte upgrade verze_ SQL Server, přeskočte tento krok a přejděte do oddílu [migrace](#migrate) .

#### <a name="assess-user-databases"></a>Posouzení uživatelských databází

Data Migration Assistant vám pomůže s migrací na moderní datovou platformu tím, že detekuje problémy s kompatibilitou, které mohou ovlivnit funkčnost databáze v nové verzi SQL Server. Data Migration Assistant doporučuje vylepšení výkonu a spolehlivosti cílového prostředí a také umožňuje přesunout vaše schéma, data a objekty přihlášení ze zdrojového serveru na cílový server.

Další informace najdete v tématu [posouzení](/sql/dma/dma-migrateonpremsql).

> [!IMPORTANT]
>Na základě typu posouzení se oprávnění požadovaná na zdrojovém SQL Server můžou lišit:
   > - Pro nástroj pro *vyparitení funkcí* se přihlašovací údaje zadané pro připojení ke zdrojové SQL Server databázi musí nacházet jako členové role serveru *sysadmin* .
   > - Pro poradce pro *problémy s kompatibilitou* musí poskytnutá pověření obsahovat alespoň `CONNECT SQL` `VIEW SERVER STATE` oprávnění, a `VIEW ANY DEFINITION` .
   > - Data Migration Assistant před spuštěním posouzení zvýrazní oprávnění požadovaná pro vybraný poradce.

#### <a name="assess-the-applications"></a>Posouzení aplikací

Vrstva aplikace obvykle přistupuje k uživatelským databázím, aby zachovala a upravila data. Data Migration Assistant může vyhodnotit vrstvu přístupu k datům aplikace dvěma způsoby:

- Pomocí zaznamenaných [rozšířených událostí](/sql/relational-databases/extended-events/extended-events) nebo [SQL Server Profiler trasování](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) vašich uživatelských databází. Můžete také použít [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-capture-trace) k vytvoření protokolu trasování, který lze použít také pro testování a/B.
- Pomocí sady [nástrojů pro migraci dat (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit), která poskytuje zjišťování a hodnocení dotazů SQL v rámci kódu a slouží k migraci zdrojového kódu aplikace z jedné databázové platformy do jiné. Tento nástroj podporuje oblíbené typy souborů jako C#, Java, XML a prostý text. Návod, jak provést vyhodnocení sady nástrojů pro migraci Data Access, najdete v blogovém příspěvku [použití Data Migration Assistant](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) .

Během posuzování uživatelských databází použijte Data Migration Assistant k [importu](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) souborů trasovacího trasování nebo souborů sady pro migraci dat pro přístup k datům.

#### <a name="assessments-at-scale"></a>Hodnocení ve velkém měřítku

Pokud máte více serverů, které vyžadují Data Migration Assistant posouzení, můžete proces automatizovat pomocí [rozhraní příkazového řádku](/sql/dma/dma-commandline). Pomocí rozhraní můžete pro každou instanci SQL Server v oboru migrace předem připravit příkazy posouzení.

Pro Shrnutí sestav ve velkých Estates se teď Data Migration Assistant posouzení dají [konsolidovat do Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-data-migration-assistant"></a>Refaktoring databází pomocí Data Migration Assistant

Na základě výsledků posouzení Data Migration Assistant můžete mít řadu doporučení, abyste zajistili, že vaše uživatelské databáze budou fungovat a správně fungovat po migraci. Data Migration Assistant poskytuje podrobné informace o ovlivněných objektech a prostředcích pro řešení jednotlivých problémů. Před zahájením migrace do produkčního prostředí nezapomeňte vyřešit všechny zásadní změny a změny chování.

U zastaralých funkcí se můžete rozhodnout, jestli chcete spouštět své uživatelské databáze v původním režimu [kompatibility](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) , pokud se chcete vyhnout provádění těchto změn a urychlení migrace. Tato akce zabrání v [upgradu kompatibility databáze](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) , dokud nebyly vyřešeny zastaralé položky.

Musíte skriptovat všechny opravy Data Migration Assistant a použít je v cílové databázi SQL Server během fáze [po migraci](#post-migration) .

> [!CAUTION]
> Ne všechny verze SQL Server podporují všechny režimy kompatibility. Ověřte, zda vaše [cílová SQL Server verze](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) podporuje vaši zvolenou kompatibilitu databáze. Například SQL Server 2019 nepodporuje databáze s kompatibilitou úrovně 90 (což je SQL Server 2005). Tyto databáze by vyžadovaly alespoň upgrade na úroveň kompatibility 100.
>

## <a name="migrate"></a>Migrate

Po dokončení kroků před migrací jste připraveni k migraci uživatelských databází a součástí. Migrujte své databáze pomocí preferované [metody migrace](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

V následujících částech najdete postup migrace pomocí zálohování a obnovení nebo migrace s minimálními výpadky pomocí zálohování a obnovení spolu s přesouváním protokolu.

### <a name="backup-and-restore"></a>Zálohování a obnovení

Postup při provádění standardní migrace pomocí zálohování a obnovení:

1. Na základě vašich požadavků nastavte připojení k SQL Server v Azure Virtual Machines. Další informace najdete v tématu [připojení k virtuálnímu počítači s SQL Server v Azure (Správce prostředků)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Pozastavte nebo zastavte všechny aplikace, které používají databáze určené k migraci.
1. Zajistěte, aby uživatelské databáze byly neaktivní pomocí [režimu Single User](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
1. Proveďte úplnou zálohu databáze do místního umístění.
1. Zkopírujte své místní záložní soubory do virtuálního počítače pomocí vzdálené plochy, [Azure Průzkumník dat](/azure/data-explorer/data-explorer-overview)nebo [nástroje příkazového řádku AzCopy](../../../storage/common/storage-use-azcopy-v10.md). (Doporučuje se zálohování na více než 2 TB.)
1. Obnovte úplné zálohy databáze na SQL Server v Azure Virtual Machines.

### <a name="log-shipping-minimize-downtime"></a>Přesouvání protokolu (minimalizace výpadků)

Provedení migrace s minimálními výpadky pomocí zálohování a obnovení a přesouvání protokolu:

1. V závislosti na vašich požadavcích nastavte připojení k SQL Server v Azure Virtual Machines. Další informace najdete v tématu [připojení k virtuálnímu počítači s SQL Server v Azure (Správce prostředků)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Ujistěte se, že místní uživatelské databáze, které se mají migrovat, jsou v modelu úplného nebo hromadně protokolovaného obnovení.
1. Proveďte úplnou zálohu databáze do místního umístění a upravte všechny existující úlohy zálohování databáze tak, aby se pomocí klíčového slova [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) zachoval řetězec protokolu.
1. Zkopírujte své místní záložní soubory do virtuálního počítače pomocí vzdálené plochy, [Azure Průzkumník dat](/azure/data-explorer/data-explorer-overview)nebo [nástroje příkazového řádku AzCopy](../../../storage/common/storage-use-azcopy-v10.md). (Doporučujeme zálohování o více než 1 TB.)
1. Obnovení úplných záloh databáze na SQL Server v Azure Virtual Machines.
1. Nastavte [přesouvání protokolů](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) mezi místní databází a SQL Server v Azure Virtual Machines. Ujistěte se, že není nutné znovu inicializovat databáze, protože tato úloha již byla dokončena v předchozích krocích.
1. Přeříznout na cílový server.
   1. Pozastavení nebo zastavení aplikací pomocí databází k migraci.
   1. Zajistěte, aby uživatelské databáze byly neaktivní pomocí [režimu Single User](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
   1. Až budete připraveni, proveďte [převzetí služeb při selhání](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) místních databází při přenosu protokolu a SQL Server v Azure Virtual Machines.

### <a name="migrate-objects-outside-user-databases"></a>Migrace objektů mimo uživatelské databáze

K bezproblémovému provozu migrace vašich uživatelských databází může být vyžadováno více SQL Server objektů.

Následující tabulka uvádí seznam komponent a Doporučené metody migrace, které je možné dokončit před migrací uživatelských databází nebo po ní.

| **Funkce** | **Komponenta** | **Metody migrace** |
| --- | --- | --- |
| **Databáze** | Modelování | Skript s SQL Server Management Studio. |
|| Databáze | K dosažení nejlepšího výkonu Naplánujte přesun databáze tempDB na [dočasný disk s virtuálním počítačem Azure (SSD)](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk). Nezapomeňte vybrat velikost virtuálního počítače, která má dostatečnou místní jednotku SSD, která bude vyhovovat vaší databázi tempDB. |
|| Uživatelské databáze s FileStream | Použijte metody [zálohování a obnovení](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) pro migraci. Data Migration Assistant nepodporuje databáze s FileStream. |
| **Zabezpečení** | Přihlášení SQL Server a Windows | K [migraci přihlášení uživatelů](/sql/dma/dma-migrateserverlogins)použijte Data Migration Assistant. |
|| Role SQL Server | Skript s SQL Server Management Studio. |
|| Zprostředkovatelé kryptografických služeb | Doporučujeme [převést na použití Azure Key Vault](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Tento postup používá [poskytovatele prostředků virtuálního počítače SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md). |
| **Objekty serveru** | Zálohovací zařízení | Nahraďte zálohou databáze pomocí [Azure Backup](../../../backup/backup-sql-server-database-azure-vms.md)nebo zapište zálohy do [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 CU2 +). Tento postup používá [poskytovatele prostředků virtuálního počítače SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).|
|| Propojené servery | Skript s SQL Server Management Studio. |
|| Aktivační události serveru | Skript s SQL Server Management Studio. |
| **Replikace** | Místní publikace | Skript s SQL Server Management Studio. |
|| Místní předplatitelé | Skript s SQL Server Management Studio. |
| **PolyBase** | PolyBase | Skript s SQL Server Management Studio. |
| **správy** | Databázový e-mail | Skript s SQL Server Management Studio. |
| **Agent SQL Server** | Úlohy | Skript s SQL Server Management Studio. |
|| Výstrahy | Skript s SQL Server Management Studio. |
|| Operátory | Skript s SQL Server Management Studio. |
|| Proxy | Skript s SQL Server Management Studio. |
| **Operační systém** | Soubory, sdílené složky | Poznamenejte si jakékoli jiné soubory nebo sdílené složky, které používají vaše SQL servery a replikujte do cíle Azure Virtual Machines. |

## <a name="post-migration"></a>Po migraci

Po úspěšném dokončení fáze migrace je potřeba dokončit sérii úloh po migraci, abyste měli jistotu, že všechno funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Splnění této úlohy může v některých případech vyžadovat změny v aplikacích.

Použijte jakékoli opravy Doporučené Data Migration Assistantmi uživateli databáze. Chcete-li zajistit konzistenci a povolení automatizace, je nutné tyto opravy vyskriptovat.

### <a name="perform-tests"></a>Provést testy

Testovací přístup k migraci databáze se skládá z následujících aktivit:

1. **Vývoj ověřovacích testů**: k otestování migrace databáze je nutné použít dotazy SQL. Vytváření ověřovacích dotazů pro spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.
1. **Nastavení testovacího prostředí**: testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.
1. **Spustit ověřovací testy**: Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.
1. **Spustit testy výkonu**: spustit testy výkonu proti zdroji a cíli a pak výsledky analyzovat a porovnat.

> [!TIP]
> Použijte [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview) pro pomoc s vyhodnocením cílového SQL Server výkonu.

### <a name="optimize"></a>Optimalizace

Fáze po migraci je zásadní pro sjednocení problémů s přesností dat, ověřování úplnosti a řešení potenciálních problémů s výkonem s úlohou.

Další informace o těchto problémech a krocích pro jejich zmírnění najdete v tématech:

- [Průvodce pro ověřování po migraci a optimalizace](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Ladění výkonu ve virtuálních počítačích Azure SQL](../../virtual-machines/windows/performance-guidelines-best-practices.md)
- [Centrum optimalizace nákladů Azure](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit dostupnost služeb, které se vztahují na SQL Server, přečtěte si téma [Azure Global Infrastructure Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).
- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat a speciálních úlohách, najdete v tématu [služby a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).
- Další informace o Azure SQL najdete tady:
   - [Možnosti nasazení](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server na Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete tady:
   - [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   - [Osvědčené postupy pro výpočet nákladů a velikosti úloh pro migraci do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Informace o licencování najdete v tématech:
   - [Využijte vlastní licenci s Zvýhodněné hybridní využití Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Získejte bezplatnou rozšířenou podporu pro SQL Server 2008 a SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Chcete-li vyhodnotit vrstvu přístupu aplikace, přečtěte si část [Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Informace o tom, jak provést testování A/B pro vrstvu přístupu k datům, najdete v tématu [přehled Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).
