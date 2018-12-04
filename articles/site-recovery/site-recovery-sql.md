---
title: Nastavení zotavení po havárii pro SQL Server s SQL serverem a Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak nastavit zotavení po havárii pro SQL Server pomocí SQL serveru a Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: d4be7b9c7774163aed8c0efb3414dbd6a794cf7f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847792"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Nastavení zotavení po havárii pro SQL Server 

Tento článek popisuje, jak chránit SQL Server back-endu aplikace pomocí kombinace kontinuity SQL serveru a technologie (BCDR) pro obnovení po havárii, a [Azure Site Recovery](site-recovery-overview.md).

Než začnete, ujistěte se, že rozumíte možnosti zotavení po havárii serveru SQL Server, včetně clustering převzetí služeb při selhání skupiny dostupnosti Always On, zrcadlení databáze a přesouvání protokolu.


## <a name="sql-server-deployments"></a>Nasazení SQL serveru

Mnoho úloh SQL serveru použít jako základ a dá se integrovat s aplikací, jako jsou SharePoint, Dynamics a SAP, implementovat datové služby.  SQL Server se dá nasadit v několika způsoby:

* **Samostatný SQL Server**: SQL Server a všechny databáze jsou hostované na jednom počítači (fyzický nebo virtuální). Pokud virtualizovaný, hostitele clustering slouží pro místní vysokou dostupnost. Vysoká dostupnost na úrovni hosta není implementována.
* **Převzetí služeb při selhání Clustering instance systému SQL Server (vždy na FCI)**: dva nebo více uzlů se systémem SQL Server instance se sdílenými disky jsou konfigurované v clusteru převzetí služeb při selhání Windows. Pokud uzel je vypnutý, clusteru můžete předat serveru SQL Server do jiné instance. Toto nastavení se obvykle používá pro implementaci vysoké dostupnosti v primární lokalitě. Toto nasazení nebude chránit proti selhání nebo kvůli výpadku ve vrstvě sdíleného úložiště. Sdílený disk je možné implementovat pomocí iSCSI, fiber channel nebo sdílený soubor vhdx.
* **SQL skupin dostupnosti Always On**: dva nebo více uzlů se nastavují v sdílené nic cluster s databází serveru SQL Server nakonfigurován ve skupině dostupnosti s synchronní replikace a automatické převzetí služeb při selhání.

 V tomto článku využívají následující nativní SQL po havárii obnovení technologie pro obnovení databází do vzdálené lokality:

* SQL skupin dostupnosti Always On, zajištění zotavení po havárii pro SQL Server 2012 nebo 2014 Enterprise.
* SQL zrcadlení databáze v režimu vysoké zabezpečení pro SQL Server Standard edition (libovolná verze) nebo SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Podpora Site Recovery

### <a name="supported-scenarios"></a>Podporované scénáře
Site Recovery může chránit SQL Server, jak je uvedené v tabulce.

**Scénář** | **Do sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Ano | Ano
**VMware** | Ano | Ano
**Fyzický server** | Ano | Ano
**Azure**|Není k dispozici| Ano

### <a name="supported-sql-server-versions"></a>Podporované verze systému SQL Server
Tyto verze systému SQL Server jsou podporovány pro podporované scénáře:

* SQL Server 2016 Enterprise a Standard
* SQL Server 2014 Enterprise a Standard
* SQL Server 2012 Enterprise a Standard
* SQL Server 2008 R2 Enterprise a Standard

### <a name="supported-sql-server-integration"></a>Podporované integrace SQL serveru

Site Recovery je možné integrovat s nativní technologiemi BCDR SQL serveru, které jsou uvedené v tabulce, k poskytování řešení zotavení po havárii.

