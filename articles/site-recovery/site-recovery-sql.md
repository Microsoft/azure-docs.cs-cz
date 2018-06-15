---
title: Aplikace s SQL serverem a Azure Site Recovery replikovat | Microsoft Docs
description: Tento článek popisuje, jak k replikaci systému SQL Server pomocí Azure Site Recovery pro funkce systému SQL Server po havárii.
services: site-recovery
documentationcenter: ''
author: prateek9us
manager: gauravd
editor: ''
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: pratshar
ms.openlocfilehash: 7afa05b53186ceac13bef3294c7a139f77193110
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896227"
---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Ochrana systému SQL Server pomocí zotavení po havárii serveru SQL a Azure Site Recovery

Tento článek popisuje, jak chránit systému SQL Server back-end aplikace pomocí kombinace kontinuity podnikových procesů serveru SQL Server a po havárii (BCDR) obnovení technologií, a [Azure Site Recovery](site-recovery-overview.md).

Než začnete, ujistěte se, že rozumíte možnosti obnovení po havárii systému SQL Server, včetně clustering převzetí služeb při selhání skupiny dostupnosti Always On, zrcadlení databáze a přesouvání protokolu.


## <a name="sql-server-deployments"></a>Nasazení systému SQL Server

Řadu úloh použít jako základ systém SQL Server a lze ji integrovat s aplikacemi, například SharePoint, Dynamics a SAP, implementovat datové služby.  SQL Server se dá nasadit v mnoha různými způsoby:

* **Samostatný Server SQL**: SQL Server a všechny databáze jsou hostované na jednom počítači (fyzický nebo virtuální). Když Virtualizovat, clustering hostitele se používá pro místní vysokou dostupnost. Vysoká dostupnost úrovni hosta nebyla implementována.
* **Instance systému SQL Server Failover Clustering (vždy na FCI)**: dva nebo víc uzlů systémem SQL Server instance s sdílené disky jsou nakonfigurované v clusteru s podporou převzetí služeb při selhání systému Windows. Pokud uzel je vypnutý, clusteru můžete převzetí služeb při selhání systému SQL Server do jiné instance. Tato instalace se obvykle používá k implementaci vysoké dostupnosti v primární lokalitě. Toto nasazení nebude chránit proti selhání nebo výpadek ve vrstvě sdílené úložiště. Sdílený disk může být implementovaná pomocí iSCSI, fiber channel nebo sdílené vhdx.
* **SQL skupin dostupnosti Always On**: dva nebo víc uzlů se nastavují v sdílené nic cluster s databází systému SQL Server, které jsou nakonfigurované ve skupině dostupnosti s synchronní replikace a automatické převzetí služeb při selhání.

 Tento článek využívá následující nativní SQL po havárii obnovení technologie pro obnovení databází do vzdálené lokality:

* SQL skupin dostupnosti Always On, zajistit pro zotavení po havárii pro SQL Server 2012 nebo 2014 Enterprise Edition.
* SQL zrcadlení databáze v režimu vysoké zabezpečení, pro SQL Server Standard edition (všechny verze), nebo pro SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Podpora pro obnovení lokality

### <a name="supported-scenarios"></a>Podporované scénáře
Site Recovery může chránit SQL Server, jak je shrnuto v tabulce.

**Scénář** | **Sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Ano | Ano
**VMware** | Ano | Ano
**Fyzický server** | Ano | Ano
**Azure**|Není k dispozici| Ano

### <a name="supported-sql-server-versions"></a>Podporované verze systému SQL Server
Pro podporované scénáře jsou podporovány tyto verze systému SQL Server:

* SQL Server 2016 Enterprise a Standard
* SQL Server 2014 Enterprise a Standard
* SQL Server 2012 Enterprise a Standard
* SQL Server 2008 R2 Enterprise a Standard

### <a name="supported-sql-server-integration"></a>Podporované integrace systému SQL Server

Obnovení lokality lze integrovat s nativní technologiemi BCDR serveru SQL shrnuto v tabulce, k zajištění řešení obnovení po havárii.

