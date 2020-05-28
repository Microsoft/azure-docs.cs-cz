---
title: Vysoká dostupnost a zotavení po havárii pro SQL Server | Microsoft Docs
description: Diskuzi o různých typech HADR strategií pro SQL Server běžící v Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 36812c05ca08d48bfd6dca283886e9a30fe10e9e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048620"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Microsoft Azure virtuální počítače s SQL Server můžou snížit náklady na databázové řešení vysoké dostupnosti a zotavení po havárii (HADR). Většina řešení SQL Server HADR se ve virtuálních počítačích Azure podporuje jak jako jenom Azure, tak i jako hybridní řešení. V řešení jenom Azure běží celý systém HADR v Azure. V případě hybridní konfigurace se část řešení spouští v Azure a druhá část se spouští místně ve vaší organizaci. Flexibilita prostředí Azure vám umožňuje přesunout se do Azure částečně nebo kompletně a splnit požadavky na rozpočet a HADR vašich databázových systémů SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Porozumění nutnosti řešení HADR
Máte jistotu, že váš databázový systém má HADR možnosti, které smlouva SLA (Service-level agreement) vyžaduje. Skutečnost, že Azure poskytuje mechanismy vysoké dostupnosti, jako je třeba služba pro cloudové služby, a detekce selhání při obnovení Virtual Machines, samo o sobě nezaručuje, že budete vyhovovat požadované smlouvě SLA. Tyto mechanismy chrání vysokou dostupnost virtuálních počítačů, ale ne vysokou dostupnost SQL Server spuštěných v rámci virtuálních počítačů. V případě, že je virtuální počítač online a v pořádku, je možné, aby se instance SQL Server nezdařila. Kromě toho mechanismus vysoké dostupnosti poskytovaný službou Azure umožňuje výpadkům virtuálních počítačů kvůli událostem, jako je například obnovení při selhání softwaru nebo hardwaru a upgrady operačního systému.