**Funkce** | **Podrobnosti** | **SQL Server** |
--- | --- | ---
**Skupiny dostupnosti Always On** | Více samostatných instancí systému SQL Server běží v clusteru převzetí služeb při selhání, který má více uzlů.<br/><br/>Databáze je možné seskupit do skupiny převzetí služeb při selhání, které je možné zkopírovat (zrcadlení) v instancích systému SQL Server tak, že je potřeba žádné sdílené úložiště.<br/><br/>Poskytuje zotavení po havárii mezi primární lokalitou a jeden nebo více sekundárních lokalit. Dva uzly lze nastavit ve sdílené nic cluster s databází serveru SQL Server nakonfigurován ve skupině dostupnosti s synchronní replikace a automatické převzetí služeb při selhání. | SQL Server 2016, SQL Server 2014 a SQL Server 2012 Enterprise edition
**Převzetí služeb clusteringu (vždy na FCI)** | SQL Server využívá Windows převzetí služeb při selhání clusteringu pro vysokou dostupnost úloh v místním SQL serveru.<br/><br/>Uzly, které běží instance systému SQL Server se sdílenými disky jsou konfigurované v clusteru převzetí služeb při selhání. Pokud instance je mimo provoz clusteru převezme služby při selhání do jiné.<br/><br/>Cluster nebude chránit proti selhání a méně výpadků ve sdíleném úložišti. Je možné implementovat pomocí iSCSI, Fibre channel, sdílený disk, nebo sdílené soubory Vhdx. | Verze SQL Server Enterprise Edition<br/><br/>SQL Server Standard edition (omezeno na pouze dva uzly)
**Zrcadlení (vysokou bezpečnost režim)** | Chrání izolovanou databázi na jednu sekundární kopii. K dispozici v obou vysokou bezpečnost (synchronní) a vysoký výkon (asynchronní) replikace režimy. Nevyžaduje, aby cluster převzetí služeb při selhání. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise všechny edice
**Samostatný SQL Server** | SQL Server a databáze jsou hostované na jeden server (fyzický nebo virtuální). Hostitele clusterů se používá pro zajištění vysoké dostupnosti, pokud je virtuální server. Bez vysoké dostupnosti úrovni hosta. | Edice Enterprise nebo Standard

## <a name="deployment-recommendations"></a>Doporučení pro nasazení

Tato tabulka shrnuje našimi doporučeními k integraci technologiemi BCDR SQL serveru se službou Site Recovery.

| **Verze** | **Edice** | **Nasazení** | **V místním prostředí pro místní prostředí** | **V místním prostředí do Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014, 2016 a 2012 |Enterprise |Instance clusteru převzetí služeb při selhání |Skupiny dostupnosti AlwaysOn |Skupiny dostupnosti AlwaysOn |
|| Enterprise |Skupiny dostupnosti AlwaysOn pro vysokou dostupnost |Skupiny dostupnosti AlwaysOn |Skupiny dostupnosti AlwaysOn | |
|| Standard |Instance clusteru převzetí služeb při selhání (FCI) |Replikace služby Site Recovery s místní zrcadlový svazek |Replikace služby Site Recovery s místní zrcadlový svazek | |
|| Enterprise nebo Standard |Standalone |Replikace služby Site Recovery |Replikace služby Site Recovery | |
| SQL Server 2008 R2 nebo 2008 |Enterprise nebo Standard |Instance clusteru převzetí služeb při selhání (FCI) |Replikace služby Site Recovery s místní zrcadlový svazek |Replikace služby Site Recovery s místní zrcadlový svazek |
|| Enterprise nebo Standard |Standalone |Replikace služby Site Recovery |Replikace služby Site Recovery | |
| SQL Server (libovolná verze) |Enterprise nebo Standard |Instance clusteru převzetí služeb při selhání – DTC aplikace |Replikace služby Site Recovery |Nepodporuje se |

## <a name="deployment-prerequisites"></a>Požadavky nasazení

* V místním nasazení systému SQL Server, spuštěna podporovaná verze systému SQL Server. Obvykle budete také potřebovat služby Active Directory pro SQL server.
* Požadavky na scénář, které chcete nasadit. Další informace o požadavcích na podporu pro [replikaci do Azure](site-recovery-support-matrix-to-azure.md) a [místní](site-recovery-support-matrix.md), a [požadavky na nasazení](site-recovery-prereq.md).

## <a name="set-up-active-directory"></a>Nastavení služby Active Directory

