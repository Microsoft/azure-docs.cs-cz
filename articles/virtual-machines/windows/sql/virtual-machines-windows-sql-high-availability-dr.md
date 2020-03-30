---
title: Vysoká dostupnost a zotavení po havárii pro SQL Server | Dokumenty společnosti Microsoft
description: Diskuse o různých typech strategií HADR pro SQL Server spuštěných ve virtuálních počítačích Azure.
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
ms.openlocfilehash: f58bb534728660b85f7d16910dde7a37914fd571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249760"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines

Virtuální počítače (VM) Microsoft Azure (VM) s SQL Serverem můžou pomoct snížit náklady na databázové řešení s vysokou dostupností a zotavenípo havárii (HADR). Většina řešení SQL Server HADR je podporovaná ve virtuálních počítačích Azure, a to jak jako azure-jenom, tak jako hybridní řešení. V řešení pouze azure celý systém HADR běží v Azure. V hybridní konfiguraci je část řešení spuštěna v Azure a druhá část běží místně ve vaší organizaci. Flexibilita prostředí Azure umožňuje částečně nebo úplně přejít do Azure, abyste splnili požadavky rozpočtu a HADR databázových systémů SQL Serveru.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Pochopení potřeby řešení HADR
Je na vás, abyste zajistili, že váš databázový systém má funkce HADR, které vyžaduje smlouva o úrovni služeb (SLA). Skutečnost, že Azure poskytuje mechanismy vysoké dostupnosti, jako je například vyléčení služeb pro cloudové služby a zjišťování obnovení selhání pro virtuální počítače, sama o sobě nezaručuje, že můžete splnit požadovanou sla. Tyto mechanismy chrání vysokou dostupnost virtuálních připojení, ale ne vysokou dostupnost SQL Server běží uvnitř virtuálních aplikací. Je možné, že instance serveru SQL Server selže, když je virtuální modul online a v pořádku. Kromě toho i mechanismy vysoké dostupnosti poskytované Azure umožňují prostoje virtuálních počítačů kvůli událostem, jako je obnovení z selhání softwaru nebo hardwaru a upgrady operačního systému.

