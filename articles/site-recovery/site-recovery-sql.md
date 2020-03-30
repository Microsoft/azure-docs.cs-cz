---
title: Nastavení zotavení po havárii pro SQL Server pomocí Azure Site Recovery
description: Tento článek popisuje, jak nastavit zotavení po havárii pro SQL Server pomocí SQL Server a Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 429f46156da728bbc24108090eac8c04f68da71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084735"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Nastavení zotavení po havárii pro SQL Server

Tento článek popisuje, jak pomoci chránit SQL Server back-end aplikace. Uděláte to pomocí kombinace sql server kontinuity podnikání a zotavení po havárii (BCDR) technologie a [Azure Site Recovery](site-recovery-overview.md).

Než začnete, ujistěte se, že rozumíte možnostem zotavení po havárii serveru SQL Server. Mezi tyto možnosti patří:

* Clustering s podporou převzetí služeb při selhání
* Vždy na skupinách dostupnosti
* Zrcadlení databáze
* Přesouvání protokolu
* Aktivní geografická replikace
* Skupiny automatického převzetí služeb při selhání

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Kombinace technologií BCDR s obnovou webu

Vaše volba bcdr technologie pro obnovení instancí SERVERU SQL Server by měla být založena na cíle cíle doby obnovení (RTO) a cíle bodu obnovení (RPO), jak je popsáno v následující tabulce. Zkombinujte site recovery s operací převzetí služeb při selhání zvolené technologie a orchestrate recovery celé aplikace.