Kromě toho geograficky redundantní úložiště (GRS) v Azure, které je implementované funkcí nazvanou geografická replikace, nemusí být pro vaše databáze adekvátní řešení pro zotavení po havárii. Vzhledem k tomu, že geografická replikace odesílá data asynchronně, můžou se v případě havárie ztratit nedávné aktualizace. Další informace týkající se omezení geografické replikace jsou uvedené v části [geografická replikace Nepodporovaná pro data a soubory protokolů v samostatných discích](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>HADR architektury nasazení
Mezi SQL Server technologie, které jsou podporované v Azure, patří:

* [Skupiny dostupnosti Always On](https://technet.microsoft.com/library/hh510230.aspx)
* [Instance clusteru s podporou převzetí služeb při selhání](https://technet.microsoft.com/library/ms189134.aspx)
* [Přesouvání protokolu](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server zálohování a obnovení pomocí služby Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx)
* [Zrcadlení databáze](https://technet.microsoft.com/library/ms189852.aspx) – zastaralé v SQL Server 2016

Je možné kombinovat technologie dohromady a implementovat řešení SQL Server, které má vysokou dostupnost i zotavení po havárii. V závislosti na technologii, kterou používáte, může hybridní nasazení vyžadovat tunelové připojení VPN s virtuální sítí Azure. Níže uvedené části ukazují některé z ukázkových architektur nasazení.

## <a name="azure-only-high-availability-solutions"></a>Jenom Azure: řešení s vysokou dostupností

K dispozici je řešení vysoké dostupnosti pro SQL Server na úrovni databáze se skupinami dostupnosti Always On, která se označuje jako skupiny dostupnosti. Řešení s vysokou dostupností můžete také vytvořit na úrovni instance s instancemi clusteru s podporou převzetí služeb při selhání, které jsou vždy spuštěné. Pro zajištění dalšího redundance můžete vytvořit redundanci na obou úrovních vytvořením skupin dostupnosti na instancích clusteru s podporou převzetí služeb při selhání. 

| Technologie | Ukázkové architektury |
| --- | --- |
| **Skupiny dostupnosti** |Repliky dostupnosti běžící na virtuálních počítačích Azure ve stejné oblasti poskytují vysokou dostupnost.  Musíte nakonfigurovat virtuální počítač řadiče domény, protože Clustering s podporou převzetí služeb při selhání systému Windows vyžaduje doménu služby Active Directory.<br/><br/> Pro zajištění vyšší redundance a dostupnosti můžou být virtuální počítače Azure nasazené v různých [zónách dostupnosti](../../../availability-zones/az-overview.md) , jak je popsáno v [přehledu skupiny dostupnosti](availability-group-overview.md). Pokud jsou virtuální počítače s SQL Server ve skupině dostupnosti nasazené v zónách dostupnosti, pak použijte [standardní nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-standard-overview.md) pro naslouchací proces, jak je popsáno v těchto článcích – [Azure SQL VM CLI CLI](availability-group-az-cli-configure.md)  &  [Azure pro rychlý Start](availability-group-quickstart-template-configure.md).<br/> ![Skupiny dostupnosti](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Další informace najdete v tématu [Konfigurace skupin dostupnosti v Azure (GUI)](availability-group-azure-marketplace-template-configure.md). |
| **Instance clusteru převzetí služeb při selhání** |Instance clusterů s podporou převzetí služeb při selhání (FCI), které vyžadují sdílené úložiště, se dají vytvořit 4 různými způsoby.<br/><br/>1. cluster s podporou převzetí služeb při selhání se dvěma uzly, který běží na virtuálních počítačích Azure s připojeným úložištěm s využitím [Windows serveru 2016 prostory úložiště s přímým přístupem \( s2d \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) pro poskytování softwarové virtuální sítě SAN.<br/><br/> 2. cluster s podporou převzetí služeb při selhání se dvěma uzly, který běží na virtuálních počítačích Azure pomocí služby [Premium File](failover-cluster-instance-premium-file-share-manually-configure.md). Soubory úrovně Premium jsou sdílené složky se konzistentními latencí ve formátu SSD, které jsou plně podporované pro použití s instancí clusteru s podporou převzetí služeb při selhání.<br/><br/>3. cluster s podporou převzetí služeb při selhání se dvěma uzly běžícím na virtuálních počítačích Azure s úložištěm podporovaným řešením clusteringu třetí strany. Konkrétní příklad, který používá s DataKeeper, najdete v tématu [Vysoká dostupnost sdílené složky pomocí clusteringu s podporou převzetí služeb při selhání a softwaru třetích stran s Datakeeping](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>4. cluster s podporou převzetí služeb při selhání se dvěma uzly, který běží na virtuálních počítačích Azure s virtuálním úložištěm vzdáleného cíle iSCSI přes ExpressRoute. Například NetApp Private Storage (NPS) zveřejňuje cíl iSCSI prostřednictvím ExpressRoute s Equinix do virtuálních počítačů Azure.<br/><br/>Pro řešení sdíleného úložiště a replikace dat třetích stran byste se měli obrátit na dodavatele s případnými problémy souvisejícími s přístupem k datům při převzetí služeb při selhání.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Jenom Azure: řešení zotavení po havárii
Můžete mít řešení zotavení po havárii pro databáze SQL Server v Azure pomocí skupin dostupnosti, zrcadlení databáze nebo zálohování a obnovení s využitím objektů BLOB úložiště.

| Technologie | Ukázkové architektury |
| --- | --- |
| **Skupiny dostupnosti** |Repliky dostupnosti spuštěné napříč několika datacentry ve virtuálních počítačích Azure pro zotavení po havárii. Toto řešení pro různé oblasti chrání před úplným výpadkem sítě. <br/> ![Skupiny dostupnosti](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>V rámci oblasti by všechny repliky měly být v rámci stejné cloudové služby a stejné virtuální sítě. Vzhledem k tomu, že každá oblast bude mít samostatnou virtuální síť, tato řešení vyžadují připojení VNet-to-VNet. Další informace najdete v tématu [Konfigurace připojení typu VNet-to-VNet pomocí Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Podrobné pokyny najdete v tématu [Konfigurace skupiny dostupnosti SQL Server v Azure Virtual Machines v různých oblastech](availability-group-manually-configure-multiple-regions.md).|
| **Zrcadlení databáze** |Objekt zabezpečení a zrcadlení a servery běžící v různých datových centrech pro zotavení po havárii. Je nutné nasadit pomocí certifikátů serveru. SQL Server zrcadlení databáze není pro SQL Server 2008 a SQL Server 2008 R2 na virtuálním počítači Azure podporováno. <br/>![Zrcadlení databáze](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Zálohování a obnovení pomocí služby Azure Blob Storage** |Provozní databáze zálohované přímo do úložiště objektů BLOB v jiném datovém centru pro zotavení po havárii.<br/>![Zálohování a obnovení](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Další informace najdete v tématu [zálohování a obnovení pro SQL Server v Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikace a převzetí služeb při selhání SQL Server do Azure s využitím Azure Site Recovery** |Produkční SQL Server jednoho datacentra Azure se replikují přímo do Azure Storage jiného datacentra Azure pro zotavení po havárii.<br/>![Replikace pomocí Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Další informace najdete v tématu [ochrana SQL Server pomocí SQL Server zotavení po havárii a Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybridní IT: řešení pro zotavení po havárii
Můžete mít řešení zotavení po havárii pro vaše SQL Server databáze v hybridním prostředí IT pomocí skupin dostupnosti, zrcadlení databáze, přesouvání protokolů a zálohování a obnovení s využitím služby Azure blog Storage.

| Technologie | Ukázkové architektury |
| --- | --- |
| **Skupiny dostupnosti** |Některé repliky dostupnosti spuštěné ve virtuálních počítačích Azure a dalších replikách, které jsou místně spuštěné, pro zotavení po havárii mezi lokalitami. Produkční web může být místní nebo v datovém centru Azure.<br/>![Skupiny dostupnosti](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Vzhledem k tomu, že všechny repliky dostupnosti musí být ve stejném clusteru s podporou převzetí služeb při selhání, musí cluster zahrnovat obě sítě (cluster s podporou převzetí služeb při selhání s více Tato konfigurace vyžaduje připojení VPN mezi Azure a místní sítí.<br/><br/>Pro úspěšné zotavení po havárii databází byste měli taky na lokalitu pro zotavení po havárii nainstalovat repliku řadiče domény.<br/><br/>K přidání repliky Azure do existující skupiny dostupnosti Always On je možné použít Průvodce přidáním repliky v SSMS. Další informace najdete v tématu Kurz: rozšířím skupinu dostupnosti Always On do Azure. |
| **Zrcadlení databáze** |Jeden partner spuštěný ve virtuálním počítači Azure a druhý místně spuštěný pro zotavení po havárii mezi lokalitami pomocí certifikátů serveru. Partneři nemusí být ve stejné doméně služby Active Directory a není nutné žádné připojení k síti VPN.<br/>![Zrcadlení databáze](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Další scénář zrcadlení databáze zahrnuje jeden partnerský server spuštěný ve virtuálním počítači Azure a druhý místně běžící ve stejné doméně služby Active Directory pro zotavení po havárii mezi lokalitami. Vyžaduje se [připojení VPN mezi virtuální sítí Azure a místní sítí](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) .<br/><br/>Pro úspěšné zotavení po havárii databází byste měli taky na lokalitu pro zotavení po havárii nainstalovat repliku řadiče domény. SQL Server zrcadlení databáze není pro SQL Server 2008 a SQL Server 2008 R2 na virtuálním počítači Azure podporováno. |
| **Přesouvání protokolu** |Jeden server, který běží na virtuálním počítači Azure a druhý spuštěný v místním prostředí pro zotavení po havárii mezi lokalitami. Přesouvání protokolu závisí na sdílení souborů ve Windows, takže je potřeba připojení VPN mezi virtuální sítí Azure a místní sítí.<br/>![Přesouvání protokolu](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>Pro úspěšné zotavení po havárii databází byste měli taky na lokalitu pro zotavení po havárii nainstalovat repliku řadiče domény. |
| **Zálohování a obnovení pomocí služby Azure Blob Storage** |Místní provozní databáze zálohované přímo do Azure Blob Storage pro zotavení po havárii.<br/>![Zálohování a obnovení](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Další informace najdete v tématu [zálohování a obnovení pro SQL Server v Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikace a převzetí služeb při selhání SQL Server do Azure s využitím Azure Site Recovery** |Místní produkční SQL Server replikované přímo do Azure Storage pro zotavení po havárii.<br/>![Replikace pomocí Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Další informace najdete v tématu [ochrana SQL Server pomocí SQL Server zotavení po havárii a Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Bezplatná replika DR v Azure

Pokud máte [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), můžete implementovat plány hybridního zotavení po havárii s SQL Server bez dalších nákladů na licencování pro pasivní instanci zotavení po havárii.

Na následujícím obrázku instalační program používá SQL Server běžící na virtuálním počítači Azure, který využívá 12 jader jako repliku zotavení po havárii pro místní SQL Server nasazení pomocí 12 jader. V minulosti byste potřebovali licenci 12 jader SQL Server pro místní prostředí a nasazení virtuálního počítače Azure. Nové zvýhodnění nabízí výhody pasivních replik, které běží na virtuálním počítači Azure. Nyní byste potřebovali jenom licenci 12 jader SQL Server běžet místně, pokud je splněné kritérium zotavení po havárii pro pasivní repliku na virtuálním počítači Azure.

![Bezplatná replika DR v Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/free-dr-replica-azure.png)

Další informace najdete v [licenčních podmínkách k produktu](https://www.microsoft.com/licensing/product-licensing/products). 

Pokud chcete tuto výhodu povolit, přejděte na [prostředek virtuálního počítače SQL Server](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource), v části nastavení vyberte **Konfigurovat** a v části **SQL Server licence**zvolte možnost **zotavení po havárii** . Zaškrtnutím políčka potvrďte, že se tento virtuální počítač SQL Server použije jako pasivní replika, a pak vyberte **použít** a uložte nastavení. 

![Konfigurace repliky DR v Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Důležité informace pro SQL Server HADR v Azure
Virtuální počítače Azure, úložiště a sítě mají různé provozní charakteristiky než místní, nevirtualizovaná IT infrastruktura. Úspěšná implementace řešení HADR SQL Server v Azure vyžaduje, abyste porozuměli těmto rozdílům a navrhli své řešení, aby je bylo možné přizpůsobit.

### <a name="high-availability-nodes-in-an-availability-set"></a>Uzly s vysokou dostupností ve skupině dostupnosti
Skupiny dostupnosti v Azure umožňují umístit uzly s vysokou dostupností do samostatných domén selhání (doménami selhání) a aktualizačních domén (UDs). Základní platforma Azure každému virtuálnímu počítači ve skupině dostupnosti přiřadí aktualizační doménu a doménu selhání. Tato konfigurace v rámci datového centra zajišťuje, že během plánované nebo neplánované události údržby je k dispozici aspoň jeden virtuální počítač a splňuje 99,95% Azure SLA. Chcete-li nakonfigurovat nastavení vysoké dostupnosti, umístěte všechny zúčastněné Virtual Machines SQL do stejné skupiny dostupnosti, aby nedošlo ke ztrátě aplikace nebo dat během události údržby. Ve stejné skupině dostupnosti se můžou zúčastnit jenom uzly ve stejné cloudové službě. Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](../../../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Uzly s vysokou dostupností v zóně dostupnosti
Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Fyzické oddělení Zóny dostupnosti v rámci oblasti chrání aplikace a data před selháním Datacenter tím, že zajišťuje, že je k dispozici aspoň jeden virtuální počítač a splňuje 99,99% Azure SLA. Pokud chcete nakonfigurovat vysokou dostupnost, umístěte zúčastněný Virtual Machines SQL mezi dostupné Zóny dostupnosti v oblasti. Budou se účtovat další poplatky za přenos dat mezi virtuálními počítači v zóně dostupnosti. Další informace najdete v tématu [zóny dostupnosti](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Chování clusteru s podporou převzetí služeb při selhání v sítích Azure
Služba DHCP nekompatibilní se specifikací RFC v Azure může způsobit selhání vytvoření určitých konfigurací clusteru s podporou převzetí služeb při selhání, protože název sítě clusteru má přiřazenou duplicitní IP adresu, jako je například stejná IP adresa jako jeden z uzlů clusteru. Jedná se o problém při implementaci skupin dostupnosti, které závisí na funkci clusteru s podporou převzetí služeb při selhání systému Windows.

Vezměte v úvahu scénář, kdy se vytvoří cluster se dvěma uzly a přejde do režimu online:

1. Cluster je online a pak Uzel1 vyžádá dynamicky přiřazenou IP adresu pro název sítě s clustery.
2. Služba DHCP nepoužívá jinou IP adresu než vlastní IP adresa NODE1's, protože služba DHCP rozpozná, že požadavek pochází ze samotného Uzel1.
3. Systém Windows zjistí, že se k Uzel1 i k názvu sítě clusteru s podporou převzetí služeb při selhání přiřadí duplicitní adresa a v případě, že výchozí skupinu clusteru nebude možné přejít do režimu online.
4. Výchozí skupina clusteru se přesune do UZEL2, která považuje IP adresu NODE1's za IP adresu clusteru a převede výchozí skupinu clusteru do online režimu.
5. Když se UZEL2 pokusí navázat spojení s Uzel1, pakety směrované na Uzel1 nikdy ponechají UZEL2, protože přeloží IP adresu NODE1's sám na sebe. UZEL2 nemůže navázat připojení s Uzel1 a pak ztratí kvorum a vypne cluster.
6. Mezitím může Uzel1 odesílat pakety do UZEL2, ale UZEL2 nemůže odpovědět. Uzel1 ztratí kvorum a vypne cluster.

K tomuto scénáři se můžete vyhnout přiřazením nepoužívané statické IP adresy, jako je například místní IP adresa propojení, jako je 169.254.1.1, na název sítě clusteru, aby se název sítě clusteru mohl převést do online režimu. Pro zjednodušení tohoto procesu najdete informace v tématu [Konfigurace clusteru Windows s podporou převzetí služeb při selhání v Azure pro skupiny dostupnosti](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Další informace najdete v tématu [Konfigurace skupin dostupnosti v Azure (GUI)](availability-group-azure-marketplace-template-configure.md).

### <a name="availability-group-listener-support"></a>Podpora naslouchacího procesu skupiny dostupnosti
Naslouchací procesy skupiny dostupnosti se podporují na virtuálních počítačích Azure, na kterých běží Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Tato podpora je umožněna použitím koncových bodů s vyrovnáváním zatížení povolených na virtuálních počítačích Azure, které jsou uzly skupiny dostupnosti. Aby naslouchací procesy fungovaly pro klientské aplikace spuštěné v Azure i místně spuštěné v místním prostředí, musíte postupovat podle zvláštních kroků konfigurace.

K dispozici jsou dvě hlavní možnosti Nastavení naslouchacího procesu: externí (veřejná) nebo interní. Externí (veřejný) naslouchací proces používá internetový nástroj pro vyrovnávání zatížení, který je přidružený k veřejné virtuální IP (VIP), která je přístupná přes Internet. Interní naslouchací proces používá interní nástroj pro vyrovnávání zatížení a podporuje jenom klienty v rámci stejné Virtual Network. Pro typ nástroje pro vyrovnávání zatížení je nutné povolit přímé vrácení serveru. 

Pokud skupina dostupnosti zahrnuje několik podsítí Azure (například nasazení, které překračuje oblasti Azure), musí připojovací řetězec klienta zahrnovat "**MultiSubnetFailover = true**". Výsledkem je paralelní pokusy o připojení k replikám v různých podsítích. Pokyny k nastavení naslouchacího procesu najdete v tématu.

* [Konfigurace naslouchacího procesu interního nástroje pro skupiny dostupnosti v Azure](availability-group-listener-powershell-configure.md)
* [Konfigurace externího naslouchacího procesu pro skupiny dostupnosti v Azure](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Ke každé replice dostupnosti se můžete připojit samostatně tak, že se připojíte přímo k instanci služby. Vzhledem k tomu, že skupiny dostupnosti jsou zpětně kompatibilní s klienty zrcadlení databáze, se můžete připojit k replikám dostupnosti, jako jsou partneři zrcadlení databáze, pokud jsou repliky nakonfigurované podobně jako zrcadlení databáze:

* Jedna primární replika a jedna sekundární replika
* Sekundární replika je nakonfigurovaná jako nečitelná (**čitelná sekundární** sada možností nastavená na **ne**).

Příklad připojovacího řetězce klienta, který odpovídá této konfiguraci zrcadlení v databázi pomocí ADO.NET nebo SQL Server Native Client, je následující:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Další informace o připojení klientů najdete v těchto tématech:

* [Použití klíčových slov připojovacího řetězce s SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Připojení klientů k relaci zrcadlení databáze (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Připojování k naslouchacího procesu skupiny dostupnosti v hybridním IT](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Naslouchací procesy skupin dostupnosti, připojení klientů a převzetí služeb při selhání aplikací (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Použití připojovacích řetězců zrcadlení databáze se skupinami dostupnosti](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latence sítě v hybridním IT
Řešení HADR byste měli nasadit s předpokladem, že mohou nastat časová období s vysokou latencí sítě mezi vaší místní sítí a Azure. Při nasazování replik do Azure byste měli použít asynchronní potvrzení místo synchronního potvrzení pro režim synchronizace. Při nasazování serverů zrcadlení databáze v místním prostředí i v Azure použijte vysoce výkonný režim, nikoli režim vysoké bezpečnosti.

### <a name="geo-replication-support"></a>Podpora geografické replikace
Geografická replikace na discích Azure nepodporuje datový soubor a soubor protokolu stejné databáze, která se má uložit na samostatné disky. GRS replikuje změny na jednotlivých discích nezávisle a asynchronně. Tento mechanismus garantuje pořadí zápisu v rámci jednoho disku na geograficky replikovanou kopii, ale ne napříč geograficky replikovanými kopiemi více disků. Pokud nakonfigurujete databázi tak, aby ukládala svůj datový soubor a soubor protokolu na samostatných discích, obnovené disky po havárii mohou obsahovat podrobnější kopii datového souboru, než je soubor protokolu, který přeruší protokol Write-to SQL Server a vlastnosti kyseliny pro transakce. Pokud nemáte možnost zakázat geografickou replikaci v účtu úložiště, měli byste všechny soubory a soubory protokolu pro danou databázi zachovat na stejném disku. Pokud musíte použít více než jeden disk z důvodu velikosti databáze, je nutné nasadit jednu z výše uvedených řešení pro zotavení po havárii, aby se zajistila redundance dat.

## <a name="next-steps"></a>Další kroky
Pokud potřebujete vytvořit virtuální počítač Azure s SQL Server, přečtěte si téma [zřízení virtuálního počítače s SQL Server v Azure](create-sql-vm-portal.md).

Pokud chcete dosáhnout nejlepšího výkonu z SQL Server spuštěného na virtuálním počítači Azure, přečtěte si pokyny v tématu [osvědčené postupy výkonu pro SQL Server v Azure Virtual Machines](performance-guidelines-best-practices.md).

Další témata související se spouštěním SQL Server ve virtuálních počítačích Azure najdete v tématu [SQL Server v Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).

### <a name="other-resources"></a>Další prostředky
* [Instalace nové doménové struktury služby Active Directory v Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Vytvoření clusteru s podporou převzetí služeb při selhání pro skupiny dostupnosti v Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

