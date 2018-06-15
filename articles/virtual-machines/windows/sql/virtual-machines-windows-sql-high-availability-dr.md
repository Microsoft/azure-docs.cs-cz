---
title: Vysoká dostupnost a zotavení po havárii pro SQL Server | Microsoft Docs
description: Informace o různých typů HADR strategie pro SQL Server běžící v Azure Virtual Machines.
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
ms.openlocfilehash: e9b4ca959b93e097bb52a841cec02cc476ef5f48
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401255"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines

Virtuální počítače Microsoft Azure (VM) se systémem SQL Server může pomoci snížit náklady na vysokou dostupnost a po havárii (HADR) obnovení databáze řešení. Většina řešení SQL Server HADR jsou podporovány ve virtuálních počítačích Azure, jen Azure i jako hybridní řešení. V řešení jen Azure celý systém HADR běží v Azure. V hybridní konfigurace část řešení běží v Azure a další část spustí místní ve vaší organizaci. Flexibilitu prostředí Azure umožňuje přesunout částečně nebo zcela do Azure pro uspokojení nároky a HADR požadavky systémů databáze systému SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Principy potřebu řešení s HADR
Můžete se rozhodnout, aby, zda má systém databáze HADR možností, které vyžaduje smlouvu úrovně služeb (SLA). Skutečnost, že Azure poskytuje vysokou dostupnost mechanismy, jako je například služba opravy pro cloudové služby a detekce chyb obnovení pro virtuální počítače, které samo o sobě zaručit, že splňujete požadované smlouvy o úrovni služeb. Tyto mechanismy chránit zajištění vysoké dostupnosti virtuálních počítačů, ale ne vysokou dostupnost systému SQL Server spuštěnou uvnitř virtuálních počítačů. Je možné pro instanci systému SQL Server k selhání, zatímco virtuální počítač je online a v pořádku. Kromě toho i vysokou dostupnost, které mechanismy, které poskytuje Azure povolit výpadek virtuálních počítačů z důvodu události je to třeba obnovení ze softwaru nebo selhání hardwaru a upgrady operačního systému.