Typ nasazení | BcDR technologie | Očekávané RTO pro SQL Server | Očekávané rpo pro SQL Server |
--- | --- | --- | ---
SQL Server na infrastruktuře Azure jako služba (IaaS) virtuální počítač (VM) nebo na místní úrovni.| [Skupina dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | Čas, který byl čas, který byl proveden k tomu, aby byla sekundární replika provedena jako primární. | Vzhledem k tomu, že replikace na sekundární repliku je asynchronní, dochází ke ztrátě některých dat.
SQL Server na virtuálním počítači Azure IaaS nebo v místním prostředí.| [Clustering s podporou převzetí služeb při selhání (vždy na FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | Čas, který trvalo převzetí služeb při selhání mezi uzly. | Protože always on FCI používá sdílené úložiště, stejné zobrazení instance úložiště je k dispozici při převzetí služeb při selhání.
SQL Server na virtuálním počítači Azure IaaS nebo v místním prostředí.| [Zrcadlení databáze (režim vysokého výkonu)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | Doba vynutit službu, která používá zrcadlový server jako teplý pohotovostní server. | Replikace je asynchronní. Zrcadlová databáze může poněkud zaostávat za primární databáze. Zpoždění je obvykle malé. Ale může se stát velkým, pokud je systém primárního nebo zrcadlového serveru pod velkým zatížením.<br/><br/>Přesouvání protokolu může být doplňkem zrcadlení databáze. Je to příznivá alternativa k zrcadlení asynchronní databáze.
SQL jako platforma jako služba (PaaS) v Azure.<br/><br/>Tento typ nasazení zahrnuje elastické fondy a servery Azure SQL Database. | Aktivní geografická replikace | 30 sekund po aktivaci převzetí služeb při selhání.<br/><br/>Při aktivaci převzetí služeb při selhání pro jednu ze sekundárních databází, všechny ostatní sekundární jsou automaticky propojeny s novou primární. | RPO pět sekund.<br/><br/>Aktivní geografická replikace používá technologii Always On serveru SQL Server. Asynchronně replikuje potvrzené transakce v primární databázi do sekundární databáze pomocí izolace snímek.<br/><br/>Sekundární data je zaručeno, že nikdy částečné transakce.
SQL jako PaaS nakonfigurované s aktivní geografickou replikací v Azure.<br/><br/>Tento typ nasazení zahrnuje spravovanou instanci databáze SQL, elastické fondy a servery databáze SQL. | Skupiny automatického převzetí služeb při selhání | RTO jedné hodiny. | RPO pět sekund.<br/><br/>Skupiny s automatickým převzetím služeb při selhání poskytují sémantiku skupiny nad aktivní geografickou replikací. Ale stejný mechanismus asynchronní replikace se používá.
SQL Server na virtuálním počítači Azure IaaS nebo v místním prostředí.| Replikace s obnovením lokality Azure | RTO je obvykle kratší než 15 minut. Další informace naleznete v souboru [RTO SLA poskytovaném site recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Jedna hodina pro konzistenci aplikace a pět minut pro konzistenci selhání. Pokud hledáte nižší RPO, použijte jiné bcdr technologie.

> [!NOTE]
> Několik důležitých aspektů, když pomáháte chránit úlohy SQL pomocí site recovery:
> * Site Recovery je aplikace agnostik. Site Recovery může pomoci chránit všechny verze SQL Server, který je nasazen v podporovaném operačním systému. Další informace naleznete v [matici podpory pro obnovení](vmware-physical-azure-support-matrix.md#replicated-machines) replikovaných počítačů.
> * Obnovení webu můžete použít pro jakékoli nasazení v Azure, Hyper-V, VMware nebo fyzické infrastruktuře. Postupujte podle pokynů na konci tohoto článku o [tom, jak pomoci chránit cluster serveru SQL Server](#how-to-help-protect-a-sql-server-cluster) s site recovery.
> * Ujistěte se, že rychlost změny dat pozorovaná na stroji je v [mezích site recovery](vmware-physical-azure-support-matrix.md#churn-limits). Rychlost změny se měří v bajtech zápisu za sekundu. U počítačů se systémem Windows můžete tuto rychlost změny zobrazit výběrem karty **Výkon** ve Správci úloh. Sledujte rychlost zápisu pro každý disk.
> * Site Recovery podporuje replikaci instancí clusteru s podporou převzetí služeb při selhání v rozhraní Storage Spaces Direct. Další informace naleznete v tématu [povolení přímé replikace prostorů úložiště](azure-to-azure-how-to-enable-replication-s2d-vms.md).

## <a name="disaster-recovery-of-an-application"></a>Zotavení po havárii aplikace

Obnovení webu orchestruje převzetí služeb při selhání testu a převzetí služeb při selhání celé aplikace pomocí plánů obnovení.

Existují některé předpoklady, aby váš plán obnovy byl plně přizpůsoben podle vašich potřeb. Jakékoli nasazení serveru SQL Server obvykle vyžaduje nasazení služby Active Directory. Potřebuje také připojení pro vaši aplikační vrstvu.

### <a name="step-1-set-up-active-directory"></a>Krok 1: Nastavení služby Active Directory

Nastavte službu Active Directory v sekundární lokalitě pro obnovení, aby sql server správně běžel.

* **Malý podnik**: Máte malý počet aplikací a jeden řadič domény pro místní lokalitu. Pokud chcete přepojit služby při selhání celé lokality, použijte replikaci obnovení lokality. Tato služba replikuje řadič domény do sekundárního datového centra nebo do Azure.
* **Střední až velké podniky**: Možná budete muset nastavit další řadiče domény.
  - Pokud máte velký počet aplikací, máte doménovou strukturu služby Active Directory a chcete převzetí služeb při selhání podle aplikace nebo úlohy, nastavte jiný řadič domény v sekundárním datovém centru nebo v Azure.
  -  Pokud k obnovení do vzdálené lokality používáte skupiny dostupnosti Always On, nastavte jiný řadič domény v sekundární lokalitě nebo v Azure. Tento řadič domény se používá pro obnovenou instanci serveru SQL Server.

Pokyny v tomto článku předpokládají, že řadič domény je k dispozici v sekundárním umístění. Další informace naleznete v postupech [ochrany služby Active Directory pomocí nástroje Site Recovery](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Krok 2: Zajištění připojení k ostatním vrstvám

Po databázové vrstvy běží v oblasti cílové Azure, ujistěte se, že máte připojení s aplikací a webové vrstvy. Provést nezbytné kroky předem k ověření připojení s test převzetí služeb při selhání.

Informace o tom, jak můžete navrhovat aplikace pro účely připojení, naleznete v těchto příkladech:

* [Návrh aplikace pro cloudové zotavení po havárii](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Strategie zotavení po havárii v elastickém bazénu](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Krok 3: Spolupracujte se skupinami s nepřetržitou, aktivní geografickou replikací a automatickým převzetím služeb při selhání

BcDR technologie vždy zapnuto, aktivní geografická replikace a skupiny automatického převzetí služeb při selhání mají sekundární repliky SQL Serveru spuštěné v cílové oblasti Azure. Prvním krokem převzetí služeb při selhání aplikace je určení této repliky jako primární. Tento krok předpokládá, že již máte řadič domény v sekundárním. Krok nemusí být nutné, pokud se rozhodnete provést automatické převzetí služeb při selhání. Převzetí služeb při selhání nad webovými a aplikačními vrstvami až po dokončení převzetí služeb při selhání databáze.

> [!NOTE]
> Pokud jste pomohli chránit počítače SQL pomocí site recovery, stačí vytvořit skupinu obnovení těchto počítačů a přidat jejich převzetí služeb při selhání v plánu obnovení.

[Vytvořte plán obnovení](site-recovery-create-recovery-plans.md) s virtuálními počítači aplikační a webové vrstvy. Následující kroky ukazují, jak přidat převzetí služeb při selhání databázové vrstvy:

1. Importujte skripty do skupiny dostupnosti SQL převzetí služeb při selhání ve [virtuálním počítači Správce prostředků](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) i v [klasickém virtuálním počítači](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Importujte skripty do svého účtu Azure Automation.

    [![Obrázek loga "Nasazení do Azure"](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Přidejte skript ASR-SQL-FailoverAG jako předběžnou akci první skupiny plánu obnovení.

1. Podle pokynů dostupných ve skriptu vytvořte proměnnou automatizace. Tato proměnná obsahuje název skupin dostupnosti.

### <a name="step-4-conduct-a-test-failover"></a>Krok 4: Provedení zkušebního převzetí služeb při selhání

Některé bcdr technologie, jako je například SQL Always On, nativně nepodporují převzetí služeb při selhání testu. Následující přístup doporučujeme *pouze při použití těchto technologií*.

1. Nastavte [Azure Backup](../backup/backup-azure-arm-vms.md) na virtuálním počítači, který je hostitelem repliky skupiny dostupnosti v Azure.

1. Před aktivací testu převzetí služeb při selhání plánu obnovení obnovit virtuální ho z zálohy provedené v předchozím kroku.

    ![Snímek obrazovky zobrazující okno pro obnovení konfigurace z Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Vynuťte kvora](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) ve virtuálním počítače, který byl obnoven ze zálohy.

1. Aktualizujte IP adresu naslouchací proces, aby byla adresa k dispozici v síti převzetí služeb při selhání test.

    ![Snímek obrazovky s oknem pravidel a dialogem o vlastnostech adresy IP](./media/site-recovery-sql/update-listener-ip.png)

1. Převezte posluchače do režimu online.

    ![Snímek obrazovky okna s popiskem Content_AG zobrazující názvy a stavy serverů](./media/site-recovery-sql/bring-listener-online.png)

1. Ujistěte se, že nástroje pro vyrovnávání zatížení v síti s podporou převzetí služeb při selhání má jednu adresu IP, z fondu front-endip adres, který odpovídá každému naslouchací proces skupiny dostupnosti, a s virtuálním serverem SQL Server v back-endovém fondu.

     ![Snímek obrazovky s oknem s názvem SQL-AlwaysOn-LB - Frontend IP pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Snímek obrazovky s oknem s názvem "SQL-AlwaysOn-LB - Back-end OVÝ fond IP](./media/site-recovery-sql/create-load-balancer2.png)

1. V pozdějších skupinách obnovení přidejte převzetí služeb při selhání aplikační vrstvy následované webovou vrstvou pro tento plán obnovení.

1. Proveďte zkušební převzetí služeb při selhání plánu obnovení k testování převzetí služeb při selhání aplikace od konce do konce.

## <a name="steps-to-do-a-failover"></a>Kroky k převzetí služeb při selhání

Po přidání skriptu v kroku 3 a jeho ověření v kroku 4 můžete provést převzetí služeb při selhání plánu obnovení vytvořeného v kroku 3.

Kroky převzetí služeb při selhání pro aplikační a webové vrstvy by měly být stejné v testovacích plánech obnovení převzetí služeb při selhání i při převzetí služeb při selhání.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Jak chránit cluster serveru SQL Server

U clusteru se systémem SQL Server Standard Edition nebo SQL Server 2008 R2 doporučujeme k ochraně serveru SQL Server použít replikaci obnovení sítě.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure to Azure a místní do Azure

Site Recovery neposkytuje podporu clusteru hostů při replikaci do oblasti Azure. SQL Server Standard edition také neposkytuje řešení pro zotavení po havárii s nízkými náklady. V tomto scénáři doporučujeme chránit cluster SQL Server na samostatnou instanci serveru SQL Server v primárním umístění a obnovit ji v sekundárním umístění.

1. Nakonfigurujte další samostatnou instanci SQL Serveru v primární oblasti Azure nebo v místní lokalitě.

1. Nakonfigurujte instanci tak, aby sloužila jako zrcadlo pro databáze, které chcete chránit. Nakonfigurujte zrcadlení v režimu s vysokou bezpečností.

1. Konfigurace site recovery v primární lokalitě pro [Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](site-recovery-hyper-v-site-to-azure.md)nebo [Virtuální počítače VMware a fyzické servery](site-recovery-vmware-to-azure-classic.md).

1. Replikaci nové instance serveru SQL Server do sekundární lokality použijte replikaci obnovení lokality. Vzhledem k tomu, že se jedná o vysoce bezpečnostní zrcadlovou kopii, bude synchronizována s primárním clusterem, ale replikována pomocí replikace obnovení sítě.

   ![Obrázek standardního clusteru, který zobrazuje vztah a tok mezi primární lokalitou, site recovery a Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Důležité informace o navrácení služeb po selhání

U clusterů SQL Server Standard vyžaduje navrácení služeb po obnovení po neplánovaném převzetí služeb při selhání zálohování a obnovení služby SQL Server. Tato operace se provádí z instance zrcadlení do původního clusteru s obnovením zrcadlení.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Jak sql server získat licenci při použití s site recovery?

Replikace obnovení sítě pro SQL Server je zahrnuta ve výhodách zotavení po havárii programu Software Assurance. Toto pokrytí platí pro všechny scénáře obnovení webu: místní pro azure zotavení po havárii a zotavení po havárii mezi oblastmi Azure IaaS. Další informace najdete v [tématu Ceny azure site recovery.](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-site-recovery-support-my-sql-server-version"></a>Bude site recovery podporovat verzi serveru SQL Server?

Site Recovery je aplikace agnostik. Site Recovery může pomoci chránit všechny verze SQL Server, který je nasazen v podporovaném operačním systému. Další informace naleznete v [matici podpory pro obnovení](vmware-physical-azure-support-matrix.md#replicated-machines) replikovaných počítačů.

## <a name="next-steps"></a>Další kroky

* Další informace o [architektuře site recovery](site-recovery-components.md).
* Sql Server v Azure najdete další informace o [řešeních s vysokou dostupností](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) pro obnovení v sekundární oblasti Azure.
* V databázi SQL Database najdete další informace o [kontinuitě podnikání](../sql-database/sql-database-business-continuity.md) a možnostech [vysoké dostupnosti](../sql-database/sql-database-high-availability.md) pro obnovení v sekundární oblasti Azure.
* Pro sql server počítače v místním, další informace o [možnosti vysoké dostupnosti](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) pro obnovení ve virtuálních počítačích Azure.