Kromě toho geograficky redundantní úložiště (GRS) v Azure, který je implementován s funkcí s názvem geografická replikace, nemusí být odpovídající řešení zotavení po havárii pro vaše databáze. Vzhledem k tomu, že geografická replikace odesílá data asynchronně, mohou být nedávné aktualizace ztraceny v případě havárie. Další informace týkající se omezení geografické replikace jsou zahrnuty v části [Geografická replikace není podporována pro datové soubory a soubory protokolu na samostatných discích.](#geo-replication-support)

## <a name="hadr-deployment-architectures"></a>Architektury nasazení HADR
Mezi technologie SQL Serveru HADR, které jsou podporované v Azure, patří:

* [Vždy na skupinách dostupnosti](https://technet.microsoft.com/library/hh510230.aspx)
* [Vždy v instancích clusteru s podporou převzetí služeb při selhání](https://technet.microsoft.com/library/ms189134.aspx)
* [Přesouvání protokolu](https://technet.microsoft.com/library/ms187103.aspx)
* [Zálohování a obnovení serveru SQL Server pomocí služby Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx)
* [Zrcadlení databáze](https://technet.microsoft.com/library/ms189852.aspx) – zastaralé v SQL Serveru 2016

Je možné kombinovat technologie dohromady implementovat řešení SQL Server, který má vysokou dostupnost a možnosti zotavení po havárii. V závislosti na technologii, kterou používáte, hybridní nasazení může vyžadovat tunelovou propojení VPN s virtuální sítí Azure. Následující části ukazují některé z ukázkových architektur nasazení.

## <a name="azure-only-high-availability-solutions"></a>Pouze Azure: Řešení s vysokou dostupností

Můžete mít řešení s vysokou dostupností pro SQL Server na úrovni databáze s vždy na dostupnost skupiny - volal skupiny dostupnosti. Můžete také vytvořit řešení s vysokou dostupností na úrovni instance s instancemi clusteru always on failover - instance clusteru s podporou převzetí služeb při selhání. Pro další redundanci můžete vytvořit redundanci na obou úrovních vytvořením skupin dostupnosti v instancích clusteru s podporou převzetí služeb při selhání. 

| Technologie | Příklad architektury |
| --- | --- |
| **Skupiny dostupnosti** |Repliky dostupnosti spuštěné ve virtuálních počítačích Azure ve stejné oblasti poskytují vysokou dostupnost.  Je třeba nakonfigurovat virtuální řadič domény, protože clustering s podporou převzetí služeb při selhání systému Windows vyžaduje doménu služby Active Directory.<br/><br/> Pro vyšší redundanci a dostupnost virtuálních počítačích Azure lze nasadit v různých [zónách dostupnosti,](../../../availability-zones/az-overview.md) jak je popsáno v [přehledu skupiny dostupnosti](virtual-machines-windows-portal-sql-availability-group-overview.md). Pokud jsou virtuální počítače SQL Serveru ve skupině dostupnosti nasazeny v zónách dostupnosti, použijte [standardní nástroje pro vyrovnávání zatížení](../../../load-balancer/load-balancer-standard-overview.md) pro naslouchací proces, jak je popsáno v těchto článcích – šablony Azure SQL [VM CLI](virtual-machines-windows-sql-availability-group-cli.md) & [Azure Quickstart](virtual-machines-windows-sql-availability-group-quickstart-template.md).<br/> ![Skupiny dostupnosti](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>Další informace najdete [v tématu Konfigurace skupin dostupnosti v Azure (GUI).](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) |
| **Instance clusteru s podporou převzetí služeb při selhání** |Instance clusteru s podporou převzetí služeb při selhání (FCI), které vyžadují sdílené úložiště, lze vytvořit 4 různými způsoby.<br/><br/>1. Cluster s podporou převzetí služeb při selhání se dvěma uzly spuštěný ve virtuálních počítačích Azure s připojeným úložištěm pomocí [windows serveru \(2016 Storage Spaces Direct S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) k poskytování virtuální sítě SAN založené na softwaru.<br/><br/> 2. Cluster s podporou převzetí služeb při selhání se dvěma uzny spuštěný ve virtuálních počítačích Azure pomocí [služby Premium File Share](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md). Sdílené složky Premium jsou sdílené složky s konzistentně nízkou latencí, které jsou plně podporovány pro použití s instancí clusteru s podporou převzetí služeb při selhání.<br/><br/>3. Cluster s podporou převzetí služeb při selhání se dvěma uzny spuštěný ve virtuálních počítačích Azure s úložištěm podporovaným řešením clusteringu třetích stran. Konkrétní příklad, který používá SIOS DataKeeper, naleznete [v tématu Vysoká dostupnost pro sdílenou složku pomocí clusteringu s podporou převzetí služeb při selhání a softwaru třetích stran SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>4. Cluster s podporou převzetí služeb při selhání se dvěma uzly spuštěný ve virtuálních počítačích Azure se vzdáleným úložištěm sdílených bloků iSCSI Target přes ExpressRoute. Například NetApp Private Storage (NPS) zpřístupňuje cíl iSCSI přes ExpressRoute s Equinix do virtuálních počítačů Azure.<br/><br/>V případě řešení sdíleného úložiště a replikace dat od jiných výrobců byste se měli obrátit na dodavatele, pokud se nedostanete k problémům souvisejícím s přístupem k datům při převzetí služeb při selhání.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Jenom Azure: Řešení pro zotavení po havárii
Můžete mít řešení zotavení po havárii pro vaše databáze SQL Server v Azure pomocí skupin dostupnosti, zrcadlení databáze nebo zálohování a obnovení s objekty BLOB úložiště.

| Technologie | Příklad architektury |
| --- | --- |
| **Skupiny dostupnosti** |Repliky dostupnosti spuštěné ve více datových centrech ve virtuálních počítačích Azure pro zotavení po havárii. Toto řešení mezi oblastmi chrání před úplným výpadkem webu. <br/> ![Skupiny dostupnosti](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>V rámci oblasti by měly být všechny repliky v rámci stejné cloudové služby a stejné virtuální sítě. Vzhledem k tomu, že každá oblast bude mít samostatnou virtuální síť, tato řešení vyžadují připojení virtuální sítě k virtuální síti. Další informace najdete [v tématu Konfigurace připojení virtuální sítě k virtuální síti pomocí portálu Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Podrobné pokyny najdete [v tématu Konfigurace skupiny dostupnosti serveru SQL Server na virtuálních počítačích Azure v různých oblastech](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Zrcadlení databáze** |Hlavní a zrcadlové a servery spuštěné v různých datových centrech pro zotavení po havárii. Je nutné nasadit pomocí certifikátů serveru. Zrcadlení databáze SQL Serveru není podporováno pro SQL Server 2008 ani SQL Server 2008 R2 na virtuálním počítači Azure. <br/>![Zrcadlení databáze](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Zálohování a obnovení pomocí služby Azure Blob Storage Service** |Produkční databáze zálohované přímo do úložiště objektů blob v jiném datovém centru pro zotavení po havárii.<br/>![Zálohování a obnovení](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>Další informace najdete v [tématu Zálohování a obnovení pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikace a převzetí služeb při selhání SQL Server do Azure pomocí Azure Site Recovery** |Produkční SQL Server jednoho datového centra Azure replikovaného přímo do Azure Storage v různých datových centerazure pro zotavení po havárii.<br/>![Replikace pomocí Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>Další informace naleznete [v tématu Protect SQL Server using SQL Server disaster recovery and Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybridní IT: Řešení pro zotavení po havárii
Řešení pro zotavení po havárii pro databáze SERVERU SQL Server můžete mít v hybridním it prostředí pomocí skupin dostupnosti, zrcadlení databáze, přesouvání protokolů a zálohování a obnovení pomocí úložiště blogu Azure.

| Technologie | Příklad architektury |
| --- | --- |
| **Skupiny dostupnosti** |Některé repliky dostupnosti spuštěné ve virtuálních počítačích Azure a další repliky spuštěné místně pro zotavení po havárii mezi lokalitami. Produkční lokalita může být místní nebo v datovém centru Azure.<br/>![Skupiny dostupnosti](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Vzhledem k tomu, že všechny repliky dostupnosti musí být ve stejném clusteru s podporou převzetí služeb při selhání, musí cluster prohlepí obě sítě (cluster s podporou převzetí služeb při selhání s více podsítěmi). Tato konfigurace vyžaduje připojení VPN mezi Azure a místní sítí.<br/><br/>Pro úspěšné zotavení po havárii databází byste měli nainstalovat také řadič domény repliky v lokalitě pro zotavení po havárii.<br/><br/>Je možné použít Průvodce přidáním repliky v SSMS k přidání repliky Azure do existující skupiny dostupnosti always on. Další informace najdete v tématu Kurz: Rozšíření skupiny dostupnosti vždy na Azure. |
| **Zrcadlení databáze** |Jeden partner spuštěný ve virtuálním počítači Azure a druhý spuštěný místně pro zotavení po havárii mezi lokalitami pomocí serverových certifikátů. Partneři nemusí být ve stejné doméně služby Active Directory a není vyžadováno žádné připojení vpn.<br/>![Zrcadlení databáze](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>Jiný scénář zrcadlení databáze zahrnuje jednoho partnera spuštěného ve virtuálním počítači Azure a druhého spuštěného místně ve stejné doméně služby Active Directory pro zotavení po havárii mezi lokalitami. Je [vyžadováno připojení VPN mezi virtuální sítí Azure a místní sítí.](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br/><br/>Pro úspěšné zotavení po havárii databází byste měli nainstalovat také řadič domény repliky v lokalitě pro zotavení po havárii. Zrcadlení databáze SQL Serveru není podporováno pro SQL Server 2008 ani SQL Server 2008 R2 na virtuálním počítači Azure. |
| **Přesouvání protokolu** |Jeden server spuštěný ve virtuálním počítači Azure a druhý spuštěný místně pro zotavení po havárii mezi weby. Přesouvání protokolu závisí na sdílení souborů systému Windows, takže je vyžadováno připojení VPN mezi virtuální sítí Azure a místní sítí.<br/>![Přesouvání protokolu](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>Pro úspěšné zotavení po havárii databází byste měli nainstalovat také řadič domény repliky v lokalitě pro zotavení po havárii. |
| **Zálohování a obnovení pomocí služby Azure Blob Storage Service** |Místní produkční databáze zálohované přímo do úložiště objektů blob Azure pro zotavení po havárii.<br/>![Zálohování a obnovení](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>Další informace najdete v [tématu Zálohování a obnovení pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikace a převzetí služeb při selhání SQL Server do Azure pomocí Azure Site Recovery** |Místní produkční SQL Server replikovaný přímo do Azure Storage pro zotavení po havárii.<br/>![Replikace pomocí Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>Další informace naleznete [v tématu Protect SQL Server using SQL Server disaster recovery and Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Bezplatná replika zotavení po havárii v Azure

Pokud máte [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), můžete implementovat hybridní zotavení po havárii (DR) plány s SQL Server bez vzniku dalších licenčních nákladů pro pasivní instance zotavení po Havárii.

Na bitové kopii níže používá nastavení SQL Server spuštěný na virtuálním počítači Azure využívající 12 jader jako repliku zotavení po havárii pro místní nasazení SERVERU SQL Server s využitím 12 jader. V minulosti byste museli licencovat 12 jader SQL Serveru pro místní nasazení a nasazení virtuálního počítače Azure. Nová výhoda nabízí výhody pasivní repliky spuštěné na virtuálním počítači Azure. Teď budete muset licencovat jenom 12 jader SQL Serveru spuštěných místně, pokud jsou splněna kritéria zotavení po havárii pro pasivní repliku ve virtuálním počítači Azure.

![Bezplatná replika zotavení po havárii v Azure](media/virtual-machines-windows-sql-high-availability-dr/free-dr-replica-azure.png)

Další informace naleznete v [licenčních podmínkách produktu](https://www.microsoft.com/licensing/product-licensing/products). 

Chcete-li tuto výhodu povolit, přejděte na [prostředek virtuálního počítače serveru SQL Server](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource), vyberte možnost **Konfigurovat** v části nastavení a pak v části SQL Server License zvolte možnost Zotavení **po havárii** . **SQL Server License** Zaškrtnutím políčka potvrďte, že tento virtuální počítač SQL Server se bude používat jako pasivní replika, a pak vyberte **Použít pro** uložení nastavení. 

![Konfigurace repliky zotavení po havárii v Azure](media/virtual-machines-windows-sql-high-availability-dr/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Důležité důležité informace pro SQL Server HADR v Azure
Virtuální počítače Azure, úložiště a sítě mají jiné provozní charakteristiky než místní, nevirtualizovaná IT infrastruktura. Úspěšná implementace řešení HADR SQL Server v Azure vyžaduje, abyste těmto rozdílům porozuměli a navrhli řešení tak, aby jim vyhovovalo.

### <a name="high-availability-nodes-in-an-availability-set"></a>Uzly s vysokou dostupností v sadě dostupnosti
Skupiny dostupnosti v Azure umožňují umístit uzly s vysokou dostupností do samostatných domén selhání (FD) a aktualizačních domén (UDs). Základní platforma Azure každému virtuálnímu počítači ve skupině dostupnosti přiřadí aktualizační doménu a doménu selhání. Tato konfigurace v rámci datového centra zajišťuje, že během plánované nebo neplánované události údržby je k dispozici alespoň jeden virtuální počítač, který splňuje 99,95% azure sla. Chcete-li nakonfigurovat nastavení vysoké dostupnosti, umístěte všechny zúčastněné virtuální počítače SQL do stejné sady dostupnosti, abyste zabránili ztrátě aplikace nebo dat během události údržby. Do stejné skupiny dostupnosti se mohou účastnit pouze uzly ve stejné cloudové službě. Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Uzly s vysokou dostupností v zóně dostupnosti
Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Fyzické oddělení zón dostupnosti v rámci oblasti chrání aplikace a data před selháním datových center tím, že zajišťuje, že je k dispozici alespoň jeden virtuální počítač a splňuje 99,99% azure sla. Chcete-li nakonfigurovat vysokou dostupnost, umístěte zúčastněné virtuální počítače SQL rozložené na dostupné zóny dostupnosti v oblasti. Budou účtovány další poplatky za přenos dat mezi zónami dostupnosti. Další informace naleznete v tématu [Zóny dostupnosti](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Chování clusteru s podporou převzetí služeb při selhání v azure sítích
Služba DHCP nekompatibilní s rfc v Azure může způsobit selhání vytvoření určitých konfigurací clusteru s podporou převzetí služeb při selhání, protože názvu sítě clusteru je přiřazena duplicitní adresa IP, například stejná adresa IP jako jeden z uzlů clusteru. Jedná se o problém při implementaci skupin dostupnosti, které závisí na funkci clusteru s podporou převzetí služeb při selhání systému Windows.

Zvažte scénář, kdy je vytvořen cluster se dvěma uznami a přepnuto do režimu online:

1. Cluster je online a node1 si pak vyžádá dynamicky přiřazenou adresu IP pro název sítě clusteru.
2. Služba DHCP nezadává jinou IP adresu než vlastní IP adresu NODE1, protože služba DHCP rozpozná, že požadavek pochází od samotného node1.
3. Systém Windows zjistí, že duplicitní adresa je přiřazena společnosti NODE1 i názvu sítě clusteru s podporou převzetí služeb při selhání a výchozí skupina clusteru se nepodaří přejít do režimu online.
4. Výchozí skupina clusteru se přesune na ADRESU NODE2, která považuje adresu IP společnosti NODE1 za adresu IP clusteru a přenese výchozí skupinu clusteru do režimu online.
5. Když se NODE2 pokusí navázat připojení s NODE1, pakety směrované na NODE1 nikdy neopustí NODE2, protože řeší IP adresu NODE1 pro sebe. NODE2 nemůže navázat připojení s NODE1, pak ztratí kvorum a vypne cluster.
6. Do té doby NODE1 můžete odeslat pakety node2, ale NODE2 nemůže odpovědět. NODE1 ztratí kvorum a vypne cluster.

Tomuto scénáři se lze vyhnout přiřazením nepoužívané statické adresy IP, například místní ip adresy například 169.254.1.1, k názvu sítě clusteru za účelem uvedení názvu sítě clusteru do režimu online. Tento proces zjednodušte [najdete v tématu Konfigurace clusteru s podporou převzetí služeb při selhání systému Windows v Azure pro skupiny dostupnosti](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Další informace najdete [v tématu Konfigurace skupin dostupnosti v Azure (GUI).](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

### <a name="availability-group-listener-support"></a>Podpora naslouchací proces skupiny dostupnosti
Naslouchací procesy skupiny dostupnosti jsou podporované na virtuálních počítačích Azure se systémem Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Tato podpora je umožněna pomocí koncových bodů s vyrovnáváním zatížení povolených na virtuálních počítačích Azure, které jsou uzly skupiny dostupnosti. Musíte postupovat podle speciálních kroků konfigurace pro naslouchací procesy pracovat pro klientské aplikace, které běží v Azure, stejně jako ty spuštěné místně.

Existují dvě hlavní možnosti nastavení posluchače: externí (veřejné) nebo interní. Externí (veřejný) naslouchací proces používá nástroj pro vyrovnávání zatížení směřující k internetu a je spojen s veřejnou virtuální IP adresou (VIP), která je přístupná přes internet. Interní naslouchací proces používá interní nástroj pro vyrovnávání zatížení a podporuje pouze klienty v rámci stejné virtuální sítě. Pro oba typy nástrojpro vyrovnávání zatížení je nutné povolit direct server return. 

Pokud skupina dostupnosti zahrnuje více podsítí Azure (například nasazení, které překračuje oblasti Azure), musí připojovací řetězec klienta obsahovat "**MultisubnetFailover=True**". Výsledkem jsou pokusy o paralelní připojení s replikami v různých podsítích. Pokyny k nastavení posluchače naleznete v tématu

* [Konfigurace naslouchací proces ILB pro skupiny dostupnosti v Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Konfigurace externího naslouchací hopro skupiny dostupnosti v Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Ke každé replice dostupnosti se stále můžete připojit samostatně přímým připojením k instanci služby. Vzhledem k tomu, že skupiny dostupnosti jsou zpětně kompatibilní s klienty zrcadlení databáze, můžete se připojit k replikám dostupnosti, jako jsou partneři zrcadlení databáze, pokud jsou repliky nakonfigurovány podobně jako zrcadlení databáze:

* Jedna primární replika a jedna sekundární replika
* Sekundární replika je nakonfigurována jako nečitelná **(čitelná sekundární** možnost nastavená na **ne)**

Příklad připojovací řetězec klienta, který odpovídá této konfiguraci zrcadlení databáze pomocí ADO.NET nebo nativního klienta serveru SQL Server, je uveden níže:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Další informace o připojení klienta naleznete v tématu:

* [Použití klíčových slov připojovacího řetězce s nativním klientem serveru SQL Server](https://msdn.microsoft.com/library/ms130822.aspx)
* [Připojení klientů k relaci zrcadlení databáze (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Připojení k naslouchacímu procesu skupiny dostupnosti v hybridním IT](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Naslouchací procesy skupiny dostupnosti, připojení klienta a převzetí služeb při selhání aplikace (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Použití připojovacích řetězců zrcadlení databáze se skupinami dostupnosti](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latence sítě v hybridním IT
Měli byste nasadit řešení HADR s předpokladem, že může existovat období s vysokou latencí sítě mezi místní sítí a Azure. Při nasazování replik do Azure byste měli použít asynchronní potvrzení namísto synchronního potvrzení pro režim synchronizace. Při nasazování serverů zrcadlení databáze místně i v Azure použijte režim vysokého výkonu namísto režimu vysoké bezpečnosti.

### <a name="geo-replication-support"></a>Podpora geografické replikace
Geografická replikace na discích Azure nepodporuje datový soubor a soubor protokolu stejné databáze, které mají být uloženy na samostatných discích. GRS replikuje změny na každém disku nezávisle a asynchronně. Tento mechanismus zaručuje pořadí zápisu v rámci jednoho disku na geograficky replikované kopie, ale ne přes geograficky replikované kopie více disků. Pokud nakonfigurujete databázi pro uložení datového souboru a souboru protokolu na samostatné disky, obnovené disky po havárii mohou obsahovat aktuálnější kopii datového souboru než soubor protokolu, což přeruší zápis na server SQL Server a vlastnosti ACID Transakce. Pokud nemáte možnost zakázat geografickou replikaci v účtu úložiště, měli byste uchovávat všechna data a soubory protokolu pro danou databázi na stejném disku. Pokud je nutné použít více než jeden disk z důvodu velikosti databáze, je třeba nasadit jeden z řešení zotavení po havárii uvedených výše k zajištění redundance dat.

## <a name="next-steps"></a>Další kroky
Pokud potřebujete vytvořit virtuální počítač Azure s SQL Serverem, přečtěte si informace [o zřizování virtuálního počítače SQL Serveru v Azure](virtual-machines-windows-portal-sql-server-provision.md).

Pokud chcete dosáhnout nejlepšího výkonu ze serveru SQL Server spuštěného na virtuálním počítači Azure, přečtěte si pokyny v článku [Doporučené postupy výkonu pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md).

Další témata související se spuštěním SQL Serveru ve virtuálních počítačích Azure najdete v článku [SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Další prostředky
* [Instalace nové doménové struktury služby Active Directory v Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Vytvoření clusteru s podporou převzetí služeb při selhání pro skupiny dostupnosti v virtuálním počítači Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