**Funkce** | **Podrobnosti** | **SQL Server** |
--- | --- | ---
**Skupiny dostupnosti Always On** | Více samostatných instancí systému SQL Server spustit v clusteru s podporou převzetí služeb při selhání, který má více uzly.<br/><br/>Databáze je možné seskupit do skupin převzetí služeb při selhání, které je možné zkopírovat (zrcadlení) na instance systému SQL Server tak, že je potřeba žádné sdílené úložiště.<br/><br/>Poskytuje zotavení po havárii mezi primární lokalitou a jeden nebo více sekundárních lokalit. Dva uzly lze nastavit v sdílenou nic cluster s databází serveru SQL Server nakonfigurován ve skupině dostupnosti s synchronní replikace a automatické převzetí služeb při selhání. | SQL Server 2016, SQL Server 2014 a SQL Server 2012 Enterprise edition
**Převzetí služeb clusteringu (vždy na FCI)** | SQL Server využívá Windows převzetí služeb při selhání clusteringu pro vysokou dostupnost úloh, místní systém SQL Server.<br/><br/>Instance systému SQL Server s sdílené disky uzly konfigurované v clusteru s podporou převzetí služeb při selhání. Pokud instance je mimo provoz clusteru převezme jiný.<br/><br/>Clusteru nepodporuje ochranu proti výpadkům ve sdíleném úložišti nebo selhání. Sdílený disk může být implementováno s rozhraní iSCSI, fiber channel, nebo sdílené soubory Vhdx. | SQL Server Enterprise Edition<br/><br/>SQL Server Standard edition (omezeným na pouze dva uzly)
**Databáze zrcadlení (vysokou bezpečnost režim)** | Chrání jeden sekundární kopie pro jednu databázi. K dispozici v obou vysokou bezpečnost (synchronní) a vysoký výkon (asynchronní) replikaci režimy. Nevyžaduje clusteru s podporou převzetí služeb při selhání. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise všechny edice
**Samostatný systém SQL Server** | SQL Server a databáze jsou hostované na jednom serveru (fyzické nebo virtuální). Clustering hostitele se používá pro vysokou dostupnost, pokud je virtuální server. Žádné úrovni hosta vysokou dostupnost. | Standard nebo Enterprise edition

## <a name="deployment-recommendations"></a>Doporučení pro nasazení

Tato tabulka shrnuje Naše doporučení pro integraci technologiemi BCDR SQL serveru pomocí Site Recovery.

| **Verze** | **Edice** | **Nasazení** | **Místní pro místní** | **Místní do Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 nebo 2012 |Enterprise |Instance clusteru převzetí služeb při selhání |Skupiny dostupnosti Always On |Skupiny dostupnosti Always On |
|| Enterprise |Always On skupin dostupnosti pro zajištění vysoké dostupnosti |Skupiny dostupnosti Always On |Skupiny dostupnosti Always On | |
|| Standard |Instance clusteru převzetí služeb při selhání (FCI) |Replikace obnovení lokality s místní zrcadlení |Replikace obnovení lokality s místní zrcadlení | |
|| Standard nebo Enterprise |Standalone |Replikace obnovení lokality |Replikace obnovení lokality | |
| SQL Server 2008 R2 nebo 2008 |Standard nebo Enterprise |Instance clusteru převzetí služeb při selhání (FCI) |Replikace obnovení lokality s místní zrcadlení |Replikace obnovení lokality s místní zrcadlení |
|| Standard nebo Enterprise |Standalone |Replikace obnovení lokality |Replikace obnovení lokality | |
| SQL Server (všechny verze) |Standard nebo Enterprise |Instance clusteru převzetí služeb při selhání - DTC aplikace |Replikace obnovení lokality |Nepodporuje se |

## <a name="deployment-prerequisites"></a>Požadavky nasazení

