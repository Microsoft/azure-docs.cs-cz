---
title: Konfigurace a operace SAP HANA infrastruktury v Azure | Microsoft Docs
description: Provozní příručka pro SAP HANA systémy, které jsou nasazené na virtuálních počítačích Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5438132f32117e0ec48a6f985c3b9d2045a9da2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88649682"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfigurace infrastruktury SAP HANA a operace v Azure
Tento dokument poskytuje pokyny pro konfiguraci infrastruktury Azure a operačních SAP HANA systémů, které jsou nasazené na nativních virtuálních počítačích Azure (VM). Dokument obsahuje také informace o konfiguraci pro SAP HANA škálování pro SKU virtuálního počítače M128s. Tento dokument nemá za cíl nahradit standardní dokumentaci SAP, která zahrnuje následující obsah:

- [Průvodce správou SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Instalační příručky SAP](https://service.sap.com/instguides)
- [Poznámky SAP](https://service.sap.com/notes)

## <a name="prerequisites"></a>Požadavky
K použití tohoto průvodce potřebujete základní znalosti následujících součástí Azure:

- [Virtuální počítače Azure](../../linux/tutorial-manage-vm.md)
- [Sítě a virtuální sítě Azure](../../linux/tutorial-virtual-network.md)
- [Azure Storage](../../linux/tutorial-manage-disks.md)

Další informace o SAP NetWeaver a dalších součástech SAP v Azure najdete v části věnované [SAP v Azure](./get-started.md) v [dokumentaci k Azure](../../../index.yml).

## <a name="basic-setup-considerations"></a>Základní požadavky na instalaci
Následující části popisují základní pokyny k instalaci pro nasazení SAP HANA systémů na virtuálních počítačích Azure.

### <a name="connect-into-azure-virtual-machines"></a>Připojení k virtuálním počítačům Azure
Jak je popsáno v [příručce pro plánování virtuálních počítačů Azure](./planning-guide.md), existují dvě základní metody pro připojení k virtuálním počítačům Azure:

- Připojte se přes Internet a veřejné koncové body na virtuálním počítači skoku nebo na VIRTUÁLNÍm počítači, na kterém běží SAP HANA.
- Připojte se přes [síť VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) nebo Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Připojení typu Site-to-site prostřednictvím sítě VPN nebo ExpressRoute je nezbytné pro produkční scénáře. Tento typ připojení je taky potřeba pro neprodukční scénáře, které se dodávají do produkčních scénářů, ve kterých se používá software SAP. Následující obrázek ukazuje příklad připojení mezi lokalitami:

![Připojení mezi weby](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Výběr typů virtuálních počítačů Azure
Typy virtuálních počítačů Azure, které se dají použít v produkčních scénářích, najdete v [dokumentaci SAP pro IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). V případě neprodukčních scénářů je k dispozici širší spektrum nativních typů virtuálních počítačů Azure.

>[!NOTE]
> V případě neprodukčních scénářů použijte typy virtuálních počítačů, které jsou uvedené v [#1928533 Poznámka pro SAP](https://launchpad.support.sap.com/#/notes/1928533). Pokud chcete využívat virtuální počítače Azure v produkčních scénářích, vyhledejte v [seznamu IaaSch certifikovaných platforem SAP seznam](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)certifikovaných virtuálních počítačů s SAP HANA.

Virtuální počítače v Azure nasadíte pomocí:

- Azure Portal.
- Azure PowerShell rutiny.
- Rozhraní příkazového řádku Azure

Můžete také nasadit kompletní nainstalovanou SAP HANA platformu na Azure VM Services přes [cloudovou platformu SAP](https://cal.sap.com/). Proces instalace je popsaný v tématu [nasazení SAP S/4HANA nebo černobíle/4HANA v Azure](./cal-s4h.md) nebo pomocí služby Automation vydanou [zde](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

>[!IMPORTANT]
> Aby bylo možné použít M208xx_v2 virtuálních počítačů, je třeba pečlivě vybrat image pro Linux z Galerie imagí virtuálních počítačů Azure. Pokud si chcete přečíst podrobnosti, přečtěte si článek [paměťově optimalizované velikosti virtuálních počítačů](../../mv2-series.md).
> 


### <a name="storage-configuration-for-sap-hana"></a>Konfigurace úložiště pro SAP HANA
V případě konfigurací úložiště a typů úložiště, které se mají používat s SAP HANA v Azure, si přečtěte dokument [SAP HANA konfigurace úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) .


### <a name="set-up-azure-virtual-networks"></a>Nastavení virtuálních sítí Azure
Když máte připojení typu Site-to-site k Azure prostřednictvím sítě VPN nebo ExpressRoute, musíte mít aspoň jednu virtuální síť Azure, která je připojená přes virtuální bránu k okruhu sítě VPN nebo ExpressRoute. V jednoduchých nasazeních se virtuální brána dá nasadit v podsíti Azure Virtual Network (VNet), která je také hostitelem instancí SAP HANA. Chcete-li nainstalovat SAP HANA, vytvořte dvě další podsítě v rámci služby Azure Virtual Network. Jedna podsíť hostuje virtuální počítače za účelem spuštění instancí SAP HANA. Druhá podsíť spustí virtuální počítače JumpBox nebo Management pro hostování SAP HANA studia, jiného softwaru pro správu nebo softwaru aplikace.

> [!IMPORTANT]
> Mimo funkčnost, ale z důvodů výkonu je důležitější, není podporována konfigurace [síťových virtuálních zařízení Azure](https://azure.microsoft.com/solutions/network-appliances/) v komunikačních cestách mezi aplikací SAP a vrstvou DBMS systému SAP NetWeaver, Hybris nebo S/4HANA založeného na protokolu SAP. Komunikace mezi aplikační vrstvou SAP a vrstvou DBMS musí být ta přímá. Omezení nezahrnuje [pravidla Azure ASG a NSG](../../../virtual-network/security-overview.md) , pokud pravidla ASG a NSG umožňují přímou komunikaci. Další scénáře, kdy síťová virtuální zařízení nejsou podporované, jsou v komunikačních cestách mezi virtuálními počítači Azure, které představují uzly clusteru Linux Pacemaker a SBD zařízení, jak je popsáno v tématu [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v SUSE Linux Enterprise Server pro aplikace SAP](./high-availability-guide-suse.md). Nebo v komunikačních cestách mezi virtuálními počítači Azure a Windows serverem SOFS nastavenými v tématu vytvoření [instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdílené složky v Azure](./sap-high-availability-guide-wsfc-file-share.md). Síťová virtuální zařízení v komunikačních cestách může snadno zdvojnásobit latenci sítě mezi dvěma komunikačními partnery, může omezit propustnost v kritických cestách mezi aplikační vrstvou SAP a vrstvou DBMS. V některých scénářích vydaných zákazníky může síťová virtuální zařízení způsobit selhání clusterů Pacemaker Linux v případech, kdy komunikace mezi uzly clusteru se systémem Linux Pacemaker musí komunikovat se svým zařízením SBD prostřednictvím síťové virtuální zařízení.  
> 

> [!IMPORTANT]
> Jiný návrh, který **není podporován,** je oddělení aplikační vrstvy SAP a vrstvy DBMS do různých virtuálních sítí Azure, které nejsou navzájem [partnerského vztahu](../../../virtual-network/virtual-network-peering-overview.md) mezi sebou. Doporučuje se oddělit vrstvu aplikace SAP a DBMS pomocí podsítí v rámci služby Azure Virtual Network místo používání různých virtuálních sítí Azure. Pokud se rozhodnete nepostupovat podle doporučení a místo toho tyto dvě vrstvy oddělit do jiné virtuální sítě, musí být tyto dvě virtuální sítě [partnerské](../../../virtual-network/virtual-network-peering-overview.md). Uvědomte si, že síťový provoz mezi dvěma [partnerskými](../../../virtual-network/virtual-network-peering-overview.md) virtuálními sítěmi Azure podléhá nákladům na přenos. Velký objem dat v mnoha terabajtech vyměňovaných mezi aplikační vrstvou SAP a systémy DBMS může být shromážděn, pokud je vrstva aplikace SAP a vrstva DBMS oddělená mezi dvěma partnerskými virtuálními sítěmi Azure. 

Při instalaci virtuálních počítačů pro spuštění SAP HANA musí virtuální počítače:

- Nainstalované dvě virtuální síťové karty: jednu síťovou kartu pro připojení k podsíti pro správu a jednu síťovou kartu pro připojení k místní síti nebo jiným sítím k instanci SAP HANA ve virtuálním počítači Azure.
- Statické privátní IP adresy, které jsou nasazené pro virtuální síťové karty.

> [!NOTE]
> Statické IP adresy byste měli přiřadit prostřednictvím Azure do jednotlivých virtuální síťové adaptéry. V rámci hostovaného operačního systému do vNIC byste neměli přiřazování statických IP adres. Některé služby Azure, jako je například služba Azure Backup, spoléhají na skutečnost, že minimálně primární vNIC je nastavená na DHCP, a ne na statické IP adresy. Viz také dokument [řešení potíží se zálohováním virtuálních počítačů Azure](../../../backup/backup-azure-vms-troubleshoot.md#networking). Pokud potřebujete přiřadit více statických IP adres k virtuálnímu počítači, je třeba přiřadit více virtuální síťové adaptéry k virtuálnímu počítači.
>
>

Pro nasazení, která jsou dlouhodobá, je ale potřeba vytvořit v Azure architekturu sítě Virtual datacentra. Tato architektura doporučuje oddělení virtuální sítě Azure, které se připojuje k místní síti Azure, do samostatné virtuální sítě Azure. Tato samostatná virtuální síť by měla hostovat veškerý provoz, který ponechá buď místně, nebo na Internet. Tento přístup umožňuje nasadit software pro auditování a protokolování provozu, který vstoupí do virtuálního datového centra v Azure v této samostatné virtuální síti centra. Takže máte jednu virtuální síť, která je hostitelem veškerého softwaru a konfigurací, které se vztahují k místnímu a odchozímu provozu do nasazení Azure.

Články [Azure Virtual Datacenter: Perspektiva sítě](/azure/architecture/vdc/networking-virtual-datacenter) a [virtuální datové centrum Azure a rovina řízení podniku](/azure/architecture/vdc/) poskytují více informací o přístupu k virtuálnímu datovému centru a souvisejícím návrhu virtuální sítě Azure.


>[!NOTE]
>Přenosy dat mezi virtuální sítí a koncovou sítí rozbočovače s využitím [partnerského vztahu](../../../virtual-network/virtual-network-peering-overview.md) virtuálních sítí Azure se vztahují na další [náklady](https://azure.microsoft.com/pricing/details/virtual-network/). Na základě těchto nákladů možná budete muset vzít v úvahu, že byste měli zvážit kompromisy mezi spuštěním striktního síťového návrhu centra a paprsku a spuštěním několika [bran Azure ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md) , které se připojíte k paprskům, aby se vynechal partnerský vztah virtuálních sítí. Brány Azure ExpressRoute ale také zavádí další [náklady](https://azure.microsoft.com/pricing/details/vpn-gateway/) . Také se můžete setkat s dalšími náklady na software třetích stran, který používáte pro protokolování, auditování a monitorování síťových přenosů. V závislosti na nákladech na výměnu dat prostřednictvím partnerského vztahu virtuálních sítí na jedné straně a na náklady, které vytváří další brány Azure ExpressRoute a další licence softwaru, se můžete rozhodnout pro mikrosegmentaci v rámci jedné virtuální sítě pomocí podsítí jako izolační jednotky namísto virtuální sítě.


Přehled různých metod pro přiřazování IP adres najdete v tématu [typy IP adres a metody přidělování v Azure](../../../virtual-network/public-ip-addresses.md). 

Pro virtuální počítače se systémem SAP HANA byste měli pracovat se přiřazenými statickými IP adresami. Důvodem je, že některé atributy konfigurace odkazují na IP adresy pro HANA.

[Skupiny zabezpečení sítě Azure (skupin zabezpečení sítě)](../../../virtual-network/virtual-network-vnet-plan-design-arm.md) se používají k směrování provozu, který je směrován na instanci SAP HANA nebo na JumpBox. [Skupiny zabezpečení aplikace](../../../virtual-network/security-overview.md#application-security-groups) skupin zabezpečení sítě a nakonec jsou přidruženy k SAP HANA podsíti a podsíti pro správu.

Následující obrázek ukazuje přehled přibližného schématu nasazení pro SAP HANA po architektuře virtuální sítě rozbočovače a paprsků:

![Schéma přibližného nasazení pro SAP HANA](media/hana-vm-operations/hana-simple-networking-dmz.png)

Pokud chcete nasadit SAP HANA v Azure bez připojení typu Site-to-site, budete chtít i nadále chránit instanci SAP HANA z veřejného Internetu a skrýt ji za předávacím proxy serverem. V tomto základním scénáři nasazení při překladu názvů hostitelů spoléhá na integrované služby DNS Azure. Ve složitějším nasazení, kde se používají veřejné IP adresy, jsou integrované služby DNS pro Azure obzvláště důležité. Využijte Azure skupin zabezpečení sítě a [Azure síťová virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/) k řízení a monitorování směrování z Internetu do vaší architektury virtuální sítě Azure v Azure. Následující obrázek ukazuje přibližné schéma pro nasazení SAP HANA bez připojení typu Site-to-site v architektuře virtuální sítě typu hub a paprsek:
  
![Hrubá schéma nasazení pro SAP HANA bez připojení typu Site-to-site](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

Další popis způsobu použití Azure síťová virtuální zařízení k řízení a monitorování přístupu z Internetu bez centrální architektury sítě a paprsků najdete v článku [nasazení vysoce dostupných síťových virtuálních zařízení](/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurace infrastruktury Azure pro SAP HANA škálování na více instancí
Pokud chcete zjistit typy virtuálních počítačů Azure, které jsou certifikované pro škálování na více instancí OLAP nebo S/4HANA, podívejte se do [adresáře SAP HANA hardwaru](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Zaškrtnutí ve sloupci clusteringu označuje podporu škálování na více instancí. Typ aplikace označuje, zda je podporováno škálování na více instancí OLAP nebo S/4HANA. Podrobnosti o uzlech, které jsou certifikovány pro jednotlivé virtuální počítače na úrovni horizontálního škálování, najdete v podrobnostech o položkách v konkrétní SKU virtuálního počítače, které jsou uvedené v adresáři SAP HANA hardwaru.

Minimální verze operačního systému pro nasazení konfigurací škálování na více systémů ve virtuálních počítačích Azure najdete v podrobnostech o položkách v konkrétní SKU virtuálního počítače, které jsou uvedené v adresáři SAP HANA hardwaru. Z n-Node konfigurace škálování na více uzlů – jeden uzel funguje jako hlavní uzel. Ostatní uzly až do limitu certifikace jako pracovní uzel. Další pohotovostní uzly se nepočítají do počtu certifikovaných uzlů.

>[!NOTE]
> Nasazení SAP HANA se škálováním na více virtuálních počítačů v Azure s pohotovostním uzlem je možné pouze pomocí úložiště [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) . Žádné jiné SAP HANA certifikované úložiště Azure neumožňuje konfiguraci SAP HANAch pohotovostních uzlů.
>

Pro/Hana/Shared doporučujeme také použití [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). 

Typický základní návrh pro jeden uzel v konfiguraci se škálováním na více instancí bude vypadat takto:

![Základní informace o škálování jednoho uzlu](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

Základní konfigurace uzlu virtuálního počítače pro SAP HANA škálování na více instancí vypadá takto:

- Pro **/Hana/Shared**používáte NATIVNÍ službu NFS, která je poskytována prostřednictvím Azure NetApp Files. 
- Všechny ostatní svazky disku nejsou sdíleny mezi různými uzly a nejsou založené na systému souborů NFS. Konfigurace a kroky instalace pro škálování na více systémů s nesdílenými **/Hana/data** a **/Hana/log** se poskytují dál v tomto dokumentu. Pro certifikované úložiště HANA, které se dá použít, se podívejte na článek [SAP HANA konfigurace úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md).


Velikost svazků nebo disků je nutné ověřit v dokumentu [SAP HANA požadavky na úložiště TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), aby byla požadovaná velikost závislá na počtu pracovních uzlů. Dokument uvolní vzorec, který potřebujete použít k získání požadované kapacity svazku.

Další kritéria návrhu, která se zobrazují v obrázcích konfigurace s jedním uzlem pro SAP HANA virtuální počítač se škálováním na více instancí, je virtuální síť nebo lepší konfigurace podsítě. SAP důrazně doporučuje oddělení provozu na straně klienta nebo aplikace od komunikací mezi uzly HANA. Jak je znázorněno na obrázcích, tento cíl se dosahuje tak, že se k virtuálnímu počítači připojí dva různé virtuální síťové adaptéry. Obě virtuální síťové adaptéry jsou v různých podsítích, mají dvě různé IP adresy. Pak budete řídit tok přenosů s pravidly směrování pomocí skupin zabezpečení sítě nebo uživatelsky definovaných tras.

Zejména v Azure neexistují žádné prostředky a metody pro vymáhání kvality služeb a kvót na konkrétních virtuální síťové adaptéry. V důsledku toho oddělení komunikace mezi klientem a aplikací a komunikací uvnitř uzlu neotevře žádné příležitosti k určení priorit jednoho datového proudu v rámci druhé. Místo toho oddělení zachová míru zabezpečení při stínění komunikace mezi uzly a konfigurací škálování na více systémů.  

>[!NOTE]
>SAP doporučuje oddělit síťový provoz na stranu klienta/aplikace a provoz uvnitř uzlu, jak je popsáno v tomto dokumentu. Proto je doporučeno umístit architekturu, jak je znázorněno na poslední grafice. Také se zeptejte týmu zabezpečení a dodržování předpisů na požadavky, které se odchylují od doporučení. 
>

Minimální požadovaná síťová architektura v síťovém bodě zobrazení by vypadala takto:

![Základní informace o škálování jednoho uzlu](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalace SAP HANA škálování na více instancí n Azure
Instalace konfigurace SAP se škálováním na více instancí vyžaduje provedení přibližných kroků:

- Nasazení nové nebo přizpůsobování existující infrastruktury virtuální sítě Azure
- Nasazení nových virtuálních počítačů pomocí spravovaných Premium Storage Azure, disků Ultra diskových svazků nebo svazků NFS na bázi ANF
- - Přizpůsobte směrování sítě, abyste se ujistili, že například komunikace mezi uzly mezi virtuálními počítači není směrována přes [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/). 
- Nainstalujte SAP HANA hlavní uzel.
- Přizpůsobení konfiguračních parametrů SAP HANAho hlavního uzlu
- Pokračování v instalaci SAP HANA pracovních uzlů

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalace SAP HANA v konfiguraci s možností horizontálního rozšíření kapacity
Vzhledem k tomu, že je nasazená Infrastruktura virtuálních počítačů Azure a všechny další přípravy, je nutné nainstalovat SAP HANA konfigurací škálování na více instancí v těchto krocích:

- Instalace uzlu SAP HANA Master podle dokumentace SAP
- V případě použití Azure Premium Storage nebo Ultra diskového úložiště s nesdílenými disky/Hana/data a/Hana/log je třeba změnit soubor global.ini a přidat parametr ' basepath_shared = No ' do souboru global.ini. Tento parametr umožňuje, aby SAP HANA běžely s možností horizontálního navýšení kapacity bez "sdílených" **/Hana/data** a **/Hana/log** svazků mezi uzly. Podrobnosti jsou popsány v dokumentaci [SAP #2080991](https://launchpad.support.sap.com/#/notes/2080991). Pokud používáte svazky NFS založené na ANF pro/Hana/data a/Hana/log, nemusíte tuto změnu dělat.
- Po případné změně parametru global.ini Restartujte instanci SAP HANA.
- Přidejte další pracovní uzly. Viz také <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html> . Určete interní síť pro SAP HANA komunikaci mezi uzly během instalace, nebo pak použijte například místní hdblcm. Podrobnější dokumentaci najdete v tématu také o [#2183363 SAP Note](https://launchpad.support.sap.com/#/notes/2183363). 

Podrobné informace o nastavení SAP HANA systému škálování na více systémů s pohotovostním uzlem na SUSE Linux je podrobněji popsán v tématu [nasazení SAP HANA systému škálování na více systémů s pohotovostním uzlem na virtuálních počítačích Azure pomocí Azure NetApp Files na SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md). Ekvivalentní dokumentaci pro Red Hat najdete v článku [nasazení SAP HANA systému se škálováním na více systémů s pohotovostním uzlem na virtuálních počítačích Azure pomocí Azure NetApp Files na Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md). 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA dynamické vrstvy 2,0 pro virtuální počítače Azure

Kromě SAP HANA certifikace na virtuálních počítačích Azure M-Series je podpora SAP HANA dynamické vrstvy 2,0 taky podporovaná na Microsoft Azure (Další informace najdete v tématu odkazy na dokumentaci SAP HANA dynamického vrstvení). I když se nejedná o rozdíl v instalaci produktu nebo jeho provozu, například prostřednictvím řídicího panelu SAP HANA v rámci virtuálního počítače Azure, je k dispozici několik důležitých položek, které jsou pro oficiální podporu v Azure povinné. Tyto klíčové body jsou popsány níže. V celém článku se místo dynamického naplnění vrstvy 2,0 na celé jméno použije zkratka "DT 2,0".

SAP HANA dynamické vrstvení 2,0 není podporován SAP BW nebo S4HANA. Hlavní případy použití: teď jsou nativní aplikace HANA.


### <a name="overview"></a>Přehled

Následující obrázek obsahuje přehled týkající se podpory DT 2,0 na Microsoft Azure. Existuje sada povinných požadavků, které je třeba dodržovat, aby byly dodrženy oficiální certifikace:

- Na vyhrazeném virtuálním počítači Azure musí být nainstalováno DT 2,0. Nemusí běžet na stejném virtuálním počítači, kde SAP HANA běžet.
- Virtuální počítače SAP HANA a DT 2,0 musí být nasazeny v rámci stejné virtuální sítě Azure.
- Virtuální počítače SAP HANA a DT 2,0 musí být nasazeny s povolenými akcelerovanými síťovými službami Azure.
- Typ úložiště pro virtuální počítače s DT 2,0 musí být Azure Premium Storage
- K virtuálnímu počítači DT 2,0 musí být připojené víc disků Azure.
- Při vytváření svazků na discích Azure je potřeba vytvořit softwarový RAID/prokládaný svazek (ať už přes LVM nebo mdadm).

Další podrobnosti se budou vysvětlit v následujících oddílech.

![Přehled architektury SAP HANA DT 2,0](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Vyhrazený virtuální počítač Azure pro SAP HANA DT 2,0

V Azure IaaS je DT 2,0 podporován pouze na vyhrazeném virtuálním počítači. Na stejném virtuálním počítači Azure, ve kterém je spuštěná instance HANA, není povoleno spouštět DT 2,0. Ke spuštění SAP HANA DT 2,0 lze použít počáteční dva typy virtuálních počítačů:

- M64-32ms 
- E32sv3 

[Tady](../../sizes-memory.md) najdete popis typu virtuálního počítače.

Vzhledem k základní myšlence DT 2,0, která má za následek přesměrování "teplé" dat, aby se ušetřily náklady, které mají smysl používat odpovídající velikosti virtuálních počítačů. V souvislosti s možnými kombinacemi neexistuje žádné striktní pravidlo. Závisí na konkrétním zatížení zákazníka.

Doporučené konfigurace by byly:

| Typ virtuálního počítače SAP HANA | Typ virtuálního počítače DT 2,0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


K dispozici jsou všechny kombinace SAP HANAch virtuálních počítačů řady M-Series s podporovanými virtuálními počítači typu 2,0 (M64-32ms a E32sv3).


### <a name="azure-networking-and-sap-hana-dt-20"></a>Sítě Azure a SAP HANA DT 2,0

Instalace DT 2,0 na vyhrazeném virtuálním počítači vyžaduje propustnost sítě mezi virtuálním počítačem DT 2,0 a minimálním SAP HANAm virtuálního počítače o velikosti 10 GB. Proto je nutné umístit všechny virtuální počítače ve stejné virtuální síti Azure a povolit urychlené síťové služby Azure.

[Tady](../../../virtual-network/create-vm-accelerated-networking-cli.md) najdete další informace o akcelerovaných sítích Azure.

### <a name="vm-storage-for-sap-hana-dt-20"></a>Úložiště virtuálního počítače pro SAP HANA DT 2,0

V souladu s pokyny 2,0 pro osvědčené postupy v/v platí, že propustnost vstupně-výstupních operací disku by měla být minimálně 50 MB/s na fyzickou jader. Podívejte se na specifikaci pro dva typy virtuálních počítačů Azure, které jsou podporované pro DT 2,0. maximální propustnost vstupně-výstupních operací disku pro virtuální počítač vypadá takto:

- E32sv3:768 MB/s (neuložené do mezipaměti) znamená poměr 48 MB/s na fyzický jádro
- M64-32ms: 1000 MB/s (neuložené do mezipaměti) znamená poměr 62,5 MB/s na fyzický jádro

Aby bylo dosaženo maximálního počtu propustnosti disku na jeden virtuální počítač, je nutné připojit více disků Azure k virtuálnímu počítači DT 2,0 a vytvořit softwarové pole RAID (prokládání) na úrovni operačního systému. Jeden disk Azure nemůže poskytnout propustnost pro dosažení maximálního limitu virtuálních počítačů v tomto ohledu. Služba Azure Premium Storage je povinná pro spuštění DT 2,0. 

- Podrobnosti o dostupných typech disků Azure najdete [tady](../../disks-types.md) .
- Podrobnosti o vytváření RAID softwaru prostřednictvím mdadm najdete [tady](../../linux/configure-raid.md) .
- Podrobnosti o konfiguraci LVM pro vytvoření prokládaného svazku pro maximální propustnost najdete [tady](../../linux/configure-lvm.md) .

V závislosti na požadavcích na velikost jsou k dispozici různé možnosti pro dosažení maximální propustnosti virtuálního počítače. Tady jsou možná konfigurace disků datových svazků pro každý typ virtuálního počítače DT 2,0, aby se dosáhlo horního limitu propustnosti virtuálních počítačů. Virtuální počítač E32sv3 by se měl považovat za úroveň vstupu pro menší úlohy. V případě, že by se neměl dostatečně rychle vypínat, může být nutné změnit velikost virtuálního počítače na M64-32ms.
V případě, že virtuální počítač M64-32ms má mnoho paměti, zatížení v/v nemusí dosahovat limitu, obzvláště pro úlohy náročné na čtení. Proto může být méně disků v sadě Stripe dostačující v závislosti na úlohách specifických pro zákazníka. Ale na bezpečné straně jsou zvolené konfigurace disku, aby se zaručila maximální propustnost:


| Skladová položka virtuálního počítače | Konfigurace disku 1 | Konfigurace disku 2 | Konfigurace disku 3 | Konfigurace disku 4 | Konfigurace disku 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50-> 16 TB | 4 x P40-> 8 TB | 5 x P30-> 5 TB | 7 x P20-> 3,5 TB | 8 x P15-> 2 TB | 
| E32sv3 | 3 x P50-> 12 TB | 3 x P40-> 6 TB | 4 x P30-> 4 TB | 5 x P20-> 2,5 TB | 6 x P15-> 1,5 TB | 


Zvláště pro případ, že je zatížení náročné na čtení, může zvýšit výkon v/v, aby se zapnula mezipaměť hostitele Azure jen pro čtení, jak se doporučuje pro datové svazky databázového softwaru. Vzhledem k tomu, že mezipaměť disku hostitele Azure musí být v transakčním protokolu "none". 

V souvislosti s velikostí svazku protokolu doporučeným výchozím bodem je heuristika o 15% velikosti dat. Vytvoření svazku protokolu se dá provést pomocí různých typů disků Azure v závislosti na nákladech a propustnosti. Pro svazek protokolu je potřeba vysoká propustnost vstupně-výstupních operací.  V případě použití typu virtuálního počítače M64-32ms je povinné povolit [akcelerátor zápisu](../../how-to-enable-write-accelerator.md). Azure Akcelerátor zápisu poskytuje optimální latenci zápisu na disk pro transakční protokol (k dispozici pouze pro řadu M-Series). K dispozici jsou některé položky, které by měly být zváženy, podobně jako maximální počet disků na typ virtuálního počítače. Podrobnosti o Akcelerátor zápisu najdete [tady](../../how-to-enable-write-accelerator.md) .


Tady je několik příkladů velikosti svazku protokolu:

| velikost objemů dat a typ disku | svazek protokolu a typ disku konfigurace 1 | svazek protokolu a typ disku konfigurace 2 |
| --- | --- | --- |
| 4 x P50-> 16 TB | 5 x P20-> 2,5 TB | 3 x P30-> 3 TB |
| 6 x P15-> 1,5 TB | 4 x P6-> 256 GB | 1 x P15-> 256 GB |


Podobně jako u SAP HANA horizontálního navýšení kapacity musí být adresář/Hana/Shared sdílený mezi SAP HANAm virtuálním počítačem a virtuálním počítačem DT 2,0. Stejná architektura jako pro SAP HANA škálování na více instancí pomocí vyhrazených virtuálních počítačů, které fungují jako vysoce dostupný server NFS, se doporučuje. Aby bylo možné poskytnout sdílený svazek zálohy, lze použít stejný návrh. Ale zákazník je až na to, pokud by to bylo nutné, nebo pokud je dostačující jenom pro použití vyhrazeného virtuálního počítače s dostatečnou kapacitou úložiště k tomu, aby fungoval jako záložní server.



### <a name="links-to-dt-20-documentation"></a>Odkazy na dokumentaci k DT 2,0 

- [Instalace a Průvodce aktualizací SAP HANA dynamické vrstvy](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA výukových kurzů a prostředků s dynamickým vrstvou](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA pro ověření dynamické vrstvy](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [Vylepšení dynamického vrstvení SAP HANA 2,0 SPS 02](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operace nasazení SAP HANA na virtuálních počítačích Azure
Následující části popisují některé operace týkající se nasazení SAP HANA systémů na virtuálních počítačích Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Zálohování a obnovení operací na virtuálních počítačích Azure
Následující dokumenty popisují, jak zálohovat a obnovit nasazení SAP HANA:

- [Přehled zálohování SAP HANA](./sap-hana-backup-guide.md)
- [SAP HANA zálohování na úrovni souborů](./sap-hana-backup-file-level.md)
- [SAP HANA srovnávací test snímku úložiště](./sap-hana-backup-guide.md)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Spuštění a restartování virtuálních počítačů, které obsahují SAP HANA
Významnou funkcí veřejného cloudu Azure je, že se vám účtují jenom vaše výpočetní minuty. Když třeba vypnete virtuální počítač, na kterém běží SAP HANA, za tento čas se vám bude účtovat jenom náklady na úložiště. Při zadání statických IP adres pro virtuální počítače v počátečním nasazení je k dispozici další funkce. Po restartování virtuálního počítače, který má SAP HANA, se virtuální počítač restartuje s předchozími IP adresami. 


### <a name="use-saprouter-for-sap-remote-support"></a>Použití SAProuter pro vzdálenou podporu SAP
Pokud máte připojení typu Site-to-site mezi místními umístěními a Azure a používáte komponenty SAP, je pravděpodobné, že už používáte SAProuter. V takovém případě pro vzdálenou podporu proveďte následující položky:

- Udržujte soukromou a statickou IP adresu virtuálního počítače, který je hostitelem SAP HANA v konfiguraci SAProuter.
- Nakonfigurujte NSG podsítě, která hostuje virtuální počítač HANA, aby povolovala přenosy přes port TCP/IP 3299.

Pokud se k Azure připojujete přes Internet a nemáte k tomuto virtuálnímu počítači směrovač SAP s SAP HANA, musíte součást nainstalovat. Nainstalujte SAProuter do samostatného virtuálního počítače v podsíti pro správu. Následující obrázek ukazuje přibližné schéma pro nasazení SAP HANA bez připojení typu Site-to-site a SAProuter:

![Hrubá schéma nasazení pro SAP HANA bez připojení typu Site-to-site a SAProuter](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Nezapomeňte nainstalovat SAProuter v samostatném virtuálním počítači, a ne ve vašem VIRTUÁLNÍm počítači s JumpBox. Samostatný virtuální počítač musí mít statickou IP adresu. Pokud chcete připojit SAProuter k SAProuter hostovanému v SAP, kontaktujte SAP pro IP adresu. (SAProuter, který je hostovaný pomocí SAP, je protějškem instance SAProuter, kterou nainstalujete na virtuální počítač.) K nakonfigurování instance SAProuter použijte IP adresu z SAP. V nastavení konfigurace je jediným potřebným portem port TCP 3299.

Další informace o tom, jak nastavit a spravovat vzdálená připojení podpory prostřednictvím SAProuter, najdete v dokumentaci ke službě [SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Vysoká dostupnost s SAP HANA na nativních virtuálních počítačích Azure
Pokud používáte SUSE Linux Enterprise Server nebo Red Hat, můžete vytvořit cluster Pacemaker se zařízeními STONITH. Zařízení můžete použít k nastavení konfigurace SAP HANA, která používá synchronní replikaci s replikací systému HANA a automatickým převzetím služeb při selhání. Další informace najdete v části Další kroky.

## <a name="next-steps"></a>Další kroky
Seznamte se s články, jak je uvedeno níže.
- [Konfigurace úložiště virtuálních počítačů Azure SAP HANA](./hana-vm-operations-storage.md)
- [Nasazení SAP HANA systému se škálováním na více systémů s pohotovostním uzlem na virtuálních počítačích Azure pomocí Azure NetApp Files v SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Nasazení SAP HANA systému se škálováním na více systémů s pohotovostním uzlem na virtuálních počítačích Azure pomocí Azure NetApp Files v Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
- [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)

 
