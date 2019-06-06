---
title: Konfigurace infrastruktury SAP HANA a operací v Azure | Dokumentace Microsoftu
description: Provozní příručka pro systémy SAP HANA, které jsou nasazeny na virtuálních počítačích Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 156bb4cbf43dc71627f9db785dba574f25139285
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733828"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfigurace infrastruktury SAP HANA a operace v Azure
Tento dokument obsahuje pokyny pro konfiguraci infrastruktury Azure a operační systémy SAP HANA, které jsou nasazené na nativních virtuálních počítačích Azure (VM). Dokument obsahuje také informace o konfiguraci pro SAP HANA Škálováním pro skladovou Položku virtuálního počítače M128s. Tento dokument není určena k nahrazení standardní dokumentaci k SAPU, který obsahuje následující obsah:

- [Příručka věnovaná SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Průvodců instalací SAPU](https://service.sap.com/instguides)
- [Poznámky SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Požadavky
K použití tohoto průvodce potřebujete základní znalosti o následující komponenty Azure:

- [Virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Sítě Azure a virtuální sítě](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Další informace o SAP NetWeaver a jiných komponent SAP v Azure, najdete v článku [SAP v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) část [dokumentace ke službě Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Důležité informace o základní nastavení
Následující části popisují důležité informace o základní nastavení pro nasazení systému SAP HANA na virtuálních počítačích Azure.

### <a name="connect-into-azure-virtual-machines"></a>Připojení do virtuálních počítačů Azure
Jak je uvedeno v [virtuálních počítačů Azure Průvodce plánováním](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), existují dvě základní metody pro připojení do virtuálních počítačů Azure:

- Připojení přes internet a veřejných koncových bodů na virtuálním počítači s Jump nebo na virtuálním počítači, na kterém běží SAP HANA.
- Připojení přes [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) nebo Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Připojení Site-to-site přes VPN nebo ExpressRoute je nezbytné pro produkční scénáře. Tento typ připojení je nutná pro neprodukční scénáře, které informačního kanálu do produkčních scénářů, kde se používá softwaru SAP. Následující obrázek ukazuje příklad připojení webů:

![Připojení mezi weby](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Vyberte typy virtuálních počítačů Azure
Typy virtuálních počítačů Azure, které lze použít pro produkční scénáře jsou uvedeny v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). U scénářů s testovacím širší nativní typy virtuálních počítačů Azure je k dispozici.

>[!NOTE]
> Li se o neprodukční scénářů použití typy virtuálních počítačů, které jsou uvedeny v [Poznámka SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Použití virtuálních počítačů Azure pro produkční scénáře, zkontrolujte, zda virtuální počítače v systému SAP publikována s certifikací SAP HANA [seznam certifikací platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Nasazení virtuálních počítačů v Azure s použitím:

- Na webu Azure portal.
- Rutiny Powershellu pro Azure.
- Azure CLI.

Také můžete nasadit kompletní instalované platformy SAP HANA ve službě Azure VM prostřednictvím [SAP Cloudovou platformu](https://cal.sap.com/). Proces instalace je popsána v [nasadit řešení SAP S/4HANA nebo BW/4HANA v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) nebo s automatizací vydané [tady](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="storage-configuration-for-sap-hana"></a>Konfigurace úložiště pro SAP HANA
Konfigurace úložiště a typy úložiště, který se má použít se SAP HANA v Azure, najdete dokumentu [konfigurace úložiště virtuálních počítačů SAP HANA Azure](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Nastavení virtuální sítě Azure
Pokud máte připojení site-to-site do Azure přes VPN nebo ExpressRoute, musíte mít aspoň jednu virtuální síť Azure, který je připojený prostřednictvím virtuální brány k okruhu ExpressRoute nebo VPN. V jednoduchých nasazení je možné nasadit virtuální brány v podsítě virtuální sítě Azure (VNet), který je hostitelem instance systému SAP HANA i. Instalace SAP HANA, vytvořte dva další podsítě v rámci virtuální sítě Azure. Jednu podsíť hostuje virtuální počítače ke spuštění instance systému SAP HANA. Další podsítě spustí Jumpbox nebo virtuální počítače pro správu, k hostování SAP HANA Studio, další software pro správu nebo aplikační software.

> [!IMPORTANT]
> Mimo funkce, ale další důležité z důvodů výkonu, není možné konfigurovat [Azure síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/) v komunikační trasa mezi aplikací SAP a úroveň databázového systému SAP NetWeaver Hybris nebo S/4HANA na systému SAP. Komunikace mezi aplikační vrstvě SAP a vrstvu DBMS musí být s přímým přístupem. Omezení nezahrnuje [Azure ASG a skupiny zabezpečení sítě pravidla](https://docs.microsoft.com/azure/virtual-network/security-overview) tak dlouho, dokud tato pravidla ASG a skupiny zabezpečení sítě umožní přímé komunikaci. Další scénáře, ve kterém nejsou podporovány síťová virtuální zařízení jsou v komunikační trasy mezi virtuálními počítači Azure, které představují uzly clusteru Linux Pacemaker SBD zařízení a jak je popsáno v [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Nebo v komunikaci se trasy mezi virtuálními počítači Azure a Windows Server SOFS nastavit až, jak je popsáno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s využitím sdílené složky v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Síťová virtuální zařízení v komunikaci cesty může snadno dvakrát latence sítě mezi dva partneři komunikaci, můžete omezit propustnost v kritické cesty mezi aplikační vrstvě SAP a vrstvu DBMS. V některých scénářích zjištěnými se zákazníky může způsobit síťová virtuální zařízení Pacemaker Linuxové clustery selhání v případech, kdy je potřeba komunikace mezi uzly clusteru Linux Pacemaker sdělit své zařízení SBD přes síťové virtuální zařízení.  
> 

> [!IMPORTANT]
> Další návrh, který je **není** zodpovědnosti aplikační vrstvě SAP a vrstvu DBMS do různých virtuálních sítí Azure, které nejsou podporovány je [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi sebou. Doporučujeme oddělit aplikační vrstvě SAP a DBMS vrstvy pomocí podsítí v rámci virtuální sítě Azure, namísto použití různých virtuálních sítích Azure. Pokud se rozhodnete postupujte podle doporučení a místo toho oddělit dvě vrstvy do jiné virtuální sítě, dvě virtuální sítě musí být [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Mějte na paměti, které síťový provoz mezi dvěma [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuálním sítím Azure jsou předmětem náklady na přenos. S velkou datový svazek v po mnoho terabajtů, které se vyměňují mezi aplikační vrstvě SAP a DBMS vrstvu můžete sbírají značné náklady, pokud aplikační vrstvě SAP a DBMS vrstvy je rozdělen mezi dvěma partnerskými virtuálními sítěmi Azure. 

Při instalaci virtuálních počítačů pro spuštění SAP HANA, třeba virtuální počítače:

- Dvě virtuální síťové karty nainstalované: jednu síťovou kartu pro připojení k podsíti správy a jednu síťovou kartu pro připojení z místní sítě a jiných sítí, na instance SAP HANA ve virtuálním počítači Azure.
- Statické privátní IP adresy, které jsou nasazené pro obě virtuální síťové karty.

> [!NOTE]
> Byste měli přiřadit statické IP adresy pomocí Azure znamená, že na jednotlivé virtuální síťové adaptéry. Pro virtuální síťový adaptér by neměl přiřadit statické IP adresy v rámci hostovaného operačního systému. Některé služby Azure, jako je služba Azure Backup závisí na skutečnost, který na alespoň primárního virtuálního síťového adaptéru je nastavena, DHCP, ne statické IP adresy. Viz také dokument [Poradce při potížích s Azure se zálohováním virtuálních počítačů](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Pokud je potřeba přiřadit více statických IP adres k virtuálnímu počítači, musíte přiřadit víc karet Vnic k virtuálnímu počítači.
>
>

Ale pro nasazení, které jsou enduring, budete muset vytvořit virtuální datové centrum síťové architektury v Azure. Tato architektura se doporučuje oddělení brány virtuální sítě Azure, která se připojuje k místním do samostatné virtuální sítě Azure. Tento samostatný virtuální sítě by měl hostovat veškerý provoz, které se zasílají buď místní nebo k Internetu. Tento přístup umožňuje nasadit software pro auditování a protokolování provoz, který zadá virtuální datové centrum Azure v tento samostatný virtuální síti centra. Je proto nutné jednu virtuální síť, který je hostitelem veškerý software a konfigurace, které se týkají in - a odchozí provoz do nasazení vašeho řešení Azure.

Články [virtuální datové centrum Azure: Z hlediska sítě](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) a [virtuální datové centrum Azure a rovina podnikového řízení](https://docs.microsoft.com/azure/architecture/vdc/) poskytují další informace o virtuální datové centrum a související návrh virtuální sítě Azure.


>[!NOTE]
>Provoz, který probíhá mezi virtuální síti centra a pomocí virtuální sítě paprsků [Azure VNet peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) je předmětem další [náklady](https://azure.microsoft.com/pricing/details/virtual-network/). Na základě těchto nákladů, možná budete muset zvažte učinění ohrožení mezi systémem striktní návrh hvězdicové sítě a spouští několik [Azure ExpressRoute Gateway](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) připojit k 'paprsky' Pokud chcete vynechat, VNet peering. Však zavést další Azure ExpressRoute Gateway [náklady](https://azure.microsoft.com/pricing/details/vpn-gateway/) také. Můžete také setkat další náklady na software jiných výrobců, které používáte pro síťový provoz, protokolování, auditování a monitorování. Závisí na náklady pro výměnu dat prostřednictvím partnerského vztahu virtuálních sítí na jedné straně a náklady na vytvořené další brány Azure ExpressRoute a další softwarové licence, můžete se rozhodnout pro mikrosegmentaci v rámci jedné virtuální síti pomocí podsítí jako jednotka izolace místo virtuálních sítí.


Přehled různých metod pro přiřazení IP adres najdete v tématu [typy IP adres a metody přidělování v Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Pro virtuální počítače spouštějí SAP HANA byste měli spolupracovat s statické IP adresy přiřazené. Důvodem je, že některé atributy konfigurace pro HANA odkazují na IP adresy.

[Azure skupiny zabezpečení sítě (Nsg)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) se používají ke směrování provozu, který se směruje do instance SAP HANA nebo jumpbox. Skupiny zabezpečení sítě a nakonec [skupiny zabezpečení aplikací](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) jsou přidružené k podsíti SAP HANA a podsítě pro správu.

Následující obrázek znázorňuje přehled hrubý nasazení schématu pro SAP HANA po hvězdicové architektury virtuální sítě:

![Hrubý nasazení schématu pro SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

SAP HANA v Azure nasadit bez připojení site-to-site, stále chcete stínit instance SAP HANA z veřejného Internetu a skrýt za dopředné proxy serverem. V tomto scénáři základní nasazení spoléhá na Azure integrované služby DNS přeložit názvy hostitelů. Ve složitějším nasazení, kde veřejně přístupnou IP adresy se používají jsou obzvláště důležité, Azure integrované služby DNS. Pomocí skupin zabezpečení sítě Azure a [síťových virtuálních zařízení Azure](https://azure.microsoft.com/solutions/network-appliances/) k řízení, monitorování, směrování z Internetu do vaší virtuální síti Azure architektury v Azure. Následující obrázek ukazuje hrubý schéma pro nasazení SAP HANA bez připojení site-to-site v rozbočovač a uvedenou architekturu virtuální sítě:
  
![Hrubý nasazení schématu pro SAP HANA bez připojení site-to-site](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Další popis o tom, jak používat Azure síťová virtuální zařízení na sledování a řízení přístupu z Internetu bez centra a architekturu virtuální sítě paprsků najdete v článku [nasazení vysoce dostupných síťových virtuálních zařízení](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurace infrastruktury Azure pro horizontální navýšení kapacity SAP HANA
Microsoft má jeden SKU virtuálních počítačů řady M-Series, s certifikací SAP HANA konfigurace škálování na víc systémů. Typ virtuálního počítače M128s je teď má certifikaci pro škálování až 16 uzlů. Změny v certifikace škálování na více instancí SAP HANA na virtuálních počítačích Azure, zkontrolujte [seznam certifikací platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Minimální verze operačního systému pro nasazení horizontální navýšení kapacity konfigurací ve virtuálních počítačích Azure jsou:

- SUSE Linux 12 SP3
- Red hat Linux 7.4

Horizontální navýšení kapacity certifikace 16 uzlů

- Hlavní uzel je jeden uzel
- Maximální počet 15 uzlů se pracovní uzly

>[!NOTE]
>Horizontální navýšení kapacity nasazení virtuálního počítače Azure není možné používat pohotovostní uzel
>

Důvod nebude moct konfigurovat pohotovostní uzel jsou dva účely:

- Azure má v tuto chvíli žádné nativní služby systému souborů NFS. V důsledku sdílených složek NFS je potřeba nakonfigurovat pomocí funkce třetích stran.
- Žádná konfigurace systému souborů NFS třetích stran můžou ke splnění kritérií latence úložiště pro SAP HANA s jejich řešení nasazená v Azure.

V důsledku toho **/hana/data** a **/hana/log** svazky nelze sdílet. Nesdílí tyto svazky jednotlivé uzly, zabraňuje využití pohotovostní uzel SAP HANA v konfiguraci horizontální navýšení kapacity.

V důsledku základním návrhu pro jeden uzel v konfiguraci horizontální navýšení kapacity bude vypadat takto:

![Základní informace o škálování na víc systémů z jediného uzlu](media/hana-vm-operations/scale-out-basics.PNG)

Základní konfigurace uzlu virtuálního počítače pro horizontální navýšení kapacity SAP HANA vypadá takto:

- Pro **/hana/sdílené**, sestavování clusteru pro systém souborů NFS podle operačního systému SUSE Linux 12 SP3 s vysokou dostupností. Tento cluster hostitelů **/hana/sdílené** sdílených složek NFS horizontální navýšení kapacity konfigurací a SAP NetWeaver nebo BW/4HANA centrální služby. Dokumentace k sestavení taková konfigurace je k dispozici v článku [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Všechny ostatní diskové svazky jsou **není** sdílet mezi různými uzly a jsou **není** založené na systému souborů NFS. Konfigurace instalace a postupy pro horizontální navýšení kapacity HANA instalace s nesdílené **/hana/data** a **/hana/log** je k dispozici níže v tomto dokumentu.

>[!NOTE]
>S vysokou dostupností clusteru systému souborů NFS se zobrazuje na obrázcích, pokud se podporuje s operačním systémem SUSE Linux pouze. Vysoce dostupné řešení systému souborů NFS založeného na Red Hat bude se vám později.

Změna velikosti svazků pro uzly s výjimkou je stejná jako vertikálně navýšit kapacitu, **/hana/sdílené**. Pro skladovou Položku virtuálního počítače M128s doporučené velikosti a typy vypadat:

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VM I/O<br /> Propustnost | / hana/dat | / hana/log | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Zkontrolujte, zda splňuje propustnost úložiště pro různé svazky doporučené úlohy, které chcete spustit. Pokud úloha vyžaduje větší svazky pro **/hana/data** a **/hana/log**, je potřeba zvýšit počet virtuálních pevných disků Storage úrovně Premium pro Azure. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýšení IOPS a propustnost vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure. Platí také akcelerátor zápisu Azure pro disky, které tvoří **/hana/log** svazku.
 
V dokumentu [požadavky na úložiště pro SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), vzorec má název, který definuje velikost **/hana/sdílené** svazek pro horizontální navýšení kapacity jako velikost paměti z jednoho pracovního uzlu na čtyři uzly pracovního procesu.

Předpokládáme, že provedete SAP HANA horizontální navýšení kapacity certifikovaných M128s virtuálního počítače Azure s přibližně 2 TB paměti a doporučení SAP lze shrnout jako:

- Jeden hlavní uzel a až čtyři pracovní uzel, **/hana/sdílené** by musí být 2 TB velikosti svazku. 
- Jeden hlavní uzel a pět a 8 pracovních uzlů, velikost **/hana/sdílené** by měl být 4 TB. 
- Jeden hlavní uzel a 9 až 12 pracovních uzlů, velikost 6 TB pro **/hana/sdílené** by bylo zapotřebí. 
- Jeden hlavní uzel a pomocí 12 až 15 pracovních uzlů, které jsou nezbytné **/hana/sdílené** svazek, který je ve velikosti 8 TB.

Další důležité návrh, který se zobrazí v grafice konfiguraci jednoho uzlu virtuálního počítače s horizontálním navýšením SAP HANA je virtuální síť nebo lépe konfiguraci podsítě. SAP důrazně doporučuje oddělit internetový provoz klienta nebo aplikace z komunikace mezi uzly HANA. Jak je znázorněno na obrázcích, tento cíl se dosahuje tím, že dvě různé karet Vnic, které jsou připojené k virtuálnímu počítači. Oba virtuální síťové adaptéry jsou v různých podsítích, mají dvou různých IP adresách. Potom řízení toku provozu pomocí pravidel směrování pomocí skupin zabezpečení sítě nebo trasy definované uživatelem.

Zejména v Azure neexistují žádné prostředky a metody k vynucení kvalitu služby a kvóty na konkrétní virtuální síťové adaptéry. V důsledku toho oddělení zákazníky i meziuzlové komunikaci klienta nebo aplikace se neotevře příležitosti k určení priority jednoho datového proudu provoz z nich. Místo toho oddělení zůstane míru zabezpečení v stínění meziuzlové komunikaci horizontální navýšení kapacity konfigurací.  

>[!IMPORTANT]
>SAP důrazně doporučuje oddělení síťového provozu na straně klienta nebo aplikace a provoz uzlů, jak je popsáno v tomto dokumentu. Doporučujeme proto uvedení architekturu na místě, jak je znázorněno v poslední grafiky.
>

Z hlediska sítě minimální požadovaná síťová architektura vypadat nějak takto:

![Základní informace o škálování na víc systémů z jediného uzlu](media/hana-vm-operations/scale-out-networking-overview.PNG)

Omezení podporována, pokud jsou další jeden hlavní uzel 15 pracovního procesu.

Z hlediska úložiště architektura úložiště vypadat nějak takto:


![Základní informace o škálování na víc systémů z jediného uzlu](media/hana-vm-operations/scale-out-storage-overview.PNG)

**/Hana/sdílené** svazek umístěn na vysoce dostupné konfiguraci sdílené složky systému souborů NFS. Zatímco všechny ostatní disky jsou: místně připojené k jednotlivým virtuálním počítačům. 

### <a name="highly-available-nfs-share"></a>Vysoce dostupné sdílené složky systému souborů NFS
S vysokou dostupností clusteru systému souborů NFS zatím pracuje s operačním systémem SUSE Linux pouze. Dokument [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) popisuje, jak ho nastavit. Pokud clusteru systému souborů NFS nesdílejte s další konfigurace HANA mimo virtuální síť azure, která se spouští instance systému SAP HANA, nainstalujte ji do stejné virtuální síti. Nainstalujte ho ve vlastní podsíti a ujistěte se, že ne všechny libovolného provozu přístup k podsíť. Místo toho byste měli omezit přenosy do této podsítě na IP adresy virtuálního počítače, spusťte provoz do **/hana/sdílené** svazku.

Související s virtuální síťový adaptér virtuálního počítače HANA horizontální navýšení kapacity, který by měl směrovat **/hana/sdílené** provoz, doporučení jsou:

- Od provozu do **/hana/sdílené** je střední, směrování, která je přiřazena k síti klienta v minimální požadavky na konfiguraci virtuálního síťového adaptéru
- Nakonec pro provoz do **/hana/sdílené**, nasaďte třetí podsíti ve virtuální síti nasadíte horizontální navýšení kapacity konfigurací SAP HANA a virtuálního síťového adaptéru přiřadit třetí, která je hostována v této podsíti. Pomocí třetího virtuálního síťového adaptéru a přidružené IP adresy pro provoz do sdílené složky systému souborů NFS. Pak můžete použít samostatné přístup a pravidla směrování.

>[!IMPORTANT]
>Síťový provoz mezi virtuální počítače, které mají SAP HANA způsobem horizontální navýšení kapacity, nasazení a systému souborů NFS s vysokou dostupností může za žádných okolností směrován přes [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/) nebo podobné virtuální zařízení. Zatímco skupiny zabezpečení sítě Azure se nevyžaduje těchto zařízení. Zkontrolujte vaše pravidla směrování, pokud chcete mít jistotu, že jsou zkrácen síťová virtuální zařízení nebo podobné virtuální zařízení při přístup k vysoce dostupné sdílené složky NFS z virtuálních počítačů spouštějí SAP HANA.
> 

Pokud chcete sdílet s vysokou dostupností clusteru systému souborů NFS mezi konfiguracemi SAP HANA, přesuňte všechny tyto konfigurace HANA do stejné virtuální síti. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalace Azure n škálování na více instancí SAP HANA
Instalace konfigurace SAP horizontální navýšení kapacity, musíte provést kroky k hrubé:

- Nasazování nových nebo úpravu nové infrastruktury virtuální sítě Azure
- Nasazení nových virtuálních počítačů pomocí Azure Storage úrovně Premium Managed svazky
- Nasazení nového nebo přizpůsobit existující cluster s vysokou dostupností systému souborů NFS
- Přizpůsobit, abyste měli jistotu, že, například meziuzlové komunikaci mezi virtuálními počítači není směrován přes směrováním v síti [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/). Totéž platí pro přenosy mezi virtuálními počítači a s vysokou dostupností clusteru systému souborů NFS.
- Instalace hlavního uzlu SAP HANA.
- Přizpůsobení parametrů konfigurace hlavního uzlu SAP HANA
- Pokračovat v instalaci pracovních uzlů SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalace SAP HANA v konfiguraci horizontální navýšení kapacity
Nasazení infrastruktury virtuálních počítačů Azure a dalším krokům hotovi, budete muset nainstalovat horizontální navýšení kapacity konfigurací SAP HANA v těchto krocích:

- Instalace SAP HANA hlavní uzel podle dokumentace společnosti SAP.
- **Po instalaci budete muset změnit global.ini soubor a přidejte parametr "basepath_shared = ne ' pro global.ini**. Tento parametr umožňuje SAP HANA po spuštění v Škálováním bez "sdílené" **/hana/data** a **/hana/log** svazky mezi uzly. Podrobnosti jsou popsány v [2080991 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2080991).
- Po změně parametr global.ini, restartuje instanci SAP HANA
- Přidáte pracovní uzly. Viz také <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Zadejte interní síť pro SAP HANA komunikace v rámci uzlu během instalace nebo později, například pomocí, místní hdblcm. Podrobnější dokumentaci najdete v článku také [2183363 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2183363). 

Následující rutina tento instalační program škálovatelných konfigurací, které jste nainstalovali bude používat-sdílené disky pro spuštění **/hana/data** a **/hana/log**. Vzhledem k tomu **/hana/sdílené** svazku se to umístit vysoce dostupných sdílených složek NFS.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA dynamické ovládání datových vrstev na 2.0 pro virtuální počítače Azure

Kromě certifikací SAP HANA na virtuálních počítačích Azure řady M-series, SAP HANA dynamické ovládání datových vrstev na 2.0 také podporuje v Microsoft Azure (viz dokumentace k SAP HANA dynamické ovládání datových vrstev na odkazy níže). Neplatí žádné rozdíly v instalaci nebo ji operační, například přes SAP HANA řídicí panel ve virtuálním počítači Azure, existuje pár důležitých položek, které jsou povinné pro oficiální podporu k Azure. Tyto klíčové body jsou popsané níže. V celém článku – zkratka "DT 2.0" bude možné použít místo celé jméno dynamické ovládání datových vrstev na 2.0.

SAP HANA dynamické ovládání datových vrstev na 2.0 nepodporuje SAP BW nebo S4HANA. Případy použití hlavní teď jsou nativní aplikace HANA.


### <a name="overview"></a>Přehled

Následující obrázek poskytuje přehled ohledně podpory DT 2.0 v Microsoft Azure. Existuje sada povinné požadavky, který má být dodržována dodržovat oficiální certifikaci:

- DT 2.0 musí být nainstalován na vyhrazených virtuálních počítačích Azure. Nelze spustit na jednom virtuálním počítači, kde běží SAP HANA
- SAP HANA a virtuální počítače DT 2.0 musí být nasazeny v rámci stejné virtuální síti Azure
- SAP HANA a virtuální počítače DT 2.0 musí být nasazeny s povolenými akcelerovanými síťovými službami Azure
- Typ úložiště pro virtuální počítače DT 2.0 musí být Azure Storage úrovně Premium
- Více disků v Azure musí být připojené k virtuálnímu počítači DT 2.0
- Je potřeba vytvořit raid softwaru / prokládaných svazků (buď prostřednictvím lvm nebo mdadm) použitím prokládáním pro disky Azure

Další podrobnosti se chystáte vysvětlené v následujících částech.

![Přehled 2.0 architektury DT SAP HANA](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Vyhrazený virtuální počítač Azure pro SAP HANA DT 2.0

Na Azure IaaS DT 2.0 je podporována pouze na vyhrazený virtuální počítač. Není povoleno spustit DT 2.0 na stejném virtuálním počítači Azure se spuštěným systémem HANA instance. Zpočátku dva typy virtuálních počítačů lze použít ke spuštění SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Zobrazit popis typu virtuálního počítače [zde](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Základní myšlenka DT 2.0, což je o snižování zátěže "teplé" data, aby bylo možné ušetřit uveden je vhodné použít odpovídající velikosti virtuálních počítačů. I když týkající se možných kombinací neexistuje žádné striktní pravidlo. To závisí na pracovním vytížení konkrétního zákazníka.

Doporučená konfigurace by byl:

| Typ virtuálního počítače SAP HANA | Typ virtuálního počítače DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Všechny kombinace s certifikací SAP HANA virtuální počítače řady M-series s podporovaných DT 2.0 virtuálních počítačů (M64 32ms a E32sv3) jsou možné.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Sítě Azure a SAP HANA DT 2.0

Propustnost sítě mezi tímto DT 2.0 a SAP HANA VM z 10 Gb minimální instalaci DT 2.0 na vyhrazený virtuální počítač vyžaduje. Proto je nutné umístit všechny virtuální počítače ve stejné virtuální síti Azure a povolit akcelerované síťové služby Azure.

Zobrazit další informace o akcelerovaných síťových služeb Azure [zde](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Úložiště virtuálního počítače pro SAP HANA DT 2.0

Podle DT 2.0 nejlepších praktik propustnost vstupně-výstupních operací disku by měl být minimální 50 MB/s každé fyzické jádro. Prohlížení specifikace pro dva typy virtuálních počítačů Azure, které jsou podporovány pro DT 2.0 disku maximální limit propustnosti vstupně-výstupních operací pro vzhled virtuálního počítače, jako jsou:

- E32sv3    :   768 MB/s (bez mezipaměti) to znamená, že poměr 48 MB za sekundu podle počtu fyzických jader
- M64-32ms  :  1 000 MB za sekundu (bez mezipaměti) to znamená, že poměr 62,5 MB za sekundu podle počtu fyzických jader

Je potřeba připojit k virtuálnímu počítači 2.0 DT více disků v Azure a vytvořit software raid (prokládáním) na úrovni operačního systému k dosažení maximálního limitu propustnost disků jednotlivých virtuálních počítačů. Jeden disk Azure nemůže poskytnout propustnost pro dosažení maximálního limitu virtuálních počítačů v této souvislosti. Azure Premium storage je povinné pro spuštění DT 2.0. 

- Najdete podrobnosti o typech disků k dispozici Azure [zde](../../windows/disks-types.md)
- Najdete podrobnosti o vytváření softwaru diskového pole raid prostřednictvím mdadm [zde](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Podrobnosti o konfiguraci LVM vytvořit prokládané svazku pro maximální propustnosti můžete najít [zde](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

V závislosti na požadavcích velikost existují různé možnosti pro dosažení maximální propustnosti virtuálního počítače. Tady je konfigurace disků objem dat pro každý typ virtuálního počítače DT 2.0 k dosažení horní limit propustnosti virtuálního počítače. Virtuální počítač E32sv3 by měly být považovány za úroveň položka pro úlohy menší. V případě, že ji by měl ukázat, že není rychlé dostatečně může být potřeba změnit velikost virtuálního počítače do M64 32ms.
Jako virtuální počítač M64 32ms má velikost paměti, zatížení vstupně-výstupní operace nemusí dosáhnout omezení zejména pro úlohy náročné na čtení. Proto může být méně disky v sadě stripe dostatečná v závislosti na konkrétních úloh zákazníka. Ale bude v bezpečí disku se rozhodli zajistit maximální propustnost následujících konfigurací:


| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Konfigurace disku 1 | Konfigurace disku 2 | Konfigurace disku 3 | Konfigurace disku 4 | Konfigurace disku 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Zejména v případě zatížení intenzivní čtení může zvýšit vstupně-výstupním výkonem k zapnutí nastavení v mezipaměti pro Azure hostitele "jen pro čtení" podle doporučení pro datové svazky databázový software. Zatímco transakce protokolu mezipaměti disku Azure hostitele musí být "none". 

Doporučené výchozí bod týkající se velikost svazku protokolu je Heuristika 15 % velikosti dat. Vytvoření svazku protokolu lze provést pomocí typů jiný disk Azure v závislosti na požadavcích náklady a propustnost. U protokolu se vyžaduje Vysoká propustnost vstupně-výstupních operací svazku.  V případě použití virtuálního počítače zadejte M64 32ms, důrazně doporučujeme povolit [akcelerátorem zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Azure Write Accelerator poskytuje latence zápisu u optimální disku pro protokol transakcí (dostupné pouze pro řadu M-series). Existují některé položky, které byste měli zvážit, když jako maximální počet disků podle typu virtuálního počítače. Najdete podrobnosti o akcelerátorem zápisu [zde](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Tady je pár příkladů o určení velikosti svazku protokolu:

| Zadejte velikost svazku dat a disku | svazek s protokolem a disk zadejte konfigurace 1 | svazek s protokolem a disk zadejte config 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Například pro SAP HANA horizontální navýšení kapacity, musí /hana/shared directory sdílet mezi SAP HANA a virtuálním Počítačem 2.0 DT. Stejnou architekturu jako SAP HANA horizontální navýšení kapacity pomocí vyhrazené virtuální počítače, která působí jako vysoce dostupný server pro systém souborů NFS se doporučuje. Negace sdílené záložní svazek je možné identické návrhu. Ale až zákazník Pokud by bylo nutné vysokou dostupnost, nebo pokud stačí jenom použít vyhrazený virtuální počítač s dostatečnou kapacitou úložiště tak, aby fungoval jako záložní server.



### <a name="links-to-dt-20-documentation"></a>Odkazy na dokumentaci DT 2.0 

- [SAP HANA dynamické ovládání datových vrstev na instalaci a aktualizaci Průvodce](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA dynamické ovládání datových vrstev na kurzy a materiály](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dynamické vrstvení PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 aktualizace Service PACKU 02 dynamické vrstvení vylepšení](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operace pro nasazení SAP HANA na virtuálních počítačích Azure
Následující části popisují některé operace související s nasazováním systémů SAP HANA na virtuálních počítačích Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Zálohování a obnovení operací na virtuálních počítačích Azure
Tyto dokumenty popisují, jak zálohovat a obnovit vaše nasazení SAP HANA:

- [Přehled zálohování SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Zálohování na úrovni souboru SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Srovnávací test snímků úložiště SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Spuštění a restartování virtuálních počítačů, které obsahují SAP HANA
Viditelného funkcí služby veřejného cloudu Azure je, že vám budeme účtovat jenom výpočetní minut. Například při vypnutí virtuálního počítače, na kterém běží SAP HANA, bude se vám účtovat jenom za náklady na úložiště během této doby. Další funkcí je k dispozici, když zadat statické IP adresy pro virtuální počítače v původním nasazení. Po restartování virtuálního počítače, který má SAP HANA, virtuální počítač se restartuje s jeho předchozí IP adresy. 


### <a name="use-saprouter-for-sap-remote-support"></a>Vzdálená podpora SAP pomocí SAProuter
Pokud máte připojení site-to-site mezi místními umístěními a Azure a komponenty SAP spouštíte, máte pravděpodobně již spuštěnou SAProuter. V takovém případě dokončete následující položky pro podporu vzdáleného:

- Udržujte privátní a statickou IP adresu virtuálního počítače, který je hostitelem SAP HANA v konfiguraci SAProuter.
- Nakonfigurujte skupiny zabezpečení sítě, který je hostitelem virtuálního počítače HANA povolený provoz přes TCP/IP port 3299 podsítě.

Pokud se připojujete k Azure prostřednictvím Internetu, a není nutné směrovač SAP pro virtuální počítač se SAP HANA, musíte nainstalovat součást. Nainstalujte SAProuter v samostatných virtuálních počítačů v podsíti správy. Hrubý schéma pro nasazení SAP HANA, bez připojení site-to-site a s SAProuter naleznete na následujícím obrázku:

![Bez připojení site-to-site a SAProuter surové nasazení schématu pro SAP HANA](media/hana-vm-operations/hana-simple-networking3.PNG)

Nezapomeňte nainstalovat SAProuter na samostatném virtuálním počítači a ne ve virtuálním počítači Jumpboxu. Samostatný virtuální počítač musí mít statickou IP adresu. Pro připojení vašich SAProuter k SAProuter, který je hostitelem SAP, obraťte se na SAP pro IP adresu. (SAProuter, který je hostitelem SAP je protějškem SAProuter instance, který nainstalujete na virtuální počítač.) Umožňuje nakonfigurovat instanci SAProuter adresu IP ze SAP. V nastavení konfigurace pouze nezbytné port je TCP port 3299.

Další informace o tom, jak nastavit a spravovat připojení vzdálenou podporu prostřednictvím SAProuter najdete v tématu [dokumentaci k SAPU](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Vysoká dostupnost se SAP HANA na nativních virtuálních počítačích Azure
Pokud používáte systém SUSE Linux Enterprise Server pro SAP aplikace 12 SP1 nebo novější, můžete vytvořit Pacemaker cluster s využitím techniky STONITH zařízení. Zařízení můžete použít k nastavení konfigurace služby SAP HANA, která používá synchronní replikace s systémové replikace HANA a automatické převzetí služeb při selhání. Další informace o postupu instalace najdete v tématu [Příručka pro vysokou dostupnost SAP HANA pro Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