* Místní nasazení systému SQL Server, spuštěna podporovaná verze systému SQL Server. Obvykle je také nutné služby Active Directory pro SQL server.
* Požadavky pro scénář, kterou chcete nasadit. Další informace o požadavcích na podporu pro [replikaci do Azure](site-recovery-support-matrix-to-azure.md) a [místní](site-recovery-support-matrix.md), a [požadavky nasazení](site-recovery-prereq.md).
* Chcete-li nastavit obnovení v Azure, spusťte [posouzení připravenosti na virtuální počítač Azure](http://www.microsoft.com/download/details.aspx?id=40898) nástroj na virtuální počítače systému SQL Server, abyste měli jistotu, jsou kompatibilní s Azure a Site Recovery.

## <a name="set-up-active-directory"></a>Nastavení služby Active Directory

Nastavení služby Active Directory v obnovení sekundární lokality, systému SQL Server správně spouštět.

* **Malý podnik**– s malý počet aplikací a jeden řadič domény pro místní lokalitu, pokud chcete převzít celou síť, doporučujeme používat Site Recovery replikaci replikace do sekundární řadič domény Datacenter, nebo do Azure.
* **Středních a velkých podniků**– Pokud máte velký počet aplikací, doménové struktury služby Active Directory a chcete selhání převezme aplikace nebo zatížení, doporučujeme nastavit další řadič domény v sekundárního datacentra, nebo v Azure. Pokud používáte skupiny dostupnosti Always On provést obnovení na vzdálený server, doporučujeme že nastavit jiný další řadič domény v sekundární lokalitě nebo v Azure, použijte pro obnovenou instanci systému SQL Server.

Podle pokynů v tomto článku předpokládá, že je řadič domény k dispozici v sekundárním umístění. [Další informace](site-recovery-active-directory.md) o ochraně Active Directory pomocí Site Recovery.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integraci se službou SQL serveru Always On pro replikaci do Azure.

Zde je, co musíte udělat:

1. Skripty importovat do účtu Azure Automation. Tato položka obsahuje skripty pro převzetí služeb při selhání skupiny dostupnosti SQL v [správce prostředků virtuálního počítače](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) a [klasické virtuální počítač](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Přidání automatické obnovení systému-SQL-FailoverAG jako akce před první skupiny plánu obnovení.

1. Postupujte podle pokynů k dispozici ve skriptu pro vytvoření Proměnná automatizace zadat název skupiny dostupnosti.

### <a name="steps-to-do-a-test-failover"></a>Kroky, jak provést testovací převzetí služeb

SQL Always On nenabízí nativní podporu převzetí služeb při selhání. Proto doporučujeme následující:

1. Nastavit [Azure Backup](../backup/backup-azure-arm-vms.md) na virtuálním počítači, který je hostitelem repliky skupiny dostupnosti v Azure.

1. Před spuštěním testu převzetí služeb při selhání plánu obnovení, obnovení virtuálního počítače ze zálohy pořízené v předchozím kroku.

    ![Obnovit ze zálohy Azure ](./media/site-recovery-sql/restore-from-backup.png)

1. [Vynucení kvora](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) ve virtuálním počítači obnovena ze zálohy.

1. Aktualizujte IP naslouchacího procesu na IP adresy k dispozici v testovací síti převzetí služeb při selhání.

    ![Aktualizovat naslouchací proces IP](./media/site-recovery-sql/update-listener-ip.png)

1. Přepněte online naslouchací proces.

    ![Naslouchací proces převedení do online režimu](./media/site-recovery-sql/bring-listener-online.png)

1. Vytvořte nástroj pro vyrovnávání zatížení s jednou z IP vytvořil v rámci fondu IP front-endu odpovídající každé naslouchací proces skupiny dostupnosti a virtuální počítač SQL přidán do fondu back-end.

     ![Vytvořit nástroj pro vyrovnávání zatížení - fondu IP front-endu ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Vytvořit nástroj pro vyrovnávání zatížení - fond back-end ](./media/site-recovery-sql/create-load-balancer2.png)

1. Proveďte testovací převzetí služeb v plánu obnovení.

### <a name="steps-to-do-a-failover"></a>Kroky, jak provést převzetí služeb při selhání

Po přidání skript v plánu obnovení a ověřit plán obnovení pomocí tohoto postupu převzetí služeb při selhání, můžete provést převzetí služeb při selhání plánu obnovení.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integraci se službou SQL serveru Always On pro replikaci do sekundární místní lokalitu

Pokud SQL Server používá skupin dostupnosti pro vysokou dostupnost (nebo FCI), doporučujeme v lokalitě pro obnovení také použití skupin dostupnosti. Všimněte si, že to platí pro aplikace, které nepoužívají distribuované transakce.

1. [Konfigurovat databáze](https://msdn.microsoft.com/library/hh213078.aspx) do skupiny dostupnosti.
1. Vytvoření virtuální sítě v sekundární lokalitě.
1. Nastavte připojení site-to-site VPN mezi virtuální sítě a v primární lokalitě.
1. Vytvoření virtuálního počítače v lokalitě pro obnovení a na něj nainstalovat SQL Server.
1. Rozšiřte existující skupiny dostupnosti Always On nový virtuální počítač SQL Server. Konfiguraci této instance systému SQL Server jako asynchronní repliky kopie.
1. Vytvořit naslouchací proces skupiny dostupnosti nebo aktualizovat existující naslouchací proces zahrnout asynchronní repliky virtuálního počítače.
1. Ujistěte se, že aplikace farmy se nastavuje pomocí naslouchací proces. Pokud je instalační program díky název databázového serveru, aktualizujte ho na použití naslouchací proces, takže je nebudete muset překonfigurovat po převzetí služeb při selhání.

Pro aplikace, které používají distribuované transakce, doporučujeme nasadit Site Recovery s [VMware nebo fyzický server site-to-site replikace](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Důležité informace o plánu obnovení
1. Tento ukázkový skript přidáte do knihovny nástroje VMM na primárních a sekundárních lokalit.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Když vytvoříte plán obnovení pro aplikace, přidejte před akci krok 1. skupina skriptování, která volá skript, který chcete převzít služby skupiny dostupnosti.

## <a name="protect-a-standalone-sql-server"></a>Chránit samostatný systém SQL Server

V tomto scénáři doporučujeme použít replikace Site Recovery pro ochranu počítače systému SQL Server. Přesný postup závisí, zda je SQL Server virtuálním počítači nebo fyzickém serveru a jestli chcete replikovat do Azure nebo sekundární místní lokalitu. Další informace o [scénáře obnovení lokality](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Ochraně clusteru SQL serveru (standard edition nebo Windows Server 2008 R2)

U clusteru se systémem SQL Server Standard edition nebo SQL Server 2008 R2 doporučujeme že používat Site Recovery replikaci k ochraně SQL serveru.

### <a name="on-premises-to-on-premises"></a>Z lokálního prostředí do lokálního prostředí

* Pokud aplikace používá distribuované transakce doporučujeme nasadíte [Site Recovery s replikací sítě SAN](site-recovery-vmm-san.md) pro prostředí Hyper-V, nebo [VMware nebo fyzický server VMware](site-recovery-vmware-to-vmware.md) pro prostředí VMware.
* Pro aplikace bez DTC použijte výše uvedený přístup k obnově clusteru jako samostatný server s využitím místní vysokou bezpečnost zrcadlení databáze.

### <a name="on-premises-to-azure"></a>Místním nasazením a Azure

Site Recovery neposkytuje hostovaného clusteru podporu při replikaci do Azure. SQL Server také neposkytuje řešení pro zotavení po havárii nízkonákladové pro Standard edition. V tomto scénáři doporučujeme chránit místní cluster serveru SQL Server na samostatný systém SQL Server a obnovit v Azure.

1. Nakonfigurujte další samostatné instance systému SQL Server na webu na místě.
1. Nakonfigurujte v této instanci, která bude sloužit jako zrcadlení pro databáze, které chcete chránit. Konfigurace zrcadlení v režimu vysokou bezpečnost.
1. Pro konfiguraci Site Recovery na webu na místě ([technologie Hyper-V](site-recovery-hyper-v-site-to-azure.md) nebo [virtuální počítače VMware nebo fyzické servery)](site-recovery-vmware-to-azure-classic.md).
1. Použijte Site Recovery replikaci do Azure replikovat nové instance systému SQL Server. Vzhledem k tomu, že je zrcadlení kopie vysokou bezpečnost, bude synchronizován s primární clusteru, ale bude možné replikovat do Azure pomocí Site Recovery replikace.


![Cluster Standard](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Důležité informace o navrácení služeb po obnovení

Navrácení služeb po obnovení po neplánovaném převzetí služeb při selhání pro SQL Server Standard clusterů, vyžaduje zálohování systému SQL server a obnovení ze instanci zrcadlového původní cluster, se reestablishment zrcadlení.

## <a name="next-steps"></a>Další postup
[Další informace](site-recovery-components.md) o architektuře Site Recovery.
