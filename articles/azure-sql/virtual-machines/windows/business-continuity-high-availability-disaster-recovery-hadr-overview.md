---
title: Vysoká dostupnost, zotavení po havárii, Kontinuita podnikových aplikací
description: Seznamte se s možnostmi vysoké dostupnosti, zotavení po havárii (HADR) a možností provozní kontinuity, které jsou dostupné pro SQL Server na virtuálních počítačích Azure, jako jsou například skupiny dostupnosti Always On, instance clusteru s podporou převzetí služeb při selhání, zrcadlení databáze, přenos protokolů a zálohování & obnovení do Azure Storage.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: 8459ab364fc0af15dd1a1b0035e4ce27d192f7a9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91293454"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Provozní kontinuita a HADR pro SQL Server v Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Provozní kontinuita znamená pokračování v podnikání v případě havárie, plánování obnovení a zajištění vysoké dostupnosti dat. SQL Server v Azure Virtual Machines můžou snížit náklady na řešení databáze s vysokou dostupností a zotavení po havárii (HADR). 

Většina řešení SQL Server HADR se podporuje na virtuálních počítačích (VM), a to jak pro Azure, tak i v hybridních řešeních. V řešení jenom Azure běží celý systém HADR v Azure. V případě hybridní konfigurace se část řešení spouští v Azure a druhá část se spouští místně ve vaší organizaci. Flexibilita prostředí Azure vám umožňuje přesunout se do Azure částečně nebo kompletně a splnit požadavky na rozpočet a HADR vašich databázových systémů SQL Server.

Tento článek porovnává a naproti tomu nabízí řešení pro provozní kontinuitu, která jsou k dispozici pro SQL Server na virtuálních počítačích Azure. 

## <a name="overview"></a>Přehled

Je to na vás, abyste zajistili, že váš databázový systém má HADR možnosti, které smlouva o úrovni služeb (SLA) vyžaduje. Skutečnost, že Azure poskytuje mechanismy vysoké dostupnosti, jako je třeba služba pro cloudové služby a detekce selhání pro virtuální počítače, samo o sobě nezaručuje, že můžete splnit smlouvu SLA. I když tyto mechanismy chrání vysokou dostupnost virtuálního počítače, nechrání dostupnost SQL Server běžícího uvnitř virtuálního počítače. 

V případě, že je virtuální počítač online a v pořádku, je možné, aby se instance SQL Server nezdařila. I mechanismus vysoké dostupnosti, který poskytuje Azure, umožňuje výpadkům virtuálních počítačů kvůli událostem, jako je obnovení při selhání softwaru nebo hardwaru a upgrady operačního systému.

