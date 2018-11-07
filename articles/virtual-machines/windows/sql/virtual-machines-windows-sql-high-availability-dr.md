---
title: Vysoká dostupnost a zotavení po havárii pro SQL Server | Dokumentace Microsoftu
description: Diskuzi o různých typech HADR strategie pro SQL Server provozovaný v Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 463ef5f4a655617074915078fb4ced9e596f8957
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257710"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines

Microsoft Azure virtual machines (VM) pomocí serveru SQL Server může pomoct snížit náklady vysokou dostupnost a po havárii (HADR) databáze řešení pro zotavení. Většina řešení SQL Server HADR jsou podporovány na virtuálních počítačích Azure, jako jen pro Azure i hybridní řešení. V řešení jen pro Azure celý systém HADR běží v Azure. V hybridní konfigurace část řešení běží v Azure a další část spustí místní ve vaší organizaci. Flexibilní prostředí Azure můžete splnit snazší plánování rozpočtu a požadavky HADR systémů databáze SQL serveru do Azure přesunout částečně nebo zcela.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Principy potřebné pro HADR řešení
Je jenom na vás k zajištění, že má databázový systém HADR možnosti, které vyžaduje smlouvu o úrovni služeb (SLA). Skutečnost, že Azure nabízí mechanismy, vysokou dostupnost, jako je služba opravy pro cloud services a detekce selhání pro zotavení pro virtuální počítače, sám není zaručit, že lze splnit požadované smlouvy SLA. Tyto mechanismy ochrany vysokou dostupnost virtuálních počítačů, ale ne vysokou dostupnost SQL serveru běžícího uvnitř virtuálních počítačů. Je možné instanci systému SQL Server selže, když je virtuální počítač online a v dobrém stavu. Kromě toho i vysokou dostupnost, které umožňují mechanismy, které poskytuje Azure výpadek virtuálních počítačů, protože události, například obnovení ze softwaru nebo selhání hardwaru a upgrady operačního systému.

