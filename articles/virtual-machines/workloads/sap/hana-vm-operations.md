---
title: Konfigurace a operace infrastruktury SAP HANA v Azure | Dokumenty společnosti Microsoft
description: Operations guide pro systémy SAP HANA, které se nasazují na virtuálních počítačích Azure.
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
ms.openlocfilehash: 07c8f84f2e37abd87953d8e4cb20b37258b25fda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920462"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfigurace infrastruktury SAP HANA a operace v Azure
Tento dokument obsahuje pokyny pro konfiguraci infrastruktury Azure a provoz systémů SAP HANA, které se nasazují na nativních virtuálních počítačích (VM) Azure. Dokument také obsahuje informace o konfiguraci pro sap hana horizontální navýšení kapacity pro skladovou položku virtuálního počítače M128s. Tento dokument není určen k nahrazení standardní dokumentace SAP, která obsahuje následující obsah:

- [Průvodce správou SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Instalační příručky SAP](https://service.sap.com/instguides)
- [Poznámky SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Požadavky
Chcete-li použít tuto příručku, potřebujete základní znalosti následujících součástí Azure:

- [Virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Sítě Azure a virtuální sítě](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Další informace o SAP NetWeaver a dalších komponentách SAP v Azure najdete v části [SAP v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) v [dokumentaci k Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Základní aspekty nastavení
Následující části popisují základní aspekty nastavení pro nasazení systémů SAP HANA na virtuálních počítačích Azure.

### <a name="connect-into-azure-virtual-machines"></a>Připojení k virtuálním počítačům Azure
Jak je popsáno v [průvodci plánováním virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), existují dvě základní metody pro připojení k virtuálním počítačům Azure:

- Připojte se přes internet a veřejné koncové body na virtuálním počítači jump nebo na virtuálním počítači, na který běží SAP HANA.
- Připojte se prostřednictvím [sítě VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) nebo Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Připojení k webu přes SÍŤ VPN nebo ExpressRoute je nezbytné pro produkční scénáře. Tento typ připojení je také potřeba pro neprodukční scénáře, které se přidávají do produkčních scénářů, kde se používá software SAP. Následující obrázek znázorňuje příklad připojení mezi sítěmi:

![Připojení mezi sítěmi](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Volba typů virtuálních počítačů Azure
Typy virtuálních počítačů Azure, které se dá použít pro produkční scénáře, jsou uvedeny v [dokumentaci SAP pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Pro neprodukční scénáře je k dispozici širší škála nativních typů virtuálních počítačů Azure.

>[!NOTE]
> Pro neprodukční scénáře použijte typy virtuálních počítačů, které jsou uvedeny v [poznámce SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Pro využití virtuálních počítačů Azure pro produkční scénáře, zkontrolujte SAP HANA certifikované virtuální počítače v sap [publikovaných certifikovaných platformách IaaS seznamu](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Nasazení virtuálních počítačů v Azure pomocí:

- Azure Portal.
- Rutiny prostředí Azure PowerShell.
- The Azure CLI.

Můžete také nasadit kompletní nainstalovanou platformu SAP HANA na služby virtuálních zařízení Azure prostřednictvím [cloudové platformy SAP](https://cal.sap.com/). Proces instalace je popsán v [části Nasazení SAP S/4HANA nebo BW/4HANA v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) nebo s automatizací [uvolněnou zde](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

>[!IMPORTANT]
> Abyste mohli používat M208xx_v2 virtuálnípočítače, musíte být opatrní při výběru image Linuxu z galerie image virtuálního počítače Azure. Chcete-li si přečíst podrobnosti, přečtěte si článek [Velikostvirtuálního počítače optimalizovaného pro paměť](../../mv2-series.md).
> 


### <a name="storage-configuration-for-sap-hana"></a>Konfigurace úložiště pro SAP HANA
Konfigurace úložiště a typy úložišť, které mají být použity s SAP HANA v Azure, přečtěte si dokument [SAP HANA Azure konfigurace virtuálního počítače úložiště](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Nastavení virtuálních sítí Azure
Pokud máte připojení k webu k webu do Azure přes VPN nebo ExpressRoute, musíte mít alespoň jednu virtuální síť Azure, která je připojena prostřednictvím virtuální brány k okruhu VPN nebo ExpressRoute. V jednoduchých nasazeních virtuální brána může být nasazena v podsíti virtuální sítě Azure (VNet), která je také hostitelem instancí SAP HANA. Chcete-li nainstalovat SAP HANA, vytvořte dvě další podsítě v rámci virtuální sítě Azure. Jedna podsíť hostuje virtuální chod pro spuštění instancí SAP HANA. Druhá podsíť spouští jumpbox nebo virtuální počítače pro správu pro hostování SAP HANA Studio, jiného softwaru pro správu nebo aplikačního softwaru.

> [!IMPORTANT]
> Z funkčnosti, ale důležitější z důvodů výkonu, není podporováno konfigurace [virtuálních zařízení sítě Azure](https://azure.microsoft.com/solutions/network-appliances/) v komunikační cestě mezi aplikací SAP a vrstvou DBMS systému SAP NetWeaver, Hybris nebo S/4HANA. Komunikace mezi aplikační vrstvou SAP a vrstvou DBMS musí být přímá. Omezení nezahrnuje [pravidla Azure ASG a NSG,](https://docs.microsoft.com/azure/virtual-network/security-overview) pokud tato pravidla ASG a NSG umožňují přímou komunikaci. Další scénáře, kde nejsou podporovány síťové virtuální zařízení jsou v komunikačních cestách mezi virtuálními počítači Azure, které představují uzly clusteru Linux Pacemaker a zařízení SBD, jak je popsáno v [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Nebo v komunikačních cestách mezi virtuálními počítači Azure a Windows Server SOFS nastavenými tak, jak je popsáno v [clusteru, instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání windows pomocí sdílené složky v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Síťová virtuální zařízení v komunikačních cestách může snadno zdvojnásobit latenci sítě mezi dvěma komunikačními partnery, může omezit propustnost v kritických cestách mezi aplikační vrstvou SAP a vrstvou DBMS. V některých scénářích pozorovaných u zákazníků mohou síťové virtuální zařízení způsobit selhání clusterů Pacemaker Linux v případech, kdy komunikace mezi uzly clusteru Linux Pacemaker musí komunikovat se svým zařízením SBD prostřednictvím síťového virtuálního zařízení.  
> 

> [!IMPORTANT]
> Jiný návrh, který **není podporován,** je oddělení aplikační vrstvy SAP a vrstvy DBMS do různých virtuálních sítí Azure, které nejsou vzájemně [peered.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Doporučujeme oddělit aplikační vrstvu SAP a vrstvu DBMS pomocí podsítí v rámci virtuální sítě Azure namísto použití různých virtuálních sítí Azure. Pokud se rozhodnete neřídit se doporučením a místo toho oddělit dvě vrstvy do jiné virtuální sítě, dvě virtuální sítě je třeba [peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Uvědomte si, že síťový provoz mezi [dvěma partnerskými virtuálními sítěmi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure jsou předmětem nákladů na přenos. S obrovským objemem dat v mnoha terabajtů vyměňovaných mezi aplikační vrstvou SAP a vrstvou DBMS lze nahromadit značné náklady, pokud je aplikační vrstva SAP a vrstva DBMS oddělena mezi dvěma partnerskými virtuálními sítěmi Azure. 

Při instalaci virtuálních discích ke spuštění SAP HANA, virtuální chody potřebovat:

- Dvě virtuální síťové karty nainstalované: jedna síťová karta pro připojení k podsíti pro správu a jedna síťová karta pro připojení z místní sítě nebo jiných sítí k instanci SAP HANA v virtuálním počítači Azure.
- Statické privátní IP adresy, které jsou nasazeny pro obě virtuální síťové karty.

> [!NOTE]
> Měli byste přiřadit statické IP adresy prostřednictvím prostředků Azure jednotlivým virtuálním počítačům. Neměli byste přiřazovat statické IP adresy v rámci hostovaného operačního programu vniku. Některé služby Azure, jako je služba Azure Backup Service, spoléhají na skutečnost, že alespoň primární virtuální číslo je nastaveno na DHCP a ne na statické IP adresy. Viz taky dokument [Poradce při potížích se zálohováním virtuálních strojů Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Pokud potřebujete přiřadit více statických IP adres k virtuálnímu počítači, musíte virtuálnímu počítači přiřadit více virtuálních počítačů.
>
>

Pro nasazení, která jsou trvalé, ale je potřeba vytvořit architekturu sítě virtuálního datového centra v Azure. Tato architektura doporučuje oddělení brány Virtuální sítě Azure, která se připojuje k místnímu webu do samostatné virtuální sítě Azure. Tato samostatná virtuální síť by měla hostovat veškerý provoz, který ponechá místní nebo internet. Tento přístup umožňuje nasadit software pro auditování a protokolování provozu, který vstoupí do virtuálního datového centra v Azure v této samostatné virtuální síti rozbočovače. Takže máte jednu virtuální síť, která hostuje veškerý software a konfigurace, které se vztahují k příchozía odchozí provoz na nasazení Azure.

Články [Azure Virtual Datacenter: A Network Perspective](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) a [Azure Virtual Datacenter a Enterprise Control Plane](https://docs.microsoft.com/azure/architecture/vdc/) poskytují další informace o přístupu virtuálního datového centra a souvisejícím návrhu virtuální sítě Azure.


>[!NOTE]
>Provoz, který toky mezi virtuální sítí rozbočovače a virtuální sítě pro paprsky pomocí [partnerského vztahu virtuální sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) je předmětem dalších [nákladů](https://azure.microsoft.com/pricing/details/virtual-network/). Na základě těchto nákladů možná budete muset zvážit kompromisy mezi spuštěním přísného návrhu sítě hubu a paprskem a spuštěním více [bran Azure ExpressRoute,](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) které se připojíte k "paprskům", abyste obejili partnerský vztah virtuální sítě. Brány Azure ExpressRoute však také zavádějí další [náklady.](https://azure.microsoft.com/pricing/details/vpn-gateway/) Můžete také setkat s dodatečnými náklady na software jiných výrobců, který používáte pro protokolování síťového provozu, auditování a monitorování. V závislosti na nákladech na výměnu dat prostřednictvím partnerského vztahu virtuální sítě na jedné straně a nákladů vytvořených dalšími bránami Azure ExpressRoute a dalšími softwarovými licencemi se můžete rozhodnout pro mikrosegmentaci v rámci jedné virtuální sítě pomocí podsítí jako jednotky izolace místo virtuálních sítí.


Přehled různých metod pro přiřazování IP adres najdete v tématu [typy IP adres a metody přidělení v Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

U virtuálních počítačů se systémem SAP HANA byste měli pracovat s přiřazenými statickými IP adresami. Důvodem je, že některé atributy konfigurace pro hana referenční IP adresy.

[Skupiny zabezpečení sítě Azure (NSGs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) se používají k přímému provozu, který je směrován na instanci SAP HANA nebo jumpbox. Skupiny nsg a nakonec [skupiny zabezpečení aplikací](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) jsou přidruženy k podsíti SAP HANA a podsíti Správa.

Následující obrázek znázorňuje přehled hrubého schématu nasazení pro SAP HANA po architektuře virtuální sítě rozbočovače a paprsku:

![Hrubé schéma nasazení pro SAP HANA](media/hana-vm-operations/hana-simple-networking-dmz.png)

Chcete-li nasadit SAP HANA v Azure bez připojení site-to-site, stále chcete chránit instanci SAP HANA před veřejným internetem a skrýt ji za předsunutým proxy serverem. V tomto základním scénáři nasazení závisí na azure integrované služby DNS k vyřešení názvy hostitelů. Ve složitějším nasazení, kde se používají veřejné IP adresy, jsou obzvláště důležité integrované služby DNS Azure. Pomocí skupin zabezpečení sítě Azure a [síťových virtuálních zařízení Azure](https://azure.microsoft.com/solutions/network-appliances/) můžete řídit a monitorovat směrování z internetu do architektury virtuální sítě Azure v Azure. Následující obrázek znázorňuje hrubé schéma pro nasazení SAP HANA bez připojení k webu v rozbočovači a spoke virtuální síti architektury:
  
![Hrubé schéma nasazení pro SAP HANA bez připojení k webu](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

Další popis, jak používat síťové virtuální zařízení Azure k řízení a monitorování přístupu z Internetu bez architektury virtuální sítě hubu a paprsku, najdete v článku [Nasazení vysoce dostupných síťových virtuálních zařízení](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurace infrastruktury Azure pro sap hana horizontální navýšení kapacity
Chcete-li zjistit typy virtuálních počítačů Azure, které jsou certifikované pro horizontální navýšení kapacity OLAP nebo horizontální navýšení kapacity S/4HANA, zkontrolujte [hardwarový adresář SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Zaškrtnutí ve sloupci Clustering označuje podporu horizontálního navýšení kapacity. Typ aplikace označuje, zda je podporováno horizontální navýšení kapacity OLAP nebo horizontální navýšení kapacity S/4HANA. Podrobnosti o uzlech certifikovaných v horizontálním navýšení kapacity pro každý z virtuálních počítačů, zkontrolujte podrobnosti o položky v konkrétní skladové položky virtuálního zařízení uvedené v adresáři hardwaru SAP HANA.

Minimální verze operačního systému pro nasazení konfigurace horizontálnínavýšení kapacity ve virtuálních počítačích Azure, zkontrolujte podrobnosti o položky v konkrétní skladové položky virtuálních počítačů uvedené v hardwarovém adresáři SAP HANA. Z konfigurace horizontálního navýšení kapacity n-uzlu OLAP funguje jeden uzel jako hlavní uzel. Ostatní uzly až do limitu certifikace fungují jako pracovní uzel. Další pohotovostní uzly se nepočítají do počtu certifikovaných uzlů

>[!NOTE]
> Nasazení sap hana s pohotovostním uzem virtuálních virtuálních virtuálních zařízení Azure je možné jenom pomocí úložiště [Azure NetApp Files.](https://azure.microsoft.com/services/netapp/) Žádné jiné úložiště Azure s certifikací SAP HANA neumožňuje konfiguraci pohotovostních uzlů SAP HANA
>

Pro /hana/shared doporučujeme také použití [souborů Azure NetApp](https://azure.microsoft.com/services/netapp/). 

Typický základní návrh pro jeden uzel v konfiguraci horizontálnínavýšení kapacity bude vypadat takto:

![Základy horizontálního navýšení kapacity jednoho uzlu](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

Základní konfigurace uzlu virtuálního počítače pro sap hana horizontální navýšení kapacity vypadá takto:

- Pro **/hana/shared**můžete použít nativní službu nfs poskytovanou prostřednictvím souborů Azure NetApp. 
- Všechny ostatní diskové svazky nejsou sdíleny mezi různými uzly a nejsou založeny na systému sdílení systému sdílení dat. Konfigurace instalace a kroky pro horizontální navýšení kapacity hana instalace s nesdílené **/hana/data** a **/hana/log** je k dispozici dále v tomto dokumentu. Pro úložiště s certifikací HANA, které lze použít, podívejte se na článek [KONFIGURACE virtuálního úložiště virtuálních počítačů SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage).


Při dimenzování svazků nebo disků je třeba zkontrolovat dokument [SAP HANA TDI Storage Requirements](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), pro požadovanou velikost v závislosti na počtu pracovních uzlů. Dokument uvolní vzorec, který je třeba použít, abyste získali požadovanou kapacitu svazku

Ostatní kritéria návrhu, která se zobrazí v grafice konfigurace jednoho uzlu pro horizontální navýšení kapacity SAP HANA virtuální hod je virtuální síť, nebo lepší konfigurace podsítě. SAP důrazně doporučuje oddělení klienta nebo aplikace čelí provoz z komunikace mezi uzly HANA. Jak je znázorněno na obrázku, tohoto cíle je dosaženo tím, že mají dva různé virtuální nic připojené k virtuálnímu počítači. Oba virtuální sítě jsou v různých podsítích, mají dvě různé IP adresy. Potom řídíte tok provozu pomocí pravidel směrování pomocí skupin nsg nebo uživatelem definovaných tras.

Zejména v Azure neexistují žádné prostředky a metody k vynucení kvality služeb a kvót na konkrétní virtuální ničita. V důsledku toho oddělení klient/aplikace čelí a intra-uzl komunikace neotevře žádné příležitosti k upřednostnění jednoho datového proudu před druhým. Místo toho oddělení zůstává měřítkem zabezpečení při stínění komunikace uvnitř uzlu konfigurace horizontálnínavýšení kapacity.  

>[!NOTE]
>SAP doporučuje oddělení síťového provozu na straně klienta nebo aplikace a provoz v rámci uzlu, jak je popsáno v tomto dokumentu. Proto se doporučuje umístit architekturu, jak je znázorněno na poslední grafice. Také se poraďte se svým týmem pro zabezpečení a dodržování předpisů požadavky, které se odchylují od doporučení 
>

Z síťového hlediska by minimální požadovaná síťová architektura vypadala takto:

![Základy horizontálního navýšení kapacity jednoho uzlu](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalace sap hana horizontální navýšení kapacity n Azure
Při instalaci konfigurace SAP s horizontálním navýšením kapacity je třeba provést hrubé kroky:

- Nasazení nové nebo přizpůsobení existující infrastruktury virtuální sítě Azure
- Nasazení nových virtuálních počítačů pomocí úložiště Azure Managed Premium Storage, svazků ultra disků a/nebo svazků systému nfs na základě ANF
- - Přizpůsobte síťové směrování a ujistěte se, že například komunikace mezi virtuálními zařízeními mezi virtuálními zařízeními není směrována prostřednictvím síťového [virtuálního zařízení](https://azure.microsoft.com/solutions/network-appliances/). 
- Nainstalujte hlavní uzel SAP HANA.
- Přizpůsobení konfiguračních parametrů hlavního uzlu SAP HANA
- Pokračovat v instalaci pracovních uzlů SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalace SAP HANA v horizontálním navýšení kapacity
Při nasazení infrastruktury virtuálních počítačů Azure a všech dalších příprav ách je třeba nainstalovat konfigurace horizontálního navýšení kapacity SAP HANA v následujících krocích:

- Instalace hlavního uzlu SAP HANA podle dokumentace SAP
- V případě použití úložiště Azure Premium nebo úložiště ultra disků s nesdílenými disky /hana/data a /hana/log je potřeba změnit soubor global.ini a přidat parametr "basepath_shared = ne" do souboru global.ini. Tento parametr umožňuje SAP HANA spustit v horizontálním navýšení kapacity bez 'sdílené' **/hana/data** a **/hana/log** svazky mezi uzly. Podrobnosti jsou popsány v [sap poznámka #2080991](https://launchpad.support.sap.com/#/notes/2080991). Pokud používáte svazky systému sdílení systému sdílení systému zabezpečení na základě anf pro /hana/data a /hana/log, není nutné tuto změnu provádět
- Po případné změně parametru global.ini restartujte instanci SAP HANA.
- Přidejte další pracovní uzly. Viz <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>také . Zadejte interní síť pro komunikaci mezi uzly SAP HANA během instalace nebo později pomocí například místní hdblcm. Podrobnější dokumentaci naleznete také [v SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Podrobnosti pro nastavení systému škálování SAP HANA s pohotovostním uzlem na SUSE Linux ukterýění je podrobně popsáno v [nasazení systému sap hana horizontálního navýšení kapacity s pohotovostním uznem na virtuálních počítačích Azure pomocí souborů Azure NetApp na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse). Ekvivalentní dokumentaci pro Red Hat najdete v článku [Nasazení horizontálního škálovaného systému SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure pomocí souborů Azure NetApp na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel). 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA Dynamic Tiering 2.0 pro virtuální počítače Azure

Kromě certifikací SAP HANA na virtuálních počítačích řady Azure M je SAP HANA Dynamic Tiering 2.0 podporován také v Microsoft Azure (viz odkazy na dokumentaci SAP HANA Dynamic Tiering dále dolů). I když není žádný rozdíl v instalaci produktu nebo jeho provozu, například prostřednictvím SAP HANA Cockpit uvnitř virtuálního počítače Azure, existuje několik důležitých položek, které jsou povinné pro oficiální podporu v Azure. Tyto klíčové body jsou popsány níže. V celém článku se použije zkratka "DT 2.0" namísto celého názvu Dynamic Tiering 2.0.

SAP HANA Dynamic Tiering 2.0 není podporován SAP BW nebo S4HANA. Hlavní případy použití právě teď jsou nativní hana aplikace.


### <a name="overview"></a>Přehled

Na obrázku níže je uveden přehled týkající se podpory DT 2.0 v Microsoft Azure. Existuje soubor povinných požadavků, které je třeba dodržovat, aby bylo v souladu s úřední certifikací:

- DT 2.0 musí být nainstalovaný na vyhrazeném virtuálním počítači Azure. Nemusí běžet na stejném virtuálním počítači, kde sap HANA běží
- Virtuální počítače SAP HANA a DT 2.0 musí být nasazené v rámci stejné virtuální sítě Azure.
- Virtuální počítače SAP HANA a DT 2.0 musí být nasazené s povolenými zrychlenými sítěmi Azure.
- Typ úložiště pro virtuální počítače DT 2.0 musí být Azure Premium Storage
- K virtuálnímu počítači DT 2.0 musí být připojeno více disků Azure.
- Je nutné vytvořit softwarový raid / pruhovaný svazek (buď přes lvm nebo mdadm) pomocí prokládání přes disky Azure

Další podrobnosti budou vysvětleny v následujících částech.

![Přehled architektury SAP HANA DT 2.0](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Vyhrazený virtuální počítač Azure pro SAP HANA DT 2.0

Ve službě Azure IaaS se DT 2.0 podporuje jenom na vyhrazeném virtuálním počítači. Není povoleno spouštět DT 2.0 na stejném virtuálním počítači Azure, kde je spuštěna instance HANA. Zpočátku dva typy virtuálních počítačů lze použít ke spuštění SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Viz Popis typu virtuálního virtuálního [mísy zde](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Vzhledem k základní myšlence DT 2.0, která je o snižování "teplých" dat, aby se ušetřily náklady, má smysl použít odpovídající velikosti virtuálních počítače. Neexistuje žádné přísné pravidlo, i když pokud jde o možné kombinace. Záleží na konkrétní zatížení zákazníka.

Doporučené konfigurace by byly:

| Typ virtuálního uživatele SAP HANA | Typ virtuálního měna DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Všechny kombinace virtuálních zařízení řady M s certifikací SAP HANA s podporovanými virtuálními zařízeními DT 2.0 (M64-32ms a E32sv3) jsou možné.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Sítě Azure a SAP HANA DT 2.0

Instalace DT 2.0 na vyhrazeném virtuálním počítači vyžaduje síťovou propustnost mezi virtuálním proudem DT 2.0 a virtuálním počítačem SAP HANA s minimem 10 Gb. Proto je povinné umístit všechny virtuální počítače v rámci stejné virtuální sítě Azure a povolit azure akcelerované sítě.

Další informace o azure accelerated networking [najdete tady](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Úložiště virtuálních zařízení pro SAP HANA DT 2.0

Podle pokynů pro osvědčené postupy DT 2.0 by měla být propustnost vstupně-vstupně-disků minimálně 50 MB/s na fyzické jádro. Při pohledu na specifikace pro dva typy virtuálních počítačů Azure, které jsou podporované pro DT 2.0 maximální limit propustnost disku vi pro virtuální počítač vypadat:

- E32sv3 : 768 MB/s (bez mezipaměti), což znamená poměr 48 MB/s na fyzické jádro
- M64-32ms: 1000 MB/s (bez mezipaměti), což znamená poměr 62,5 MB/s na fyzické jádro

Je nutné připojit více disků Azure k virtuálnímu počítači DT 2.0 a vytvořit softwarový raid (prokládání) na úrovni operačního systému k dosažení maximálního limitu propustnosti disku na virtuální počítač. Jeden disk Azure nemůže poskytnout propustnost k dosažení maximálního limitu virtuálních počítačů v tomto ohledu. Úložiště Azure Premium je povinné pro spuštění DT 2.0. 

- Podrobnosti o dostupných typech disků Azure naleznete [zde](../../windows/disks-types.md)
- Podrobnosti o vytváření softwaru raid přes mdadm lze nalézt [zde](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Podrobnosti o konfiguraci LVM pro vytvoření proklápěcího svazku pro maximální propustnost naleznete [zde](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

V závislosti na požadavcích na velikost existují různé možnosti, jak dosáhnout maximální propustnost virtuálního počítače. Tady jsou možné konfigurace disků svazku dat pro každý typ virtuálního počítače DT 2.0 k dosažení horního limitu propustnosti virtuálního počítače. Virtuální ms E32sv3 by měl být považován za vstupní úroveň pro menší úlohy. V případě, že by se ukázalo, že to není dostatečně rychlé, může být nutné změnit velikost virtuálního počítače na M64-32ms.
Vzhledem k tomu, že virtuální mtom a virtuální m2 M64-32ms má mnoho paměti, zatížení vi nemusí dosáhnout limitu, zejména pro úlohy náročné na čtení. Proto může být v závislosti na zatížení specifickém pro zákazníka dostačující méně disků v prokládané sadě. Ale pro jistotu byly níže uvedené konfigurace disku vybrány tak, aby zaručovalo maximální propustnost:


| Skladová položka virtuálního počítače | Konfigurace disku 1 | Konfigurace disku 2 | Konfigurace disku 3 | Konfigurace disku 4 | Konfigurace disku 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Zejména v případě, že je úloha intenzivní pro čtení, může zvýšit výkon vi a zapnout mezipaměť hostitelů Azure "jen pro čtení", jak je doporučeno pro datové svazky databázového softwaru. Vzhledem k tomu, že pro transakční protokol mezipaměti hostitelského disku Azure musí být "none". 

Pokud jde o velikost svazku protokolu doporučenývýchozí bod je heuristická 15 % velikosti dat. Vytvoření svazku protokolu lze provést pomocí různých typů disků Azure v závislosti na nákladech a propustnostpožadavky. Pro svazek protokolu je vyžadována vysoká vstupně-v.i./O propustnost is required.  V případě použití typu VM M64-32ms je povinné povolit [akcelerátor zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Azure Write Accelerator poskytuje optimální latenci zápisu na disk pro transakční protokol (k dispozici pouze pro řadu M). Existují některé položky, které je třeba zvážit, i když jako maximální počet disků na typ virtuálního počítače. Podrobnosti o akcelerátoru zápisu naleznete [zde](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Zde je několik příkladů o velikosti svazku protokolu:

| velikost svazku dat a typ disku | konfigurace svazku protokolu a disku 1 | konfigurace svazku protokolu a disku 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Stejně jako pro sap hana horizontální navýšení kapacity, /hana/shared adresář musí být sdíleny mezi virtuálním virtuálním uživatelem SAP HANA a DT 2.0 virtuálního uživatele. Stejná architektura jako pro SAP HANA horizontální navýšení kapacity pomocí vyhrazených virtuálních měn, které fungují jako vysoce dostupný server nfs se doporučuje. Chcete-li poskytnout sdílený záložní svazek, lze použít identický návrh. Ale je na zákazníkovi, pokud ha by bylo nutné, nebo pokud je dostačující pouze použít vyhrazený virtuální modul s dostatečnou úložnou kapacitou fungovat jako záložní server.



### <a name="links-to-dt-20-documentation"></a>Odkazy na dokumentaci DT 2.0 

- [Průvodce instalací a aktualizací dynamického vrstvení SAP HANA](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Sap HANA Dynamické vrstvení kurzy a zdroje](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA Dynamické vrstvení PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [Vylepšení dynamického vrstvení SAP HANA 2.0 SPS 02](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operace pro nasazení SAP HANA na virtuálních počítačích Azure
Následující části popisují některé operace související s nasazením systémů SAP HANA na virtuálních počítačích Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Zálohování a obnovení operací na virtuálních počítačích Azure
Následující dokumenty popisují, jak zálohovat a obnovit nasazení SAP HANA:

- [Přehled zálohování SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Zálohování na úrovni souboru SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Srovnávací test snímku úložiště SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Spuštění a restartování virtuálních počítačů obsahujících SAP HANA
Hlavní vlastností veřejného cloudu Azure je, že se vám účtují jenom za výpočetní minuty. Například při vypnutí virtuálního virtuálního uživatele, který běží SAP HANA, se vám budou účtovat jenom náklady na úložiště během této doby. Další funkce je k dispozici, když zadáte statické IP adresy pro vaše virtuální počítače v počátečním nasazení. Když restartujete virtuální počítač, který má SAP HANA, virtuální počítač se restartuje s jeho předchozí IP adresy. 


### <a name="use-saprouter-for-sap-remote-support"></a>Použití saprouteru pro vzdálenou podporu SAP
Pokud máte připojení mezi web-to-site mezi místními umístěními a Azure a používáte komponenty SAP, pak už pravděpodobně používáte SAProuter. V takovém případě vyplňte následující položky pro vzdálenou podporu:

- Udržujte privátní a statickou IP adresu virtuálního počítače, který hostuje SAP HANA v konfiguraci SAProuter.
- Nakonfigurujte skupinu zabezpečení sítě podsítě, která je hostitelem virtuálního počítače HANA, tak, aby umožňovala přenos y prostřednictvím portu TCP/IP 3299.

Pokud se připojujete k Azure přes internet a nemáte směrovač SAP pro virtuální počítač s SAP HANA, pak je potřeba nainstalovat komponentu. Nainstalujte SAProuter do samostatného virtuálního virtuálního montovny v podsíti Správa. Následující obrázek znázorňuje hrubé schéma pro nasazení SAP HANA bez připojení k webu a s SAProuterem:

![Hrubé schéma nasazení pro SAP HANA bez připojení site-to-site a SAProuter](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Nezapomeňte nainstalovat SAProuter do samostatného virtuálního počítače a ne do virtuálního počítače Jumpbox. Samostatný virtuální virtuální počítačům musí mít statickou IP adresu. Chcete-li připojit SAProuter k SAProuteru, který je hostován společností SAP, obraťte se na společnost SAP a požádejte o adresu IP. (SAProuter, který je hostitelem SAP je protějšek instance SAProuter, který nainstalujete na virtuální počítač.) Ke konfiguraci instance SAProuteru použijte IP adresu SAP. V nastavení konfigurace je jediným potřebným portem port TCP 3299.

Další informace o nastavení a údržbě připojení vzdálené podpory prostřednictvím směrovače SAProuter naleznete v [dokumentaci SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Vysoká dostupnost s SAP HANA na nativních virtuálních počítačích Azure
Pokud používáte SUSE Linux Enterprise Server nebo Red Hat, můžete vytvořit cluster Pacemaker se zařízeními STONITH. Pomocí zařízení můžete nastavit konfiguraci SAP HANA, která používá synchronní replikaci s replikací systému HANA a automatickým převzetím služeb při selhání. Další informace naleznete v části Další kroky.

## <a name="next-steps"></a>Další kroky
Seznamte se s uvedenými články
- [Konfigurace úložiště virtuálních počítačů Azure SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- [Nasazení horizontálního navýšení kapacity systému SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure pomocí souborů Azure NetApp na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Nasazení horizontálního navýšení kapacity systému SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure pomocí souborů Azure NetApp na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)

 