Geograficky redundantní úložiště (GRS) v Azure je implementováno s funkcí nazvanou geografická replikace. GRS nemusí být vhodné řešení pro zotavení po havárii pro vaše databáze. Vzhledem k tomu, že geografická replikace odesílá data asynchronně, může dojít k výpadku nedávné aktualizace při havárii. Další informace o omezení geografické replikace najdete v části [Podpora geografické replikace](#geo-replication-support) .

## <a name="deployment-architectures"></a>Architektury nasazení
Azure podporuje tyto technologie SQL Server pro zajištění kontinuity podnikových aplikací:

* [Skupiny dostupnosti Always On](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Vždy v instancích clusteru s podporou převzetí služeb při selhání (FCIs)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [Přesouvání protokolu](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [SQL Server zálohování a obnovení pomocí Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [Zrcadlení databáze](/sql/database-engine/database-mirroring/database-mirroring-sql-server) – zastaralé v SQL Server 2016

Technologie můžete kombinovat pro implementaci SQL Server řešení, které má možnosti zotavení po havárii s vysokou dostupností. V závislosti na používané technologii může hybridní nasazení vyžadovat tunelové připojení VPN s virtuální sítí Azure. V následujících částech jsou uvedeny některé příklady architektur nasazení.

## <a name="azure-only-high-availability-solutions"></a>Jenom Azure: řešení s vysokou dostupností

Řešení s vysokou dostupností můžete používat pro SQL Server na úrovni databáze se skupinami dostupnosti Always On. Řešení s vysokou dostupností můžete také vytvořit na úrovni instance s instancemi clusteru s podporou převzetí služeb při selhání. Pro dodatečnou ochranu můžete vytvořit redundanci na obou úrovních vytvořením skupin dostupnosti na instancích clusteru s podporou převzetí služeb při selhání. 

| Technologie | Ukázkové architektury |
| --- | --- |
| **Skupiny dostupnosti** |Repliky dostupnosti běžící na virtuálních počítačích Azure ve stejné oblasti poskytují vysokou dostupnost. Musíte nakonfigurovat virtuální počítač řadiče domény, protože Clustering s podporou převzetí služeb při selhání systému Windows vyžaduje doménu služby Active Directory.<br/><br/> Pro zajištění vyšší redundance a dostupnosti můžete virtuální počítače Azure nasadit v různých [zónách dostupnosti](../../../availability-zones/az-overview.md) , jak je popsáno v [přehledu skupiny dostupnosti](availability-group-overview.md). Pokud jsou virtuální počítače s SQL Server ve skupině dostupnosti nasazené v zónách dostupnosti, pak použijte [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md) pro naslouchací proces, jak je popsáno v článcích Azure [SQL VM CLI](availability-group-az-cli-configure.md) a [šablony rychlý Start pro Azure](availability-group-quickstart-template-configure.md) .<br/> ![Skupiny dostupnosti](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Další informace najdete v tématu [Konfigurace skupin dostupnosti v Azure (GUI)](availability-group-azure-marketplace-template-configure.md). |
| **Instance clusteru převzetí služeb při selhání** |Instance clusteru s podporou převzetí služeb při selhání se podporují na SQL Server virtuálních počítačích. Vzhledem k tomu, že funkce FCI vyžaduje sdílené úložiště, pět řešení bude fungovat s SQL Server na virtuálních počítačích Azure: <br/><br/> – Používání [sdílených disků Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) pro Windows Server 2019. Sdílené spravované disky představují produkt Azure, který umožňuje připojení spravovaného disku k několika virtuálním počítačům současně. Virtuální počítače v clusteru můžou číst nebo zapisovat na připojený disk na základě rezervace zvolené clusterovou aplikací prostřednictvím trvalých rezervací SCSI (SCSI PR). SCSI PR je standardní řešení úložiště používané aplikacemi, které běží v místní síti SAN (Storage Area Network). Povolení SCSI PR na spravovaném disku umožňuje migrovat tyto aplikace do Azure tak, jak jsou. <br/><br/>– Pomocí [prostory úložiště s přímým přístupem \( s2d \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) můžete poskytovat softwarovou virtuální síť SAN pro Windows Server 2016 a novější.<br/><br/>– Použití [prémiové sdílené složky](failover-cluster-instance-premium-file-share-manually-configure.md) pro Windows Server 2012 a novější Soubory úrovně Premium jsou zálohované ve formátu SSD, mají konzistentně nízkou latenci a jsou plně podporované pro použití s FCI.<br/><br/>– Používání úložiště podporovaného partnerským řešením pro clusteringu. Konkrétní příklad, který používá s DataKeeper, najdete v blogu věnovaném [clusteringu s podporou převzetí služeb při selhání a s Datakeeping](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>– Použití sdíleného blokového úložiště pro vzdálený cíl iSCSI prostřednictvím Azure ExpressRoute. Například NetApp Private Storage (NPS) zveřejňuje cíl iSCSI prostřednictvím ExpressRoute s Equinix do virtuálních počítačů Azure.<br/><br/>Pro řešení sdíleného úložiště a replikace dat od partnerů Microsoftu se obraťte na dodavatele s případnými problémy souvisejícími s přístupem k datům při převzetí služeb při selhání.<br/><br/>||

## <a name="azure-only-disaster-recovery-solutions"></a>Jenom Azure: řešení zotavení po havárii
Můžete mít řešení zotavení po havárii pro vaše SQL Server databáze v Azure pomocí skupin dostupnosti, zrcadlení databáze nebo zálohování a obnovení s objekty blob úložiště.

| Technologie | Ukázkové architektury |
| --- | --- |
| **Skupiny dostupnosti** |Repliky dostupnosti spuštěné napříč několika datacentry ve virtuálních počítačích Azure pro zotavení po havárii. Toto řešení pro různé oblasti pomáhá chránit před úplným výpadkem sítě. <br/> ![Skupiny dostupnosti](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>V rámci oblasti by všechny repliky měly být v rámci stejné cloudové služby a stejné virtuální sítě. Vzhledem k tomu, že každá oblast bude mít samostatnou virtuální síť, tato řešení vyžadují připojení typu síť-síť. Další informace najdete v tématu [Konfigurace připojení typu síť-síť pomocí Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Podrobné pokyny najdete v tématu [Konfigurace skupiny dostupnosti Always On SQL Server v různých oblastech Azure](availability-group-manually-configure-multiple-regions.md).|
| **Zrcadlení databáze** |Objekt zabezpečení a zrcadlení a servery běžící v různých datových centrech pro zotavení po havárii. Je nutné je nasadit pomocí certifikátů serveru. SQL Server zrcadlení databáze se na virtuálním počítači Azure SQL Server 2008 nebo SQL Server 2008 R2 nepodporuje. <br/>![Zrcadlení databáze](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Zálohování a obnovení pomocí Azure Blob Storage** |Provozní databáze zálohované přímo do úložiště objektů BLOB v jiném datovém centru pro zotavení po havárii.<br/>![Zálohování a obnovení](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Další informace najdete v tématu [zálohování a obnovení pro SQL Server na virtuálních počítačích Azure](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikace a převzetí služeb při selhání SQL Server do Azure s využitím Azure Site Recovery** |Produkční SQL Server instance v jednom datovém centru Azure se replikují přímo do Azure Storage v jiném datovém centru Azure pro zotavení po havárii.<br/>![Replikace pomocí Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Další informace najdete v tématu [ochrana SQL Server pomocí SQL Server zotavení po havárii a Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybridní IT: řešení pro zotavení po havárii
Řešení zotavení po havárii můžete mít pro vaše SQL Server databáze v hybridním IT prostředí pomocí skupin dostupnosti, zrcadlení databáze, přesouvání protokolů a zálohování a obnovení s využitím služby Azure Blob Storage.

| Technologie | Ukázkové architektury |
| --- | --- |
| **Skupiny dostupnosti** |Některé repliky dostupnosti spuštěné ve virtuálních počítačích Azure a dalších replikách, které jsou místně spuštěné, pro zotavení po havárii mezi lokalitami. Produkční web může být místní nebo v datovém centru Azure.<br/>![Skupiny dostupnosti](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Vzhledem k tomu, že všechny repliky dostupnosti musí být ve stejném clusteru s podporou převzetí služeb při selhání, musí cluster zahrnovat obě sítě (cluster s podporou převzetí služeb při selhání s více Tato konfigurace vyžaduje připojení VPN mezi Azure a místní sítí.<br/><br/>Pro úspěšné zotavení po havárii databází byste měli taky na lokalitu pro zotavení po havárii nainstalovat repliku řadiče domény.|
| **Zrcadlení databáze** |Jeden partner spuštěný ve virtuálním počítači Azure a druhý spuštěný v místním prostředí pro zotavení po havárii mezi lokalitami pomocí certifikátů serveru. Partneři nemusí být ve stejné doméně služby Active Directory a není nutné žádné připojení k síti VPN.<br/>![Zrcadlení databáze](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Další scénář zrcadlení databáze zahrnuje jeden partnerský server spuštěný ve virtuálním počítači Azure a druhý místně běžící ve stejné doméně služby Active Directory pro zotavení po havárii mezi lokalitami. Vyžaduje se [připojení VPN mezi virtuální sítí Azure a místní sítí](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) .<br/><br/>Pro úspěšné zotavení po havárii databází byste měli taky na lokalitu pro zotavení po havárii nainstalovat repliku řadiče domény. SQL Server zrcadlení databáze se na virtuálním počítači Azure SQL Server 2008 nebo SQL Server 2008 R2 nepodporuje. |
| **Přesouvání protokolu** |Jeden server, který běží na virtuálním počítači Azure a druhý spuštěný v místním prostředí pro zotavení po havárii mezi lokalitami. Přesouvání protokolu závisí na sdílení souborů ve Windows, takže je potřeba připojení VPN mezi virtuální sítí Azure a místní sítí.<br/>![Přesouvání protokolu](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>Pro úspěšné zotavení po havárii databází byste měli taky na lokalitu pro zotavení po havárii nainstalovat repliku řadiče domény. |
| **Zálohování a obnovení pomocí Azure Blob Storage** |Místní provozní databáze zálohované přímo do Azure Blob Storage pro zotavení po havárii.<br/>![Zálohování a obnovení](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Další informace najdete v tématu [zálohování a obnovení pro SQL Server v Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikace a převzetí služeb při selhání SQL Server do Azure s využitím Azure Site Recovery** |Místní výrobní SQL Server instance se replikují přímo do Azure Storage pro zotavení po havárii.<br/>![Replikace pomocí Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Další informace najdete v tématu [ochrana SQL Server pomocí SQL Server zotavení po havárii a Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Bezplatná replika DR v Azure

Pokud máte [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), můžete implementovat plány hybridního zotavení po havárii s SQL Server bez dalších nákladů na licencování pro instanci pasivního zotavení po havárii.

Na následujícím obrázku instalační program používá SQL Server běžící na virtuálním počítači Azure, který používá 12 jader jako repliku zotavení po havárii pro místní SQL Server nasazení, které využívá 12 jader. V minulosti byste potřebovali pro místní nasazení a nasazení Azure Virtual Machines licenci na 12 jader SQL Server. Nové zvýhodnění nabízí výhody pasivních replik pro spouštění na virtuálním počítači Azure. Teď budete muset licencovat jenom 12 jader SQL Server běžících v místním prostředí, pokud jsou splněné kritéria zotavení po havárii pro pasivní repliku v Azure Virtual Machines.

![Volná replika pro zotavení po havárii v Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/free-dr-replica-azure.png)

Další informace najdete v [licenčních podmínkách k produktu](https://www.microsoft.com/licensing/product-licensing/products). 

Pokud chcete tuto výhodu povolit, otevřete [prostředek virtuálního počítače SQL Server](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). V části **Nastavení**vyberte **Konfigurovat** a potom v části **SQL Server licence**zvolte možnost **zotavení po havárii** . Zaškrtnutím políčka potvrďte, že se tento virtuální počítač SQL Server použije jako pasivní replika, a pak vyberte **použít** a uložte nastavení. 

![Konfigurace repliky zotavení po havárii v Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Důležité informace pro SQL Server HADR v Azure
Virtuální počítače Azure, úložiště a sítě mají různé provozní charakteristiky než místní, nevirtualizovaná IT infrastruktura. Úspěšná implementace řešení HADR SQL Server v Azure vyžaduje, abyste porozuměli těmto rozdílům a navrhli své řešení, aby je bylo možné přizpůsobit.

### <a name="high-availability-nodes-in-an-availability-set"></a>Uzly s vysokou dostupností ve skupině dostupnosti
Skupiny dostupnosti v Azure umožňují umístit uzly s vysokou dostupností do samostatných domén selhání a aktualizačních domén. Platforma Azure přiřadí aktualizační doménu a doménu selhání každému virtuálnímu počítači v sadě dostupnosti. Tato konfigurace v rámci datového centra zajišťuje, že během plánované nebo neplánované události údržby je k dispozici alespoň jeden virtuální počítač, který splňuje smlouvu SLA Azure 99,95%. 

Chcete-li nakonfigurovat nastavení s vysokou dostupností, umístěte všechny účastnící SQL Server virtuální počítače do stejné skupiny dostupnosti, aby nedošlo ke ztrátě aplikace nebo dat během události údržby. Ve stejné skupině dostupnosti se můžou zúčastnit jenom uzly ve stejné cloudové službě. Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](../../../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Uzly s vysokou dostupností v zóně dostupnosti
Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každá zóna se skládá z jednoho nebo více datových center vybavených nezávislým napájením, chlazením a sítí. Fyzické oddělení zón dostupnosti v rámci oblasti pomáhá chránit aplikace a data před selháními datových center tím, že zajišťuje, že je k dispozici aspoň jeden virtuální počítač a že splňuje smlouvu SLA Azure o 99,99 procent. 

Pokud chcete nakonfigurovat vysokou dostupnost, umístěte mezi zóny dostupnosti v oblasti zahrnuté SQL Server virtuální počítače. Mezi zónami dostupnosti se účtují další poplatky za přenosy mezi sítěmi. Další informace najdete v tématu [zóny dostupnosti](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Chování clusteru s podporou převzetí služeb při selhání v sítích Azure
Služba DHCP, která není kompatibilní se specifikací RFC v Azure, může způsobit selhání vytvoření určitých konfigurací clusteru s podporou převzetí služeb při selhání. K této chybě dochází, protože název sítě clusteru má přiřazenou duplicitní IP adresu, jako je například stejná IP adresa jako jeden z uzlů clusteru. Jedná se o problém, pokud používáte skupiny dostupnosti, které závisí na funkci clusteru s podporou převzetí služeb při selhání systému Windows.

Vezměte v úvahu scénář, kdy se vytvoří cluster se dvěma uzly a přejde do režimu online:

1. Cluster se dostane do online režimu a pak Uzel1 vyžádá dynamicky přiřazenou IP adresu pro název sítě s clustery.
2. Služba DHCP neposkytuje žádnou IP adresu jinou než NODE1's vlastní IP adresu, protože služba DHCP rozpozná, že požadavek pochází z Uzel1 sám.
3. Systém Windows zjistí, že je do Uzel1 a do síťového názvu clusteru s podporou převzetí služeb při selhání přiřazená duplicitní adresa, a výchozí skupinu clusteru se nepovede přepnout do režimu online.
4. Výchozí skupina clusteru se přesune do UZEL2. UZEL2 považuje IP adresu NODE1's za IP adresu clusteru a přepne výchozí skupinu clusteru do online režimu.
5. Když se UZEL2 pokusí navázat spojení s Uzel1, pakety směrované na Uzel1 nikdy nezanechají UZEL2, protože přeloží IP adresu NODE1's sám na sebe. UZEL2 nemůže navázat připojení s Uzel1 a pak ztratí kvorum a vypne cluster.
6. Uzel1 může odesílat pakety do UZEL2, ale UZEL2 nemůže odpovědět. Uzel1 ztratí kvorum a vypne cluster.

Tomuto scénáři se můžete vyhnout tak, že k názvu sítě clusteru přiřadíte nepoužitou statickou IP adresu, aby se název sítě clusteru mohl převést do online režimu. Můžete například použít místní IP adresu propojení, jako je 169.254.1.1. Pro zjednodušení tohoto procesu najdete informace v tématu [Konfigurace clusteru Windows s podporou převzetí služeb při selhání v Azure pro skupiny dostupnosti](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Další informace najdete v tématu [Konfigurace skupin dostupnosti v Azure (GUI)](availability-group-azure-marketplace-template-configure.md).

### <a name="support-for-availability-group-listeners"></a>Podpora pro naslouchací procesy skupiny dostupnosti
Naslouchací procesy skupiny dostupnosti se podporují na virtuálních počítačích Azure se systémem Windows Server 2012 a novějším. Tato podpora je umožněna použitím koncových bodů s vyrovnáváním zatížení povolených na virtuálních počítačích Azure, které jsou uzly skupiny dostupnosti. Aby naslouchací procesy fungovaly pro klientské aplikace běžící v Azure i v místním prostředí, je nutné postupovat podle zvláštních kroků konfigurace.

K dispozici jsou dvě hlavní možnosti Nastavení naslouchacího procesu: externí (veřejná) nebo interní. Externí (veřejný) naslouchací proces používá internetový nástroj pro vyrovnávání zatížení a je přidružen k veřejné virtuální IP adrese, která je přístupná prostřednictvím Internetu. Interní naslouchací proces používá interní nástroj pro vyrovnávání zatížení a podporuje pouze klienty v rámci stejné virtuální sítě. Pro typ nástroje pro vyrovnávání zatížení je nutné povolit přímé vrácení serveru. 

Pokud skupina dostupnosti zahrnuje několik podsítí Azure (například nasazení, které překračuje oblasti Azure), musí připojovací řetězec klienta zahrnovat `MultisubnetFailover=True` . Výsledkem je paralelní pokusy o připojení k replikám v různých podsítích. Pokyny k nastavení naslouchacího procesu najdete v tématu [Konfigurace naslouchacího procesu interního nástroje pro skupiny dostupnosti v Azure](availability-group-listener-powershell-configure.md).


Ke každé replice dostupnosti se můžete připojit samostatně tak, že se připojíte přímo k instanci služby. Vzhledem k tomu, že skupiny dostupnosti jsou zpětně kompatibilní s klienty zrcadlení databáze, můžete se připojit k replikám dostupnosti, jako jsou partneři zrcadlení databáze, pokud jsou repliky nakonfigurované podobně jako zrcadlení databáze:

* Existuje jedna primární replika a jedna sekundární replika.
* Sekundární replika je nakonfigurovaná jako nečitelná (**čitelná sekundární** sada možností nastavená na **ne**).

Tady je příklad připojovacího řetězce klienta, který odpovídá konfiguraci zrcadlení databáze jako v ADO.NET nebo SQL Server Native Client:

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

Další informace o připojení klientů najdete v těchto tématech:

* [Použití klíčových slov připojovacího řetězce s SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Připojení klientů k relaci zrcadlení databáze (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Připojování k naslouchacího procesu skupiny dostupnosti v hybridním IT](https://docs.microsoft.com/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [Naslouchací procesy skupin dostupnosti, připojení klientů a převzetí služeb při selhání aplikací (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Použití připojovacích řetězců zrcadlení databáze se skupinami dostupnosti](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latence sítě v hybridním IT
Nasaďte řešení HADR s předpokladem, že mezi vaší místní sítí a Azure může nastat doba vysoké latence sítě. Při nasazování replik do Azure použijte asynchronní potvrzení místo synchronního potvrzení pro režim synchronizace. Pokud nasazujete servery zrcadlení databáze místně i v Azure, použijte vysoce výkonný režim, nikoli režim vysoké bezpečnosti.

### <a name="geo-replication-support"></a>Podpora geografické replikace
Geografická replikace na discích Azure nepodporuje datový soubor a soubor protokolu stejné databáze, která se má uložit na samostatné disky. GRS replikuje změny na jednotlivých discích nezávisle a asynchronně. Tento mechanismus garantuje pořadí zápisu v rámci jednoho disku na geograficky replikovanou kopii, ale ne napříč geograficky replikovanými kopiemi více disků. Pokud nakonfigurujete databázi tak, aby ukládala svůj datový soubor a soubor protokolu na samostatných discích, obnovené disky po havárii mohou obsahovat aktuálnější kopii datového souboru, než je soubor protokolu, který přeruší protokol zápisu do SQL Server a vlastnosti kyseliny (nedělitelnost, konzistence, izolace a odolnost) transakcí. 

Pokud nemáte možnost zakázat geografickou replikaci v účtu úložiště, ponechte všechny soubory dat a soubory protokolu pro databázi na stejném disku. Pokud musíte použít více než jeden disk z důvodu velikosti databáze, nasaďte jedno z výše uvedených řešení pro zotavení po havárii, aby se zajistila redundance dat.

## <a name="next-steps"></a>Další kroky

Rozhodněte, jestli je [Skupina dostupnosti](availability-group-overview.md) nebo [instance clusteru s podporou převzetí služeb při selhání](failover-cluster-instance-overview.md) nejlepší řešení provozní kontinuity pro vaši firmu. Pak si přečtěte [osvědčené postupy](hadr-cluster-best-practices.md) pro konfiguraci prostředí pro zajištění vysoké dostupnosti a zotavení po havárii. 