Nastavení služby Active Directory v lokalitě sekundární obnovení pro SQL Server, aby běžel bez problémů.

* **Malé organizace**– s malý počet aplikací a jeden řadič domény pro místní lokalitu, pokud chcete převzít služby při selhání celý web, doporučujeme použít replikace Site Recovery k replikaci do sekundární řadič domény datové centrum, nebo do Azure.
* **Střední a velké organizace**– Pokud máte velký počet aplikací, doménové struktury služby Active Directory a chcete po obnovení od aplikace nebo zatížení, doporučujeme nastavit další řadič domény do sekundárního datacentra nebo v Azure. Pokud používáte skupiny dostupnosti Always On pro obnovení vzdálené lokality, doporučujeme že nastavit jiný dalšího řadiče domény v sekundární lokalitě nebo v Azure, pro obnovenou instanci systému SQL Server.

Pokyny v tomto článku předpokládají, že řadič domény je k dispozici v sekundárním umístění. [Přečtěte si další](site-recovery-active-directory.md) o ochraně služby Active Directory pomocí služby Site Recovery.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integrace s SQL serveru Always On pro replikaci do Azure

Zde je, co je potřeba udělat:

1. Import skriptů do účtu Azure Automation. Tato položka obsahuje skripty pro převzetí služeb při selhání skupiny dostupnosti SQL v [virtuální počítač Resource Manageru](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) a [klasický virtuální počítač](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Přidáte Azure Site Recovery-SQL-FailoverAG jako akce před první skupiny plánu obnovení.

1. Postupujte podle pokynů k dispozici ve skriptu pro vytvoření proměnné automation k poskytnutí názvu skupiny dostupnosti.

### <a name="steps-to-do-a-test-failover"></a>Postup testovací převzetí služeb při selhání

SQL Always On nenabízí nativní podporu převzetí služeb při selhání testu. Proto doporučujeme následující:

1. Nastavit [Azure Backup](../backup/backup-azure-arm-vms.md) na virtuálním počítači, který je hostitelem repliky skupiny dostupnosti v Azure.

1. Před aktivací převzetí služeb při selhání testů v plánu obnovení, obnovení virtuálního počítače ze zálohy pořízené v předchozím kroku.

    ![Obnovení z Azure Backup ](./media/site-recovery-sql/restore-from-backup.png)

1. [Vynucení kvora](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) ve virtuálním počítači obnovení ze zálohy.

1. Aktualizujte IP adresu naslouchacího procesu na IP adresy k dispozici v testovací síti převzetí služeb při selhání.

    ![Aktualizovat naslouchací proces IP](./media/site-recovery-sql/update-listener-ip.png)

1. Přeneste online naslouchacího procesu.

    ![Naslouchací proces převedení do Online režimu](./media/site-recovery-sql/bring-listener-online.png)

1. Vytvoření load balanceru úrovně s jednou z IP vytvořené v rámci front-endový fond IP odpovídající každé naslouchacího procesu skupiny dostupnosti a virtuální počítač SQL do back-endový fond.

     ![Vytvoření Load balanceru úrovně – IP front-endový fond ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Vytvoření Load balanceru úrovně – back-endový fond ](./media/site-recovery-sql/create-load-balancer2.png)

1. Proveďte testovací převzetí služeb při selhání plánu obnovení.

### <a name="steps-to-do-a-failover"></a>Postup proveďte převzetí služeb při selhání

Po přidání skriptu v plánu obnovení a ověřit tímto způsobem testovací převzetí služeb při selhání plánu obnovení, můžete provést převzetí služeb při selhání plánu obnovení.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integrace s SQL serveru Always On pro replikaci do sekundární místní lokality

Pokud SQL Server používá skupiny dostupnosti pro vysokou dostupnost (nebo FCI), doporučujeme použít skupiny dostupnosti na webu pro zotavení. Všimněte si, že to platí pro aplikace, které nepoužívají distribuované transakce.

1. [Konfigurovat databáze](https://msdn.microsoft.com/library/hh213078.aspx) do skupiny dostupnosti.
1. Vytvoření virtuální sítě v sekundární lokalitě.
1. Nastavte připojení VPN typu site-to-site mezi virtuální sítí a primární lokality.
1. Vytvoření virtuálního počítače v lokalitě pro obnovení a nainstalujte SQL Server.
1. Rozšíření stávajících skupin dostupnosti Always On do nového virtuálního počítače SQL serveru. Konfigurace této instanci systému SQL Server jako asynchronní repliky kopie.
1. Vytvořit naslouchací proces skupiny dostupnosti nebo aktualizovat existující naslouchací proces, který chcete zahrnout virtuální počítač repliky asynchronní.
1. Ujistěte se, že aplikace farmy se nastavuje pomocí naslouchací proces. Pokud je instalační program pomocí název databázového serveru, aktualizujte ji, aby používat naslouchací proces, takže není nutné k překonfigurování po převzetí služeb.

Pro aplikace, které používají distribuované transakce, doporučujeme nasadit Site Recovery s [replikace site-to-site server VMware/fyzických prostředků](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Důležité informace o plánu obnovení
1. Tento ukázkový skript přidáte do knihovny VMM na primárních a sekundárních lokalit.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Když vytvoříte plán obnovení pro aplikaci, přidejte akci pre kroku skupiny-1, skripty, které vyvolá skript pro převzetí služeb při selhání skupiny dostupnosti.

## <a name="protect-a-standalone-sql-server"></a>Ochrana samostatný systém SQL Server

V tomto scénáři doporučujeme použít replikace Site Recovery pro ochranu počítače systému SQL Server. Přesný postup závisí, zda je virtuální počítač nebo fyzický server, SQL Server a zda chcete replikovat do Azure nebo sekundární místní lokality. Další informace o [Site Recovery scénáře](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Ochraně clusteru SQL serveru (standard edition a Windows Server 2008 R2)

U clusteru se systémem SQL Server Standard edition nebo SQL Server 2008 R2 doporučujeme že použít replikace Site Recovery pro ochranu serveru SQL Server.

### <a name="on-premises-to-on-premises"></a>Z lokálního prostředí do lokálního prostředí

* Pokud aplikace využívá distribuované transakce doporučujeme nasadíte [Site Recovery pomocí replikace sítě SAN](site-recovery-vmm-san.md) pro prostředí Hyper-V nebo [server VMware/fyzických prostředků do VMware](site-recovery-vmware-to-vmware.md) pro prostředí VMware.
* Pro aplikace bez DTC použijte výše uvedené přístup k obnově clusteru jako samostatný server s využitím místní vysokou bezpečnost zrcadlení databáze.

### <a name="on-premises-to-azure"></a>On-premises do Azure

Site Recovery neposkytuje hostovaného clusteru podporu při replikaci do Azure. SQL Server také neposkytuje řešení zotavení po havárii s nízkými náklady pro edici Standard. V tomto scénáři doporučujeme chránit místní cluster serveru SQL Server do samostatného systému SQL Server a obnovení v Azure.

1. Nakonfigurujte další samostatné instance serveru SQL Server na místní lokalitu.
1. Konfigurace instance, která má sloužit jako zrcadlení pro databáze, které chcete chránit. Konfigurace zrcadlení v režimu vysoké zabezpečení.
1. Konfigurace pro Site Recovery na webu v místním ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) nebo [virtuální počítače VMware a fyzických serverů)](site-recovery-vmware-to-azure-classic.md).
1. Pomocí replikace Site Recovery replikovat nové instanci SQL serveru do Azure. Protože jde o kopii zrcadlení vysokou bezpečnost, ji budou synchronizovány s primární clusteru, ale bude možné replikovat do Azure pomocí Site Recovery replikaci.


![Cluster Standard](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Důležité informace o navrácení služeb po obnovení

Navrácení služeb po obnovení po neplánovaném převzetí služeb při selhání pro SQL Server Standard clustery, vyžaduje zálohování SQL serveru a obnovení z instance zrcadlení pro původní cluster, reestablishment zrcadlení.

## <a name="next-steps"></a>Další postup
[Další informace](site-recovery-components.md) o architektuře Site Recovery.
