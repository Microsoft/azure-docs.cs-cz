---
title: Nastavení zotavení po havárii pro SQL Server s využitím Azure Site Recovery
description: Tento článek popisuje, jak nastavit zotavení po havárii pro SQL Server pomocí SQL Server a Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 1b02b089fea7e883bdc6c58c7a2845af12b50a37
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011944"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Nastavení zotavení po havárii pro SQL Server

Tento článek popisuje, jak přispět k ochraně SQL Server back-endu aplikace. Provedete to tak, že použijete kombinaci SQL Server technologií a [Azure Site Recovery](site-recovery-overview.md)pro zotavení po havárii (BCDR).

Než začnete, ujistěte se, že rozumíte SQL Server možnosti zotavení po havárii. Mezi tyto možnosti patří:

* Clustering s podporou převzetí služeb při selhání
* Skupiny dostupnosti Always On
* Zrcadlení databáze
* Přesouvání protokolu
* Aktivní geografická replikace
* Skupiny automatického převzetí služeb při selhání

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Kombinování technologií BCDR s Site Recovery

Vaše volba BCDR technologie pro obnovu instancí SQL Server by měla být založená na vaší plánované době obnovení (RTO) a cíli bodu obnovení (RPO), jak je popsáno v následující tabulce. Zkombinujte Site Recovery s operací převzetí služeb při selhání zvolené technologie k orchestraci obnovení celé aplikace.

