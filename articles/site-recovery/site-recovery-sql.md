---
title: Nastavení zotavení po havárii pro SQL Server s SQL serverem a Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak nastavit zotavení po havárii pro SQL Server pomocí SQL serveru a Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 1c44b10b54a5f58dff1aecf36c3633cc8ffbd8f0
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491780"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Nastavení zotavení po havárii pro SQL Server

Tento článek popisuje, jak chránit SQL Server back-endu aplikace pomocí kombinace kontinuity SQL serveru a technologie (BCDR) pro obnovení po havárii, a [Azure Site Recovery](site-recovery-overview.md).

Než začnete, ujistěte se, že chápete, že možnosti zotavení po havárii serveru SQL Server, včetně clustering převzetí služeb při selhání skupiny dostupnosti Always On, zrcadlení databází, přesouvání, aktivní geografické replikace a skupiny – automatické převzetí služeb při selhání protokolu.

## <a name="dr-recommendation-for-integration-of-sql-server-bcdr-technologies-with-site-recovery"></a>Zotavení po Havárii doporučení pro integraci produktů technologiemi BCDR SQL serveru pomocí služby Site Recovery

Volbu technologie BCDR pro obnovení SQL servery by měla být podle svých potřeb RTO a RPO jak je uvedeno následující tabulka. Po provedení široké možnosti volby Site Recovery je možné integrovat s těmito technologiemi, abychom Orchestrace zotavení celé aplikace operaci převzetí služeb při selhání.