Kromě toho geograficky redundantního úložiště (GRS) v Azure, které je implementované pomocí funkce volána geografickou replikaci, nemusí být řešení pro zotavení po havárii odpovídající databází. Protože geografická replikace asynchronně odešle data, může dojít ke ztrátě při případné havárii nejnovější aktualizace. Další informace týkající se geografická replikace omezení jsou popsané v [geografická replikace není podporována pro data a soubory protokolu na různých discích](#geo-replication-support) oddílu.

## <a name="hadr-deployment-architectures"></a>HADR architektur nasazení
SQL Server HADR technologií, které se podporují v Azure zahrnují:

* [Skupiny dostupnosti AlwaysOn](https://technet.microsoft.com/library/hh510230.aspx)
* [Instance clusteru převzetí služeb při selhání AlwaysOn](https://technet.microsoft.com/library/ms189134.aspx)
* [Přesouvání protokolu](https://technet.microsoft.com/library/ms187103.aspx)
* [Zálohování SQL serveru a obnovení pomocí služby Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx)
* [Zrcadlení databáze](https://technet.microsoft.com/library/ms189852.aspx) – zastaralé v systému SQL Server 2016

Je možné kombinovat technologie společně k implementaci řešení systému SQL Server, který má vysokou dostupnost a možnosti zotavení po havárii. V závislosti na technologii, kterou používáte může vyžadovat hybridního nasazení tunelové připojení VPN s Azure virtual network. Následující části popisují, můžete některé příklady architektur nasazení.

## <a name="azure-only-high-availability-solutions"></a>Jenom pro Azure: řešení s vysokou dostupností

Řešení vysoké dostupnosti pro SQL Server může mít na úrovni databáze pomocí skupin dostupnosti Always On - nazývají skupiny dostupnosti. Můžete také vytvořit řešení vysoké dostupnosti na úrovni instance instancemi vždy na převzetí služeb při selhání clusteru – převzetí služeb při selhání instance clusteru. Pro zajištění další redundance můžete vytvořit redundanci na obou úrovních vytvořením skupiny dostupnosti na převzetí služeb při selhání instance clusteru. 

| Technologie | Příklady architektur |
| --- | --- |
| **Skupiny dostupnosti** |Repliky dostupnosti spuštěných na virtuálních počítačích Azure ve stejné oblasti zajištění vysoké dostupnosti. Musíte nakonfigurovat řadič domény virtuálního počítače, protože Windows failover clustering vyžaduje doménu služby Active Directory.<br/> ![Skupiny dostupnosti](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Další informace najdete v tématu [konfigurace skupiny dostupnosti v Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Instance clusteru převzetí služeb při selhání** |Instance clusteru převzetí služeb při selhání (FCI), které vyžadují sdílené úložiště, můžete vytvořit 3 různými způsoby.<br/><br/>1. Převzetí služeb při selhání uzlu clusteru se systémem na virtuálních počítačích Azure s používáním připojené služby storage [systému Windows Server 2016 prostory úložiště – přímé \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) k poskytování softwarových virtuální síť SAN.<br/><br/>2. Převzetí služeb při selhání uzlu clusteru se systémem na virtuálních počítačích Azure s úložištěm podporuje Clusterové řešení třetí strany. Konkrétní příklad, který používá SIOS DataKeeper, naleznete v tématu [vysoká dostupnost pro sdílenou složku pomocí clusteringu převzetí služeb při selhání a 3. stran software SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Převzetí služeb při selhání uzlu clusteru se systémem na virtuálních počítačích Azure s vzdálené iSCSI Target sdílené blokové úložiště přes ExpressRoute. Například NetApp Private Storage (NPS) poskytuje cíl iSCSI prostřednictvím ExpressRoute s Equinix na virtuální počítače Azure.<br/><br/>Pro sdílené úložiště jiných výrobců a řešení replikace dat byste požádat dodavatele pro všechny problémy související se přístup k datům na převzetí služeb při selhání.<br/><br/>Všimněte si, že při použití FCI nad [Azure File storage](https://azure.microsoft.com/services/storage/files/) se zatím nepodporuje, protože toto řešení nevyužívá Premium Storage. Pracujeme na tom pro podporu tohoto brzy. |

## <a name="azure-only-disaster-recovery-solutions"></a>Jenom pro Azure: řešení zotavení po havárii
Můžete mít řešení zotavení po havárii pro databáze SQL serveru v Azure pomocí skupin dostupnosti, zrcadlení databáze nebo zálohování a obnovení s využitím úložiště objektů BLOB.

| Technologie | Příklady architektur |
| --- | --- |
| **Skupiny dostupnosti** |Dostupnost replik spuštěných v různých datacentrech ve virtuálních počítačích Azure pro zotavení po havárii. Toto řešení mezi různými oblastmi chrání před výpadkem kompletní lokality. <br/> ![Skupiny dostupnosti](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>V rámci oblasti musí být všechny repliky v rámci stejné cloudové službě a stejné virtuální síti. Vzhledem k tomu, že každou oblast, bude mít samostatnou virtuální síť, tato řešení vyžadovat virtuální sítě pro připojení k virtuální síti. Další informace najdete v tématu [konfigurace připojení typu VNet-to-VNet pomocí webu Azure portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Podrobné pokyny najdete v tématu [konfigurace skupiny dostupnosti SQL Server na virtuálních počítačích Azure v různých oblastech](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Zrcadlení databáze** |Instanční objekt a zrcadlové a servery spuštěné v různých datových center pro zotavení po havárii. Je nutné nasadit pomocí certifikátů serveru, protože domény služby active directory nemůžou zahrnovat víc datových center.<br/>![Zrcadlení databáze](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Zálohování a obnovení pomocí služby Azure Blob Storage** |Produkční databáze zálohovat přímo do úložiště objektů blob v jiném datovém centru pro zotavení po havárii.<br/>![Zálohování a obnovení](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Další informace najdete v tématu [zálohování a obnovení pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikace a převzetí služeb při selhání SQL serveru do Azure pomocí Azure Site Recovery** |Provozní Server SQL z jednoho datového centra Azure replikované přímo do služby Azure Storage jiné datové centrum Azure pro zotavení po havárii.<br/>![Replikovat pomocí Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>Další informace najdete v tématu [ochraně SQL serveru pomocí zotavení po havárii pro SQL Server a Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybridní IT: Řešení zotavení po havárii
Můžete mít řešení zotavení po havárii pro databáze SQL serveru v hybridní IT prostředí pomocí skupin dostupnosti, zrcadlení databáze, přesouvání protokolu a zálohování a obnovení s úložištěm objektů BLOB Azure.

| Technologie | Příklady architektur |
| --- | --- |
| **Skupiny dostupnosti** |Některé repliky dostupnosti používané virtuálními počítači Azure a ostatními replikami, místní zotavení po havárii mezi weby. Produkční lokality může být buď místně nebo v datovém centru Azure.<br/>![Skupiny dostupnosti](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Protože všechny repliky dostupnosti musí být ve stejném clusteru převzetí služeb při selhání, clusteru musí zahrnovat model obě sítě (cluster převzetí služeb při selhání více podsítí). Tato konfigurace vyžaduje propojení VPN mezi Azure a v místní síti.<br/><br/>Pro zotavení po havárii úspěšné vašich databází nainstalujete také repliky řadiče domény v lokalitě zotavení po havárii.<br/><br/>Je možné použít Průvodce přidáním repliky v aplikaci SSMS přidání Azure repliky existující skupiny dostupnosti Always On. Další informace najdete v článku kurz: rozšíření vaší skupiny dostupnosti Always On do Azure. |
| **Zrcadlení databáze** |Jeden partnerovi běžet ve Virtuálním počítači Azure a další spuštění on-premises pro zotavení po havárii webů pomocí certifikátů serveru. Partneři nemusí být ve stejné doméně služby Active Directory a bez připojení k síti VPN je povinný.<br/>![Zrcadlení databáze](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Jiné databáze zrcadlení scénář zahrnuje jeden partnerovi běžet ve Virtuálním počítači Azure a dalších spuštěné místně ve stejné doméně služby Active Directory pro zotavení po havárii mezi weby. A [připojení VPN mezi virtuální sítí Azure a v místní síti](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) je povinný.<br/><br/>Pro zotavení po havárii úspěšné vašich databází nainstalujete také repliky řadiče domény v lokalitě zotavení po havárii. |
| **Přesouvání protokolu** |Jeden server spuštěný ve Virtuálním počítači Azure a další spuštění on-premises pro zotavení po havárii mezi weby. Přesouvání protokolu, závisí na sdílení souborů Windows, takže je nutné připojení VPN mezi virtuální sítí Azure a v místní síti.<br/>![Přesouvání protokolu](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Pro zotavení po havárii úspěšné vašich databází nainstalujete také repliky řadiče domény v lokalitě zotavení po havárii. |
| **Zálohování a obnovení pomocí služby Azure Blob Storage** |Místní provozních databází zálohují přímo na úložiště objektů blob v Azure pro zotavení po havárii.<br/>![Zálohování a obnovení](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Další informace najdete v tématu [zálohování a obnovení pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikace a převzetí služeb při selhání SQL serveru do Azure pomocí Azure Site Recovery** |V místním produkčním replikované přímo do úložiště Azure pro zotavení po havárii serveru SQL Server.<br/>![Replikovat pomocí Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>Další informace najdete v tématu [ochraně SQL serveru pomocí zotavení po havárii pro SQL Server a Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Důležité informace týkající se SQL Server HADR v Azure
Virtuální počítače Azure, úložiště a sítě mají jiné charakteristiky provozní než místní, nevirtualizovaných infrastruktury IT. Úspěšné dokončení implementace řešení HADR SQL serveru v Azure vyžaduje pochopit tyto rozdíly a navrhněte řešení jim přizpůsobit.

### <a name="high-availability-nodes-in-an-availability-set"></a>Vysoká dostupnost uzly ve skupině dostupnosti
Skupiny dostupnosti v Azure umožňují uzly vysoké dostupnosti umístěte do samostatných selhání (FD) a aktualizační doména (ud). Pro virtuální počítače Azure budou umístěny ve stejné sadě dostupnosti musíte je nasadit v rámci stejné cloudové služby. Pouze uzly ve stejné cloudové službě se můžete zúčastnit ve stejné sadě dostupnosti. Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="failover-cluster-behavior-in-azure-networking"></a>Chování clusteru převzetí služeb při selhání v sítích Azure
RFC neodpovídajících služba DHCP v Azure může způsobit vytvoření určitých převzetí služeb při selhání clusteru konfigurace, které selžou kvůli název sítě s clustery přiřazením duplicitní IP adresu, jako je například stejné IP adresy jako jeden z uzlů clusteru. Jedná se o problém při implementaci skupin dostupnosti, které závisí na funkci clusteru převzetí služeb při selhání Windows.

Když clusteru se dvěma uzly se vytvoří a přepne do online režimu vezměte v úvahu scénář:

1. Cluster převede do režimu online a potom NODE1 požádá o dynamicky přidělovanou adresu IP název sítě s clustery.
2. Žádné IP adresy, než NODE1 jeho vlastní IP adresu je dán službu DHCP, protože služba DHCP rozpozná, že žádost pochází z Uzel1 samotný.
3. Windows zjistí, že duplicitní adresa se přiřadí NODE1 i název sítě s clustery převzetí služeb při selhání a výchozí skupiny clusteru selže do režimu online.
4. Výchozí skupiny clusteru přesune Uzel2, která zpracovává NODE1 na IP adresu jako IP adresu clusteru a přináší výchozí skupiny clusteru online.
5. Když NODE2 se pokusí navázat připojení s UZEL1, pakety, zaměřuje na NODE1 nikdy neopustí NODE2 protože se překládá NODE1 na IP adresu na sebe sama. NODE2 nelze navázat spojení s UZEL1, pak dojde ke ztrátě kvora a vypne clusteru.
6. Do té doby NODE1 mohou odesílat pakety Uzel2, ale nemůže odpovědět, UZEL2. Uzel1 dojde ke ztrátě kvora a cluster vypne.

V tomto scénáři se můžete vyhnout tak, že přiřadíte nepoužívaná statická IP adresa, jako jsou specifická pro připojení IP adresu, a to jako 169.254.1.1, název sítě s clustery pamětí pro síťový název clusteru online. Pro zjednodušení tohoto procesu, naleznete v tématu [Windows konfigurace převzetí služeb při selhání clusteru v Azure pro skupiny dostupnosti](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Další informace najdete v tématu [konfigurace skupin dostupnosti v Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Podpora naslouchacího procesu skupiny dostupnosti
Naslouchacích procesů skupin dostupnosti se podporují v Azure virtuální počítače se systémem Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows serveru 2016. Tato podpora je možné díky použití s vyrovnáváním zatížení povolené koncové body na virtuálních počítačích Azure, které jsou uzly skupiny dostupnosti. Je třeba dodržovat zvláštní konfigurační kroky pro naslouchací procesy pro oba klientské aplikace, na kterých běží v Azure a jak jsou spuštěné místně.

Existují dvě hlavní možnosti pro nastavení vašemu naslouchacímu procesu: externích (veřejných) nebo interní. Naslouchací proces externích (veřejných) používá internetový nástroj pro vyrovnávání zatížení a souvisí s veřejná virtuální IP (VIP), která je přístupná přes internet. Interního naslouchacího procesu interní nástroj používá a podporuje pouze klienty ve stejné virtuální síti. Buď typ nástroje pro vyrovnávání zatížení, je nutné povolit přímou odpověď serveru. 

Pokud se skupina dostupnosti zahrnuje několik podsítí Azure (například nasazení, které překročí oblasti Azure), musí obsahovat řetězec připojení klienta "**MultisubnetFailover = True**". Výsledkem je pokusy o paralelní připojení repliky v různých podsítích. Pokyny týkající se nastavení naslouchacího procesu najdete v tématu

* [Konfigurace naslouchacího procesu ILB pro skupiny dostupnosti v Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Konfigurace externího naslouchacího procesu skupiny dostupnosti v Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Můžete se připojit k každou repliku dostupnosti samostatně přímým připojením k instanci služby. Navíc protože skupiny dostupnosti jsou zpětně kompatibilní s klienty zrcadlení databáze, můžete připojit k replice dostupnosti jako databáze zrcadlení partneři tak dlouho, dokud repliky jsou nakonfigurované podobný zrcadlení databáze:

* Jedna primární replika a jedna sekundární replika
* Sekundární repliky nakonfigurován jako bez možnosti čtení (**čitelné sekundární** možnost nastavená na hodnotu **ne**)

Příklad připojovacího řetězce klienta, která odpovídá tuto konfiguraci jako v zrcadlení databáze pomocí ADO.NET nebo SQL Server Native Client je nižší než:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Další informace o připojení klienta najdete v tématu:

* [Pomocí klíčových slov připojovací řetězec SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Připojení klientů k relaci zrcadlení databáze (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Připojení k naslouchacím procesem skupiny dostupnosti v hybridním IT](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Naslouchacích procesů skupin dostupnosti, připojení klienta a aplikace převzetí služeb při selhání (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Pomocí řetězce připojení zrcadlení databáze se skupinami dostupnosti](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latence sítě v hybridním IT
Měli byste nasadit řešení HADR za předpokladu, že může být období s vysokou síťovou latencí mezi vaší místní sítí a Azure. Při nasazování repliky na Azure, abyste používali asynchronního potvrzování místo synchronním potvrzováním pro režim synchronizace. Při nasazení databáze zrcadlení servery v místním i v Azure použít režim vysoce výkonné místo režimu vysokou bezpečností.

### <a name="geo-replication-support"></a>Podpora geografické replikace
Geografická replikace disků v Azure nepodporuje datový soubor a soubor protokolu ze stejné databáze, který bude uložen na různých discích. GRS replikuje změny na každém disku nezávisle a asynchronně. Tento mechanismus zaručuje zápisu pořadí v rámci jednoho disku na geograficky replikované kopie, ale ne napříč geograficky replikované kopie několik disků. Pokud nakonfigurujete databázi pro ukládání jeho datového souboru a jeho soubor protokolu na různých discích, obnovené disky po havárii může obsahovat více aktuální kopii souboru dat než v souboru protokolu, což konce dávky zápisu protokolu v systému SQL Server a kyseliny vlastnosti t ctions. Pokud nemáte možnost zakázat geografické replikace do účtu úložiště, byste měli mít všechna data a soubory protokolu pro danou databázi na stejném disku. Pokud je nutné použít více než jeden disk z důvodu velikosti databáze, musíte nasadit některé z výše uvedených zajistit redundanci dat řešení pro zotavení po havárii.

## <a name="next-steps"></a>Další postup
Pokud potřebujete vytvořit virtuální počítač Azure s SQL serverem, přečtěte si téma [zřízení virtuálního počítače SQL serveru v Azure](virtual-machines-windows-portal-sql-server-provision.md).

Pokud chcete získat nejlepší výkon ze serveru SQL Server běžící na Virtuálním počítači Azure, přečtěte si pokyny v [osvědčené postupy z hlediska výkonu pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Další témata související s SQL serverem na virtuálních počítačích Azure, najdete v části [systému SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Další prostředky
* [Instalace nové doménové struktury služby Active Directory v Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Vytvoření clusteru převzetí služeb při selhání skupiny dostupnosti ve virtuálním počítači Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