Typ nasazení | BCDR technologie | Očekával se RTO pro SQL Server. | Očekávalo se RPO pro SQL Server. |
--- | --- | --- | ---
SQL Server virtuálním počítačem infrastruktury Azure jako služba (IaaS) nebo místně.| [Skupina dostupnosti Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | Čas potřebný k provedení sekundární repliky jako primárního. | Vzhledem k tomu, že replikace do sekundární repliky je asynchronní, dojde ke ztrátě dat.
SQL Server na virtuálním počítači Azure IaaS nebo místně.| [Clustering s podporou převzetí služeb při selhání (Always On FCI)](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | Doba, jakou trvalo převzetí služeb při selhání mezi uzly. | Protože Always On FCI používá sdílené úložiště, je stejné zobrazení instance úložiště k dispozici při převzetí služeb při selhání.
SQL Server na virtuálním počítači Azure IaaS nebo místně.| [Zrcadlení databáze (režim vysokého výkonu)](/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | Čas potřebný k vynucení služby, který jako server jako teplého pohotovostního serveru používá zrcadlený server. | Replikace je asynchronní. Zrcadlení databáze může být trochu zpožděné za hlavní databází. Prodleva je obvykle malá. Může se ale stát, že v případě vysoké zátěže dojde k velkému zatížení systému objektu zabezpečení nebo zrcadlového serveru.<br/><br/>Přesouvání protokolu může být doplňkem k zrcadlení databáze. Je to příznivá alternativa k zrcadlení asynchronní databáze.
SQL as Platform as a Service (PaaS) v Azure.<br/><br/>Tento typ nasazení zahrnuje samostatné databáze a elastické fondy. | Aktivní geografická replikace | 30 sekund po aktivaci převzetí služeb při selhání.<br/><br/>Při aktivaci převzetí služeb při selhání pro jednu ze sekundárních databází se všechny ostatní sekundární repliky automaticky propojí s novým primárním objektem. | PLÁNOVANÝ bod obnovení je 5 sekund.<br/><br/>Aktivní geografická replikace používá technologii SQL Server Always On. Asynchronně replikuje potvrzené transakce v primární databázi do sekundární databáze pomocí izolace snímků.<br/><br/>Sekundárním datům je zaručeno, že nikdy nebude mít částečné transakce.
SQL jako PaaS nakonfigurovaný s aktivní geografickou replikací v Azure<br/><br/>Tento typ nasazení zahrnuje spravované instance, elastické fondy a samostatné databáze. | Skupiny automatického převzetí služeb při selhání | RTO jednu hodinu. | PLÁNOVANÝ bod obnovení je 5 sekund.<br/><br/>Skupiny s automatickým převzetím služeb při selhání poskytují sémantiku skupiny nad aktivní geografickou replikací. Ale používá se stejný mechanismus asynchronní replikace.
SQL Server na virtuálním počítači Azure IaaS nebo místně.| Replikace s Azure Site Recovery | RTO je obvykle méně než 15 minut. Pokud se chcete dozvědět víc, přečtěte si [smlouvu SLA pro RTO, kterou poskytuje Site Recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Jedna hodina konzistence aplikace a pět minut pro konzistenci selhání. Pokud hledáte nižší cíl bodu obnovení, použijte další BCDR technologie.

> [!NOTE]
> Několik důležitých doporučení, když pomáháte chránit úlohy SQL pomocí Site Recovery:
> * Site Recovery je nezávislá aplikace. Site Recovery může přispět k ochraně jakékoli verze SQL Server nasazené v podporovaném operačním systému. Další informace najdete v tématu [matice podpory pro obnovení](vmware-physical-azure-support-matrix.md#replicated-machines) replikovaných počítačů.
> * Můžete se rozhodnout použít Site Recovery pro jakékoli nasazení v Azure, Hyper-V, VMware nebo fyzické infrastruktuře. Postupujte podle pokynů na konci tohoto článku, [které vám pomůžou ochránit cluster SQL Server](#how-to-help-protect-a-sql-server-cluster) s Site Recovery.
> * Zajistěte, aby byla míra změny dat zjištěná na počítači v rámci [omezení Site Recovery](vmware-physical-azure-support-matrix.md#churn-limits). Rychlost změny se měří v bajtech zápisu za sekundu. V případě počítačů s Windows můžete tuto rychlost změny zobrazit výběrem karty **výkon** ve Správci úloh. Sledujte rychlost zápisu pro každý disk.
> * Site Recovery podporuje replikaci instancí clusteru s podporou převzetí služeb při selhání na Prostory úložiště s přímým přístupem. Další informace najdete v tématu [Povolení replikace prostory úložiště s přímým přístupem](azure-to-azure-how-to-enable-replication-s2d-vms.md).
> 
> Při migraci úlohy SQL do Azure se doporučuje použít [pravidla výkonu pro SQL Server v azure Virtual Machines](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).

## <a name="disaster-recovery-of-an-application"></a>Zotavení po havárii aplikace

Site Recovery slouží k orchestraci testovacího převzetí služeb při selhání a při převzetí služeb při selhání celé aplikace s využitím plánů obnovení.

Existují některé předpoklady pro zajištění, že váš plán obnovení je plně přizpůsoben podle vašich potřeb. Jakékoli nasazení SQL Server obvykle vyžaduje nasazení služby Active Directory. Vyžaduje také připojení pro vaši aplikační vrstvu.

### <a name="step-1-set-up-active-directory"></a>Krok 1: nastavení služby Active Directory

Nastavte službu Active Directory v sekundární lokalitě pro obnovení, aby SQL Server běžela správně.

* **Small Enterprise**: máte k dispozici malý počet aplikací a jeden řadič domény pro místní lokalitu. Pokud chcete převzít služby při selhání celé lokality, použijte Site Recovery replikaci. Tato služba replikuje řadič domény do sekundárního datového centra nebo do Azure.
* **Střední až velká společnost**: možná budete muset nastavit další řadiče domény.
  - Pokud máte velký počet aplikací, máte doménovou strukturu služby Active Directory a chcete převzít služby při selhání aplikací nebo úloh, nastavte další řadič domény v sekundárním datovém centru nebo v Azure.
  -  Pokud používáte skupiny dostupnosti Always On k obnovení do vzdálené lokality, nastavte další řadič domény v sekundární lokalitě nebo v Azure. Tento řadič domény se používá pro obnovenou instanci SQL Server.

V pokynech v tomto článku se předpokládá, že je řadič domény dostupný v sekundárním umístění. Další informace najdete v tématu Postupy, [které vám pomohou chránit službu Active Directory pomocí Site Recovery](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Krok 2: zajištění připojení k ostatním vrstvám

Jakmile je databázová vrstva spuštěná v cílové oblasti Azure, ujistěte se, že máte připojení s aplikačními a webovými vrstvami. Abyste mohli ověřit připojení pomocí testovacího převzetí služeb při selhání, proveďte předem potřebné kroky.

Pokud chcete pochopit, jak můžete navrhovat aplikace s ohledem na možnosti připojení, přečtěte si tyto příklady:

* [Návrh aplikace pro zotavení po havárii v cloudu](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [Strategie zotavení po havárii elastického fondu](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Krok 3: spolupracuje se skupinami Always On, Active Geo-Replication a s automatickým převzetím služeb při selhání

BCDR technologie vždy aktivní geografické replikace a skupiny automatického převzetí služeb při selhání mají sekundární repliky SQL Server spuštěné v cílové oblasti Azure. Prvním krokem při převzetí služeb při selhání aplikace je zadání této repliky jako primárního. Tento krok předpokládá, že už máte řadič domény v sekundárním zařízení. Pokud se rozhodnete provést automatické převzetí služeb při selhání, nemusí být krok nezbytný. Převzetí služeb při selhání webové a aplikační vrstvy až po dokončení převzetí služeb při selhání databáze.

> [!NOTE]
> Pokud jste pomohli chránit počítače s SQL pomocí Site Recovery, stačí vytvořit skupinu obnovení těchto počítačů a přidat jejich převzetí služeb při selhání do plánu obnovení.

[Vytvořte plán obnovení](site-recovery-create-recovery-plans.md) s virtuálními počítači aplikační a webové vrstvy. Následující kroky ukazují, jak přidat převzetí služeb při selhání databázové vrstvy:

1. Naimportujte skripty pro převzetí služeb při selhání skupinou dostupnosti SQL na [správce prostředkům virtuálním počítači](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) i v [klasickém virtuálním počítači](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Naimportujte skripty do svého účtu Azure Automation.

    [![Obrázek loga "nasadit do Azure"](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Přidejte skript ASR-SQL-FailoverAG jako předběžnou akci první skupiny plánu obnovení.

1. Podle pokynů, které jsou k dispozici ve skriptu, vytvořte proměnnou automatizace. Tato proměnná poskytuje název skupin dostupnosti.

### <a name="step-4-conduct-a-test-failover"></a>Krok 4: provedení testovacího převzetí služeb při selhání

Některé technologie BCDR, například SQL Always On, nativně nepodporují testovací převzetí služeb při selhání. Následující přístup doporučujeme *jenom při použití takových technologií*.

1. Nastavte [Azure Backup](../backup/backup-azure-vms-first-look-arm.md) na virtuálním počítači, který je hostitelem repliky skupiny dostupnosti v Azure.

1. Před spuštěním testovacího převzetí služeb při selhání v plánu obnovení obnovte virtuální počítač ze zálohy provedené v předchozím kroku.

    ![Snímek obrazovky zobrazující okno pro obnovení konfigurace z Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Vynutit kvorum](/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) v virtuálním počítači, který se obnovil ze zálohy.

1. Aktualizujte IP adresu naslouchacího procesu tak, aby byl adresou dostupnou v síti testovacího převzetí služeb při selhání.

    ![Snímek obrazovky okna pravidla a dialogového okna vlastností IP adresy](./media/site-recovery-sql/update-listener-ip.png)

1. Přepněte naslouchací proces do režimu online.

    ![Snímek obrazovky s popisem okna Content_AG zobrazení názvů a stavů serveru](./media/site-recovery-sql/bring-listener-online.png)

1. Ujistěte se, že nástroj pro vyrovnávání zatížení v síti s podporou převzetí služeb při selhání má jednu IP adresu z fondu front-end IP adres, která odpovídá všem naslouchací proces skupiny dostupnosti, a s SQL Server virtuálním počítačem ve fondu back-endu.

     ![Snímek obrazovky okna s názvem SQL-AlwaysOn-9,1-front-endové IP fond](./media/site-recovery-sql/create-load-balancer1.png)

    ![Snímek obrazovky okna s názvem SQL-AlwaysOn-9,1-back-endu IP fond](./media/site-recovery-sql/create-load-balancer2.png)

1. V pozdějších skupinách obnovení přidejte převzetí služeb při selhání vaší aplikační vrstvy, za kterou následuje vaše webová vrstva, pro tento plán obnovení.

1. Proveďte test převzetí služeb při selhání plánu obnovení a otestujte tak kompletní převzetí služeb při selhání vaší aplikace.

## <a name="steps-to-do-a-failover"></a>Postup při převzetí služeb při selhání

Po přidání skriptu v kroku 3 a jeho ověření v kroku 4 můžete provést převzetí služeb při selhání plánu obnovení vytvořeného v kroku 3.

Kroky pro převzetí služeb při selhání pro aplikace a webové vrstvy by měly být stejné v rámci testovacího převzetí služeb při selhání i v plánech obnovení.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Jak lépe chránit cluster SQL Server

Pro cluster se systémem SQL Server Standard Edition nebo SQL Server 2008 R2 doporučujeme použít Site Recovery replikaci k ochraně SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Z Azure do Azure a z místního prostředí do Azure

Site Recovery neposkytuje podporu clusteru hosta při replikaci do oblasti Azure. Edice SQL Server Standard také neposkytuje řešení pro zotavení po havárii s nízkými náklady. V tomto scénáři doporučujeme chránit cluster SQL Server pro samostatnou instanci SQL Server v primárním umístění a obnovit ji v sekundárním umístění.

1. Nakonfigurujte další samostatnou instanci SQL Server v primární oblasti Azure nebo v místní lokalitě.

1. Nakonfigurujte instanci, která bude sloužit jako zrcadlení pro databáze, které chcete chránit. Nakonfigurujte zrcadlení v režimu vysoké bezpečnosti.

1. Nakonfigurujte Site Recovery v primární lokalitě pro virtuální počítače [Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](./hyper-v-azure-tutorial.md)nebo [VMware a fyzické servery](./vmware-azure-tutorial.md).

1. K replikaci nové instance SQL Server do sekundární lokality použijte Site Recovery replikace. Vzhledem k tomu, že se jedná o vysoce bezpečnou zrcadlovou kopii, bude synchronizována s primárním clusterem, ale bude replikována pomocí replikace Site Recovery.

   ![Obrázek standardního clusteru, který zobrazuje vztah a tok mezi primární lokalitou, Site Recovery a Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Požadavky na navrácení služeb po obnovení

U clusterů SQL Server Standard po obnovení po neplánovaném převzetí služeb při selhání vyžaduje SQL Server zálohování a obnovení. Tato operace se provádí z instance zrcadlení pro původní cluster, který má znovu nastavovat zrcadlení.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Jak SQL Server získat licenci při použití s Site Recovery?

Site Recovery replikace pro SQL Server je zahrnutá v části výhoda pro zotavení po havárii Software Assurance. Tato disponibilita se vztahuje na všechny Site Recovery scénáře: zotavení po havárii z místního prostředí do Azure a zotavení po havárii Azure IaaS mezi oblastmi. Další informace najdete v tématu [Azure Site Recovery ceny](https://azure.microsoft.com/pricing/details/site-recovery/) .

### <a name="will-site-recovery-support-my-sql-server-version"></a>Bude Site Recovery podporovat SQL Server verzi?

Site Recovery je nezávislá aplikace. Site Recovery může přispět k ochraně jakékoli verze SQL Server nasazené v podporovaném operačním systému. Další informace najdete v tématu [matice podpory pro obnovení](vmware-physical-azure-support-matrix.md#replicated-machines) replikovaných počítačů.

## <a name="next-steps"></a>Další kroky

* Další informace o [architektuře Site Recovery](./azure-to-azure-architecture.md).
* Pokud SQL Server v Azure, přečtěte si další informace o [řešeních vysoké dostupnosti](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions) pro obnovení v sekundární oblasti Azure.
* Pokud SQL Database, přečtěte si další informace o [provozní kontinuitě](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) a možnostech [vysoké dostupnosti](../azure-sql/database/high-availability-sla.md) pro obnovení v sekundární oblasti Azure.
* Další informace o [možnostech vysoké dostupnosti](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions) pro obnovení v Azure Virtual Machines najdete v SQL Server počítačů v místním prostředí.