**Typ nasazení** | **Technologie BCDR** | **Očekávaný RTO pro SQL** | **Očekávané cíle bodu obnovení pro SQL** |
--- | --- | --- | ---
SQL Server na virtuálním počítači Azure IaaS nebo na on-premises| **[Skupina dostupnosti AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)** | Ekvivalent čas potřebný k vytvoření sekundární repliky jako primární | Replikace je asynchronní do sekundární repliky, a proto došlo ztrátě dat.
SQL Server na virtuálním počítači Azure IaaS nebo na on-premises| **[Převzetí služeb clusteringu (vždy na FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017)** | Ekvivalent čas potřebný k převzetí služeb při selhání mezi uzly | Používá sdílené úložiště, a proto zobrazení stejné úložiště instance k dispozici na převzetí služeb při selhání.
SQL Server na virtuálním počítači Azure IaaS nebo na on-premises| **[Zrcadlení (výkonné režim)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017)** | Ekvivalent čas potřebný k vynucení službu, která používá jako záložní pohotovostní server zrcadlový server. | Je asynchronní replikace. Zrcadlové databáze může být prodleva trochu za primární databáze. Rozdíl je obvykle malé howvever, může být značné, pokud je objekt zabezpečení nebo zrcadlový server systému při velkém zatížení.<br></br>Může být dodatek k zrcadlení databáze přesouvání protokolu a uspokojivým alternativu, která umožňuje asynchronní zrcadlení
SQL jako PaaS v Azure<br></br>(Elastické fondy, servery SQL database) | **Aktivní geografická replikace** | 30 sekund, po jeho aktivaci<br></br>Při aktivaci převzetí služeb při selhání na jednu sekundární databází, všechny ostatní sekundární repliky jsou automaticky propojení se nový primární. | Cíl bodu obnovení 5 sekund<br></br>Aktivní geografická replikace využívá technologii AlwaysOn SQL serveru na asynchronní potvrzené transakce u primární databáze replikace do sekundární databáze pomocí izolace snímku. <br></br>Je zaručeno, že sekundární data nikdy nemůžete mít částečné transakce.
SQL jako PaaS nakonfigurovaný s aktivní geografickou replikaci v Azure<br></br>(SQL Database Managed Instance, elastické fondy SQL databázové servery) | **Skupiny automatického převzetí služeb při selhání** | RTO 1 hodina | Cíl bodu obnovení 5 sekund<br></br>Automatické převzetí služeb při selhání skupiny poskytují sémantiku skupiny nad aktivní geografickou replikaci, ale používá stejné mechanismu asynchronní replikace.
SQL Server na virtuálním počítači Azure IaaS nebo na on-premises| **Replikace Azure Site Recovery** | Obvykle méně než 15 minut. [Přečtěte si další](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) Další informace o smlouvě SLA RTO, poskytuje Azure Site Recovery. | 1 hodina pro zajištění konzistence aplikace a 5 minut, než konzistenci při selhání. 

> [!NOTE]
> Několik důležité aspekty při ochraně pracovních zátěží SQL pomocí Azure Site Recovery:
> * Azure Site Recovery je nezávislý na aplikace, a proto všechny verze systému SQL server, který je nasazen na podporovaný operační systém může být chráněn Azure Site Recovery. [Další informace](vmware-physical-azure-support-matrix.md#replicated-machines).
> * Můžete pomocí Site Recovery pro každé nasazení v Azure a Hyper-V, VMware nebo fyzické infrastruktuře. Postupujte prosím podle [pokyny](site-recovery-sql.md#how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2) na konci dokumentu o ochraně clusteru SQL serveru pomocí Azure Site Recovery.
> * Ujistěte se, že data změnit rychlost (zapsané bajty za sekundu) pozorovaný na tento počítač je v rámci [omezení Site Recovery](vmware-physical-azure-support-matrix.md#churn-limits). Pro počítače s windows můžete to zobrazit na kartě výkonu ve Správci úloh. Sledujte zápisu rychlost pro každý disk.
> * Azure Site Recovery podporuje replikaci instance clusteru převzetí služeb při selhání na prostory úložiště – přímé. [Další informace](azure-to-azure-how-to-enable-replication-s2d-vms.md).
 

## <a name="disaster-recovery-of-application"></a>Zotavení po havárii aplikace

**Azure Site Recovery orchestruje testovací převzetí služeb při selhání a převzetí služeb při selhání celé aplikace pomocí plánů obnovení.** 

Existují některé předpoklady k zajištění, že se že plán obnovení je plně přizpůsobit podle vašim požadavkům. Žádné nasazení systému SQL Server obvykle potřebuje služby Active Directory. Také potřebuje připojení pro aplikační vrstvu.

### <a name="step-1-set-up-active-directory"></a>Krok 1: Nastavení služby Active Directory

Nastavení služby Active Directory v lokalitě sekundární obnovení pro SQL Server, aby běžel bez problémů.

* **Malé organizace**– s malý počet aplikací a jeden řadič domény pro místní lokalitu, pokud chcete převzít služby při selhání celý web, doporučujeme použít replikace Site Recovery k replikaci do sekundární řadič domény datové centrum, nebo do Azure.
* **Střední a velké organizace**– Pokud máte velký počet aplikací, doménové struktury služby Active Directory a chcete po obnovení od aplikace nebo zatížení, doporučujeme nastavit další řadič domény do sekundárního datacentra nebo v Azure. Pokud používáte skupiny dostupnosti Always On pro obnovení vzdálené lokality, doporučujeme že nastavit jiný dalšího řadiče domény v sekundární lokalitě nebo v Azure, pro obnovenou instanci systému SQL Server.

Pokyny v tomto článku předpokládají, že řadič domény je k dispozici v sekundárním umístění. [Přečtěte si další](site-recovery-active-directory.md) o ochraně služby Active Directory pomocí služby Site Recovery.

### <a name="step-2-ensure-connectivity-with-other-application-tiers-and-web-tier"></a>Krok 2: Zajistěte připojení s ostatními úrovně aplikace a webové vrstvy

Ujistěte se, že po vytvoření a spuštění je na úrovni databáze v cílové oblasti Azure, máte připojení k aplikaci a webovou vrstvu. Přijmout nezbytná opatření se předem k ověření připojení pomocí testovacího převzetí služeb při selhání.

Vysvětlení, jak můžete navrhnout aplikace důležité informace o připojení s několik příkladů:
* [Návrh aplikace pro zotavení po havárii cloudu](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Strategie zotavení po havárii elastických fondů](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-integrate-with-always-on-active-geo-replication-or-auto-failover-groups-for-application-failover"></a>Krok 3: Integrace s Always On, aktivní geografické replikace nebo -automatické převzetí služeb při selhání skupiny převzetí služeb při selhání aplikace

Technologiemi BCDR Always On, aktivní geografické replikace a Automatická – převzetí služeb při selhání skupiny mají sekundárních replik SQL serveru běžícího v cílové oblasti Azure. Prvním krokem pro vaše aplikace převzetí služeb při selhání je proto nastavit tuto repliku jako primární (za předpokladu, že už máte v sekundární řadič domény). Tento krok nemusí být nutné v případě, že zadáte automaticky při selhání. Až po dokončení převzetí služeb při selhání databáze, měli byste převzetí služeb při selhání vaší úrovně web nebo aplikaci.

> [!NOTE] 
> Pokud jste nastavili ochranu počítače SQL pomocí Azure Site Recovery, stačí vytvořit skupinu obnovení těchto počítačů a přidejte jejich převzetí služeb při selhání v plánu obnovení.

[Vytvořit plán obnovení](site-recovery-create-recovery-plans.md) aplikace a virtuální počítače webové vrstvy. Postupujte podle níže uvedený postup přidání převzetí služeb při selhání databáze vrstvy:

1. Import skriptů do účtu Azure Automation. Tato položka obsahuje skripty pro převzetí služeb při selhání skupiny dostupnosti SQL v [virtuální počítač Resource Manageru](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) a [klasický virtuální počítač](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Přidáte Azure Site Recovery-SQL-FailoverAG jako akce před první skupiny plánu obnovení.

1. Postupujte podle pokynů k dispozici ve skriptu pro vytvoření proměnné automation k poskytnutí názvu skupiny dostupnosti.

### <a name="step-4-conduct-a-test-failover"></a>Krok 4: Chování testovací převzetí služeb při selhání

Některé technologiemi BCDR, jako je SQL Always On nativně nepodporují testovací převzetí služeb při selhání. Proto doporučujeme následující postup **pouze při integraci s tyto technologie**:

1. Nastavit [Azure Backup](../backup/backup-azure-arm-vms.md) na virtuálním počítači, který je hostitelem repliky skupiny dostupnosti v Azure.

1. Před aktivací převzetí služeb při selhání testů v plánu obnovení, obnovení virtuálního počítače ze zálohy pořízené v předchozím kroku.

    ![Obnovení z Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Vynucení kvora](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) ve virtuálním počítači obnovení ze zálohy.

1. Aktualizujte IP adresu naslouchacího procesu na IP adresy k dispozici v testovací síti převzetí služeb při selhání.

    ![Aktualizovat naslouchací proces IP](./media/site-recovery-sql/update-listener-ip.png)

1. Přeneste online naslouchacího procesu.

    ![Naslouchací proces převedení do Online režimu](./media/site-recovery-sql/bring-listener-online.png)

1. Vytvoření load balanceru úrovně s jednou z IP vytvořené v rámci front-endový fond IP odpovídající každé naslouchacího procesu skupiny dostupnosti a virtuální počítač SQL do back-endový fond.

     ![Vytvoření Load balanceru úrovně – IP front-endový fond](./media/site-recovery-sql/create-load-balancer1.png)

    ![Vytvoření Load balanceru úrovně – back-endový fond](./media/site-recovery-sql/create-load-balancer2.png)

1. Přidáte převzetí služeb při selhání aplikační vrstvy, následovaný webové vrstvy v tomto plánu obnovení ve skupinách následné obnovení. 
1. Proveďte testovací převzetí služeb při selhání plánu obnovení otestovat začátku do konce převzetí služeb při selhání aplikace.

## <a name="steps-to-do-a-failover"></a>Postup proveďte převzetí služeb při selhání

Po přidání skriptu v plánu obnovení v kroku 3 a ověří tímto způsobem testovací převzetí služeb při selhání s specializované přístup v kroku 4, můžete provést převzetí služeb při selhání plánu obnovení vytvořený v kroku 3.

Všimněte si, že postup převzetí služeb při selhání aplikace a webové vrstvy musí být stejná v testovací převzetí služeb při selhání a plány obnovení pro převzetí služeb při selhání.

## <a name="how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>Jak chránit klastr systému SQL Server (standard edition nebo SQL Server 2008 R2)

U clusteru se systémem SQL Server Standard edition nebo SQL Server 2008 R2 doporučujeme že použít replikace Site Recovery pro ochranu serveru SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure do Azure a místní do Azure

Site Recovery neposkytuje hostovaného clusteru podporu při replikaci do oblasti Azure. SQL Server také neposkytuje řešení zotavení po havárii s nízkými náklady pro edici Standard. V tomto scénáři doporučujeme ochraně clusteru SQL serveru do samostatného systému SQL Server v primární lokalitě a obnovit v sekundární.

1. Nakonfigurujte další samostatné instance serveru SQL Server v primární oblasti Azure nebo v místní lokalitě.
1. Konfigurace instance, která má sloužit jako zrcadlení pro databáze, které chcete chránit. Konfigurace zrcadlení v režimu vysoké zabezpečení.
1. Konfigurace obnovení lokality v primární lokalitě ([Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](site-recovery-hyper-v-site-to-azure.md) nebo [virtuální počítače VMware a fyzických serverů)](site-recovery-vmware-to-azure-classic.md).
1. Replikace nové instanci SQL serveru do sekundární lokality pomocí replikace Site Recovery. Protože jde o kopii zrcadlení vysokou bezpečnost, ji budou synchronizovány s primární clusteru, ale se budou replikovat pomocí Site Recovery replikaci.


![Cluster Standard](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Důležité informace o navrácení služeb po obnovení

Navrácení služeb po obnovení po neplánovaném převzetí služeb při selhání pro SQL Server Standard clustery, vyžaduje zálohování SQL serveru a obnovení z instance zrcadlení pro původní cluster, reestablishment zrcadlení.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-does-sql-get-licensed-when-protected-with-azure-site-recovery"></a>Jak SQL získat licenci při ochraně pomocí Azure Site Recovery?
Na replikaci Azure Site Recovery pro SQL Server se vztahuje výhoda Disaster Recovery programu Software Assurance, a to pro všechny scénáře Azure Site Recovery (zotavení po havárii z místního prostředí do Azure nebo zotavení po havárii IaaS napříč oblastmi Azure). [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-azure-site-recovery-support-my-sql-version"></a>Azure Site Recovery bude podporovat Moje verze SQL?
Azure Site Recovery je nezávislý na aplikaci. Proto se dá chránit jakékoli verzi systému SQL server, který je nasazen na podporovaný operační systém Azure Site Recovery. [Další informace](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>Další postup
* [Další informace](site-recovery-components.md) o architektuře Site Recovery.
* Pro servery SQL v Azure, další informace o [řešení s vysokou dostupností](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) pro obnovení do sekundární oblasti Azure.
* Pro službu SQL Database v Azure, další informace o [kontinuita podnikových procesů](../sql-database/sql-database-business-continuity.md) a [vysoké dostupnosti](../sql-database/sql-database-high-availability.md) možnosti obnovení v sekundární oblasti Azure.
* Pro počítače se SQL server on-premises [Další](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) o možnostech vysoké dostupnosti pro obnovení ve službě Azure Virtual Machines.