Geograficky redundantní úložiště (GRS) v Azure, což je implementováno s funkci geografická replikace, navíc nemusí být řešení pro zotavení po havárii odpovídající, pro vaše databáze. Protože geografická replikace asynchronně odešle data, může dojít ke ztrátě v případě havárie poslední aktualizace. Další informace týkající se omezeních geografická replikace jsou popsané v [geografická replikace není podporována pro data a soubory protokolu na různých discích](#geo-replication-support) části.

## <a name="hadr-deployment-architectures"></a>HADR nasazení architektury
SQL Server HADR technologie, které jsou podporovány v Azure patří:

* [Always On skupiny dostupnosti](https://technet.microsoft.com/library/hh510230.aspx)
* [Always On instance clusteru převzetí služeb při selhání](https://technet.microsoft.com/library/ms189134.aspx)
* [Přesouvání protokolu](https://technet.microsoft.com/library/ms187103.aspx)
* [Zálohování systému SQL Server a obnovení služby úložiště objektů Blob v Azure](https://msdn.microsoft.com/library/jj919148.aspx)
* [Zrcadlení databáze](https://technet.microsoft.com/library/ms189852.aspx) – nepoužívané v systému SQL Server 2016

Je možné kombinovat technologie společně k implementaci řešení SQL serveru, který má vysokou dostupnost a funkcím pro obnovení po havárii. V závislosti na technologie, které používáte může vyžadovat hybridní nasazení tunelového připojení sítě VPN s virtuální síť Azure. V níže uvedených částech ukážeme některé architektur příklad nasazení.

## <a name="azure-only-high-availability-solutions"></a>Jenom Azure: řešení s vysokou dostupností

Řešení vysoké dostupnosti může mít pro SQL Server na úrovni databáze pomocí skupin dostupnosti Always On - nazývají skupiny dostupnosti. Můžete také vytvořit řešení vysoké dostupnosti na úrovni instance instancemi vždy na převzetí služeb při selhání clusteru – převzetí služeb při selhání instance clusteru. Pro další redundanci můžete vytvořit redundance na obou úrovních vytvořením skupiny dostupnosti na převzetí služeb při selhání instance clusteru. 

| Technologie | Příklad architektury |
| --- | --- |
| **Skupiny dostupnosti** |Spuštění ve virtuálních počítačích Azure ve stejné oblasti replik dostupnosti zajištění vysoké dostupnosti. Musíte nakonfigurovat řadič domény virtuálního počítače, protože Windows převzetí služeb při selhání clustering vyžaduje domény služby Active Directory.<br/> ![Skupiny dostupnosti](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Další informace najdete v tématu [konfigurace skupiny dostupnosti v Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Instance clusteru převzetí služeb při selhání** |Instance clusteru převzetí služeb při selhání (FCI), které vyžadují sdílené úložiště, můžete vytvořit 3 různými způsoby.<br/><br/>1. Cluster převzetí služeb při selhání dvěma uzly se systémem ve virtuálních počítačích Azure s použitím připojené úložiště [Windows Server 2016 prostory úložiště – přímé \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) zajistit softwarový virtuální síť SAN.<br/><br/>2. Cluster převzetí služeb při selhání dvěma uzly se systémem ve virtuálních počítačích Azure s úložištěm podporuje clustering řešení třetí strany. Konkrétní příklad, který používá s DataKeeper najdete v tématu [vysoká dostupnost pro sdílenou složku pomocí clustering převzetí služeb při selhání a 3. stran software s DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Clusteru s podporou převzetí služeb při selhání dvěma uzly ve virtuálních počítačích Azure s vzdálené služby iSCSI Target sdílené blokové úložiště přes ExpressRoute. Například NetApp privátní úložiště (NPS) zpřístupní cíle iSCSI prostřednictvím ExpressRoute s Equinix na virtuálních počítačích Azure.<br/><br/>Pro sdílené úložiště jiných výrobců a řešení replikace dat by měl kontaktujte dodavatele kvůli veškeré problémy související s přístup k datům na převzetí služeb při selhání.<br/><br/>Všimněte si, že pomocí FCI na [Azure File storage](https://azure.microsoft.com/services/storage/files/) se zatím nepodporuje, protože toto řešení nevyužívá Storage úrovně Premium. Pracujeme na brzy to podporují. |

## <a name="azure-only-disaster-recovery-solutions"></a>Jenom Azure: řešení zotavení po havárii
Můžete mít řešení zotavení po havárii pro databáze SQL Server v Azure pomocí skupin dostupnosti, zrcadlení databáze nebo zálohování a obnovení pomocí úložiště objektů BLOB.

| Technologie | Příklad architektury |
| --- | --- |
| **Skupiny dostupnosti** |Repliky dostupnosti systémem přes několik datových center ve virtuálních počítačích Azure pro zotavení po havárii. Toto řešení mezi oblastmi chrání před dokončení lokality výpadku. <br/> ![Skupiny dostupnosti](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>V oblasti musí být všechny repliky v rámci stejné cloudové služby a stejnou virtuální síť. Protože každá oblast bude mít samostatné virtuální sítě, tato řešení vyžadují virtuální síť připojení virtuální sítě. Další informace najdete v tématu [konfigurace připojení typu VNet-to-VNet pomocí portálu Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Podrobné pokyny najdete v tématu [nakonfigurovat skupinu dostupnosti SQL Server na virtuálních počítačích Azure v různých oblastech](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Zrcadlení databáze** |Objekt zabezpečení a zrcadlení a servery v různých datových centrech pro zotavení po havárii. Je nutné nasadit pomocí certifikátů serveru, protože domény služby active directory nemůžou zahrnovat několik datových center.<br/>![Zrcadlení databáze](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Zálohování a obnovení pomocí služby Azure Blob Storage** |Provozní databáze zálohovat přímo do úložiště objektů blob v jiném datovém centru pro zotavení po havárii.<br/>![Zálohování a obnovení](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Další informace najdete v tématu [zálohování a obnovení pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikace a převzetí služeb při selhání SQL serveru do Azure s Azure Site Recovery** |Provozním serveru SQL z jednoho datového centra Azure replikovat přímo do služby Azure Storage jiné datové centrum Azure pro zotavení po havárii.<br/>![Replikovat pomocí Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>Další informace najdete v tématu [chránit SQL Server pomocí zotavení po havárii serveru SQL a Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybridního IT: Řešení zotavení po havárii
Můžete mít řešení pro zotavení po havárii pro databáze SQL Server v IT hybridní prostředí pomocí skupin dostupnosti, zrcadlení databáze, přesouvání protokolu a zálohování a obnovení s Azure blog storage.

| Technologie | Příklad architektury |
| --- | --- |
| **Skupiny dostupnosti** |Některé repliky dostupnosti spuštěna ve virtuálních počítačích Azure a další repliky spuštěné místně pro zotavení po havárii webů. Pracoviště může být buď místní nebo v datovém centru Azure.<br/>![Skupiny dostupnosti](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Protože všechny repliky dostupnosti musí být ve stejném clusteru převzetí služeb při selhání, musí clusteru span obě sítě (clusteru s podporou převzetí služeb při selhání více podsítí). Tato konfigurace vyžaduje připojení VPN mezi Azure a místní sítě.<br/><br/>Úspěšné zotavení po havárii databází nainstalujete také repliky řadiče domény v lokalitě pro obnovení po havárii.<br/><br/>Je možné použít Průvodce přidáním repliky v aplikaci SSMS přidat repliku Azure do existující vždy na dostupnosti skupiny. Další informace najdete v tématu kurz: rozšíření vaší vždy na skupiny dostupnosti do Azure. |
| **Zrcadlení databáze** |Jeden partner spuštění ve virtuálním počítači Azure a ostatní spuštěné místní pro zotavení po havárii webů pomocí certifikátů serveru. Partneři nemusí být ve stejné doméně služby Active Directory a není třeba žádné připojení k síti VPN.<br/>![Zrcadlení databáze](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Jiné databáze zrcadlení scénář zahrnuje jednu partnera spuštění ve virtuálním počítači Azure a ostatní spuštěné místní ve stejné doméně služby Active Directory pro zotavení po havárii webů. A [VPN připojení mezi virtuální síť Azure a místní sítí](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) je vyžadován.<br/><br/>Úspěšné zotavení po havárii databází nainstalujete také repliky řadiče domény v lokalitě pro obnovení po havárii. |
| **Přesouvání protokolu** |Jeden server se službou ve virtuálním počítači Azure a ostatní spuštěné místní zotavení po havárii webů. Přesouvání protokolu, závisí na sdílení souborů systému Windows, je nutné připojení VPN mezi virtuální síť Azure a místní sítě.<br/>![Přesouvání protokolu](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Úspěšné zotavení po havárii databází nainstalujete také repliky řadiče domény v lokalitě pro obnovení po havárii. |
| **Zálohování a obnovení pomocí služby Azure Blob Storage** |Místní provozních databází zálohují přímo na úložiště objektů blob v Azure pro zotavení po havárii.<br/>![Zálohování a obnovení](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Další informace najdete v tématu [zálohování a obnovení pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikace a převzetí služeb při selhání SQL serveru do Azure s Azure Site Recovery** |Místní produkční replikují přímo do úložiště Azure pro zotavení po havárii systému SQL Server.<br/>![Replikovat pomocí Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>Další informace najdete v tématu [chránit SQL Server pomocí zotavení po havárii serveru SQL a Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Důležité informace pro SQL Server HADR v Azure
Virtuální počítače Azure, úložiště a síťové mít odlišné provozní vlastnosti než místní, nevirtualizovaný infrastruktury IT. Úspěšné dokončení implementace řešení HADR SQL Server v Azure vyžaduje, abyste pochopili tyto rozdíly a navrhněte řešení jim přizpůsobit.

### <a name="high-availability-nodes-in-an-availability-set"></a>Uzly vysoké dostupnosti v nastavení dostupnosti
Skupiny dostupnosti v Azure umožní umístěte vysokou dostupnost uzly do samostatné domény selhání (FDs) a aktualizace domény (UDs). Pro virtuální počítače Azure umístit do stejné skupiny dostupnosti musíte ho nasadit v rámci stejné cloudové služby. Pouze uzly v rámci stejné cloudové služby mohl stát součástí stejné skupiny dostupnosti. Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="failover-cluster-behavior-in-azure-networking"></a>Chování clusteru převzetí služeb při selhání v Azure sítě
Služba DHCP bez specifikaci RFC v Azure může způsobit vytvoření určité převzetí služeb při selhání konfigurace clusteru k selhání kvůli název sítě clusteru, který je právě přiřazován duplicitní IP adresu, jako je například stejnou IP adresu jako jeden z uzlů clusteru. Jedná se o problém při implementaci skupin dostupnosti, které závisí na funkci systému Windows převzetí služeb při selhání clusteru.

Představte si situaci, při vytvoření clusteru se dvěma uzly a pak přepne do online režimu:

1. Cluster přepne do režimu online a potom Uzel1 požadavky dynamicky přiřazenou IP adresu pro název sítě s clustery.
2. Není zadána adresa IP než Uzel1 na vlastní IP adresu pomocí služby DHCP, vzhledem k tomu, že služba DHCP rozpozná, že požadavek pochází z Uzel1 sám sebe.
3. Systém Windows rozpozná, že duplicitní adresu přiřazen Uzel1 i název sítě s clustery převzetí služeb při selhání, a výchozí skupiny clusteru selže do režimu online.
4. Výchozí skupiny clusteru přesune do Uzel2, která zpracovává Uzel1 na IP adresu jako IP adresu clusteru a přináší výchozí skupiny clusteru online.
5. Když Uzel2 se pokusí navázat připojení s UZEL1, pakety směrované na Uzel1 nikdy neopustí Uzel2, protože vyřešila Uzel1 na IP adresu na sebe sama. Uzel2 nelze navázat připojení s UZEL1, pak dojde ke ztrátě kvora a ukončí clusteru.
6. Do té doby Uzel1 mohou odesílat pakety Uzel2, ale nemůže odpovědět, UZEL2. Uzel1 dojde ke ztrátě kvora a cluster vypne.

Tento scénář se vyhnout přiřazením nepoužívané statickou IP adresu, jako je například místní IP adresa jako 169.254.1.1, název sítě s clustery, aby byla síťového názvu clusteru online. Pro zjednodušení tohoto procesu, najdete v části [Windows konfiguraci clusteru převzetí služeb při selhání v Azure pro skupiny dostupnosti](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Další informace najdete v tématu [konfigurovat skupiny dostupnosti v Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Podpora naslouchací proces skupiny dostupnosti
Naslouchací procesy skupiny dostupnosti jsou podporovány v Azure virtuální počítače se systémem Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Tato podpora je možné pomocí služby Vyrovnávání zatížení sítě koncových bodů povoleno na virtuálních počítačích Azure, které jsou uzly skupiny dostupnosti. Postupujte podle kroků speciální konfigurace pro naslouchací procesy pro oba klientských aplikací, které jsou spuštěné v Azure a také ty spuštěné místně.

Existují dvě hlavní možnosti pro nastavení vaší naslouchací proces: externí (veřejných) nebo interní. Externí naslouchací proces (veřejné) používá Internetovou nástroj pro vyrovnávání zatížení a související s veřejná virtuální IP (VIP), který je přístupný prostřednictvím Internetu. Naslouchací proces interní používá interní nástroj a podporuje pouze klienty ve stejné virtuální síti. Buď typ nástroje pro vyrovnávání zatížení, je nutné povolit přímé odpovědi ze serveru. 

Pokud skupina dostupnosti zahrnuje několik podsítí Azure (například nasazení, které protne oblastí Azure), musí zahrnovat klienta připojovací řetězec "**MultisubnetFailover = True**". Výsledkem pokusy o připojení paralelní s replikami v různých podsítích. Pokyny týkající se nastavení naslouchací proces najdete v tématu

* [Konfigurace naslouchací proces ILB pro skupiny dostupnosti v Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Konfigurace o externí naslouchací proces pro skupiny dostupnosti v Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Stále připojením ke každé repliky dostupnosti samostatně přímým připojením k instanci služby. Navíc vzhledem k tomu, že zpětně kompatibilní s klienty zrcadlení databáze skupiny dostupnosti se můžete připojit k replik dostupnosti jako databáze zrcadlení partnery repliky jsou nakonfigurovány podobná zrcadlení databáze:

* Jedna primární replika a jedna sekundární replika
* Sekundární repliky nakonfigurován jako bez možnosti čtení (**čitelný sekundární** možnost nastavena na **ne**)

Zde je příklad klienta připojovací řetězec, který odpovídá této konfigurace zrcadlení jako databáze pomocí ADO.NET nebo SQL Server Native Client:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Další informace o připojení klientů najdete v tématu:

* [Klíčová slova připojovací řetězec pomocí SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Připojení klientů k databázi zrcadlení relace (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Připojování k naslouchací proces skupiny dostupnosti v hybridní IT](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Naslouchací procesy skupiny dostupnosti, připojení klientů a převzetí služeb při selhání aplikace (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Použití řetězců připojení zrcadlení databáze se skupinami dostupnosti](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latence sítě v hybridní IT
Měli byste nasadit řešení HADR za předpokladu, že může být období s vysoké latenci mezi místní sítí a Azure. Pokud nasazujete repliky do Azure, měli byste použít asynchronního potvrzování místo synchronním potvrzováním pro režim synchronizace. Při nasazování serverů zrcadlení databáze místní i v Azure, použijte režim vysoce výkonné místo režimu vysokou bezpečnost.

### <a name="geo-replication-support"></a>Geografická replikace podpory
Geografická replikace v disky Azure nepodporuje datový soubor a soubor protokolu stejné databáze ukládaly na různých discích. GRS replikovat změny na každém disku nezávisle a asynchronně. Tento mechanismus zaručuje zápisu pořadí v rámci jednoho disku do geograficky replikované kopírovat, ale ne geograficky replikované kopie více disků. Pokud nakonfigurujete databázi k ukládání jeho datového souboru a jeho soubor protokolu na různých discích, obnovené disky po havárii může obsahovat více aktuální kopii datového souboru než soubor protokolu, která dělí předběžné protokolování v systému SQL Server a ACID vlastností transakce. Pokud nemáte možnost zakázat geografická replikace na účtu úložiště, byste měli mít všechna data a soubory protokolu pro danou databázi na stejném disku. Pokud je nutné použít více než jeden disk kvůli překročení velikosti databáze, budete muset nasaďte jednu z výše uvedených zajistit redundanci dat řešení obnovení po havárii.

## <a name="next-steps"></a>Další postup
Pokud potřebujete vytvořit virtuální počítač Azure se systémem SQL Server, přečtěte si téma [zřizování virtuálního počítače systému SQL Server na platformě Azure](virtual-machines-windows-portal-sql-server-provision.md).

Nejlepšího výkonu dosáhnete z SQL Server běžící na virtuálním počítači Azure najdete pokyny v [osvědčené postupy z hlediska výkonu pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Další témata související se systémem SQL Server ve virtuálních počítačích Azure, najdete v části [systému SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Další prostředky
* [Instalace nové doménové struktury služby Active Directory v Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Vytvoření clusteru převzetí služeb při selhání pro skupiny dostupnosti ve virtuálním počítači Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

