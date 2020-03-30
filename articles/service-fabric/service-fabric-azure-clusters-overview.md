---
title: Vytváření clusterů v systémech Windows Server a Linux
description: Clustery Service Fabric běží na Windows Server a Linux, což znamená, že budete moci nasadit a hostit service fabric aplikace kdekoli můžete spustit Windows Server nebo Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: b6942c2a0647401df0d88b83e1b144ca3207a6db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614668"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Přehled clusterů Service Fabric v Azure
Cluster Service Fabric je síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel clusteru. Clustery lze škálovat na tisíce uzlů. Pokud do clusteru přidáte nové uzly, service fabric znovu vyvažuje repliky a instance oddílů služby napříč zvýšeným počtem uzlů. Celkový výkon aplikace zlepšuje a tvrzení o přístupu k paměti snižuje. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu vyvažuje repliky oddílů a instance přes snížený počet uzlů, aby bylo možné lépe využít hardware v každém uzlu.

Typ uzlu definuje velikost, číslo a vlastnosti pro sadu uzlů (virtuálních počítačů) v clusteru. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. Typy uzlů se používají k definování rolí pro sadu uzlů clusteru, například „front-end“ nebo „back-end“. Cluster může mít více než jeden typ uzlu, ale v případě produkčních clusterů musí existovat alespoň pět virtuálních počítačů primárního typu (nebo minimálně tři virtuální počítače v případě testovacích clusterů). V uzlech primárního typu jsou umístěny [systémové služby Service Fabric](service-fabric-technical-overview.md#system-services). 

## <a name="cluster-components-and-resources"></a>Součásti a prostředky clusteru
Cluster Service Fabric v Azure je prostředek Azure, který používá a spolupracuje s jinými prostředky Azure:
* Virtuální počítače a virtuální síťové karty
* škálovací sady virtuálních počítačů
* virtuální sítě
* nástroje pro vyrovnávání zatížení,
* účty úložiště
* veřejné IP adresy

![Cluster prostředků infrastruktury služby][Image]

### <a name="virtual-machine"></a>Virtuální počítač
[Virtuální počítač,](/azure/virtual-machines/) který je součástí clusteru, se nazývá uzel, technicky je uzel clusteru za běhu service fabric. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají vlastnosti, například [vlastnosti umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Každý počítač nebo virtuální počítač má službu automatického spuštění *FabricHost.exe*, která se spustí při spuštění a potom spustí dva spustitelné soubory *Fabric.exe* a *FabricGateway.exe*, které tvoří uzel. Produkční nasazení je jeden uzel na fyzický nebo virtuální počítač. Pro testovací scénáře můžete hostovat více uzlů na jednom počítači nebo virtuálním počítači spuštěním více instancí *Fabric.exe* a *FabricGateway.exe*.

Každý virtuální počítač je přidružený ke kartě rozhraní virtuální sítě (NIC) a každé síťové kartě je přiřazena privátní IP adresa.  Virtuální počítač je přiřazen k virtuální síti a místní balancer prostřednictvím síťové karty.

Všechny virtuální počítače v clusteru jsou umístěny ve virtuální síti.  Všechny uzly ve stejné typu uzlu nebo škálovací sadě jsou umístěny ve stejné podsíti ve virtuální síti.  Tyto uzly mají pouze privátní IP adresy a nejsou přímo adresovatelné mimo virtuální síť.  Klienti mohou přistupovat ke službám na uzlech prostřednictvím azure balancer.

### <a name="scale-setnode-type"></a>Typ škálovací sady/uzlu
Při vytváření clusteru definujete jeden nebo více typů uzlů.  Uzly nebo virtuální počítače v typu uzlu mají stejnou velikost a charakteristiky, jako je počet procesorů, paměti, počet disků a vstupně-v. disk.  Například jeden typ uzlu může být pro malé, front-endové virtuální uživatele s porty otevřené pro Internet, zatímco jiný typ uzlu může být pro velké, back-endové virtuální uživatele, které zpracovávají data. V clusterech Azure je každý typ uzlu mapován na [škálovací sadu virtuálních strojů](/azure/virtual-machine-scale-sets/).

Škálovací sady můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který definujete v clusteru Azure Service Fabric, nastaví samostatnou škálovací sadu. Runtime Service Fabric je zaváděcí na každém virtuálním počítači ve škálovací sadě pomocí rozšíření virtuálních zařízení Azure. Můžete nezávisle škálovat každý typ uzlu nahoru nebo dolů, změnit skladovou položku operačního systému spuštěnou na každém uzlu clusteru, mít otevřené různé sady portů a používat různé metriky kapacity. Škálovací sada má pět [upgradovacích domén](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) a pět [domén selhání](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) a může mít až 100 virtuálních virtuálních mů.  Clustery s více než 100 uzly vytvoříte vytvořením více škálovacích sad nebo typů uzlů.

> [!IMPORTANT]
> Výběr počtu typů uzlů pro váš cluster a vlastnosti každého typu uzlu (velikost, primární, internet čelí, počet virtuálních počítačů, atd.) je důležitý úkol.  Další informace naleznete v [aspekty plánování kapacity clusteru](service-fabric-cluster-capacity.md).

Další informace načtete [typy uzlů Service Fabric a škálovací sady virtuálních počítačů](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
Instance virtuálních počítačů jsou spojené za [azure vyrovnávání zatížení](/azure/load-balancer/load-balancer-overview), který je přidružený k [veřejné IP adresy](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) a popisek DNS.  Při zřizování clusteru s * &lt;názvem&gt;clusteru*se název DNS, * &lt;název&gt;clusteru .&lt; location&gt;.cloudapp.azure.com* je popisek DNS přidružený k systému vyrovnávání zatížení před škálovací sadou.

Virtuální virtuální společnosti v clusteru mají pouze [privátní IP adresy](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  Provoz správy a provoz služeb jsou směrovány prostřednictvím veřejného balanceru zatížení.  Síťový provoz je směrován do těchto počítačů prostřednictvím pravidel NAT (klienti se připojují ke konkrétním uzlům/instancím) nebo pravidlům vyrovnávání zatížení (provoz jde do kruhového dotazování virtuálních počítačů).  Správce zatížení má přidruženou veřejnou IP adresu s názvem DNS ve formátu: * &lt;název_clusteru&gt;.&lt; umístění&gt;.cloudapp.azure.com*.  Veřejná IP adresa je další prostředek Azure ve skupině prostředků.  Pokud definujete více typů uzlů v clusteru, vytvoří se pro každý typ/škálovací sadu uzlů systém vyrovnávání zatížení. Nebo můžete nastavit jeden systém vyrovnávání zatížení pro více typů uzlů.  Typ primárního uzlu má * &lt;&gt;název clusteru s popisky DNS .&lt; umístění&gt;.cloudapp.azure.com*, jiné typy uzlů mají * &lt;&gt;-&lt;uzel&gt;název_clusteru dns&lt; label . umístění&gt;.cloudapp.azure.com*.

### <a name="storage-accounts"></a>Účty úložiště
Každý typ uzlu clusteru je podporován [účtem úložiště Azure](/azure/storage/common/storage-introduction) a spravovanými disky.

## <a name="cluster-security"></a>Zabezpečení clusteru
Cluster Service Fabric je prostředek, který vlastníte.  Je vaší odpovědností zabezpečit clustery, abyste zabránili neoprávněným uživatelům v připojení k nim. Zabezpečený cluster je obzvláště důležitý, pokud v clusteru spouštěte produkční úlohy. 

### <a name="node-to-node-security"></a>Zabezpečení mezi uzlužinami
Zabezpečení mezi uzlem zabezpečuje komunikaci mezi virtuálními počítači nebo počítači v clusteru. Tento scénář zabezpečení zajišťuje, že pouze počítače, které jsou oprávněny připojit se ke clusteru, se mohou účastnit hostování aplikací a služeb v clusteru. Service Fabric používá certifikáty X.509 k zabezpečení clusteru a poskytování funkcí zabezpečení aplikací.  K zabezpečení přenosu clusteru a zajištění ověřování clusteru a serveru je vyžadován certifikát clusteru.  Vlastní podepsané certifikáty lze použít pro testovací clustery, ale k zabezpečení produkčních clusterů by měl být použit certifikát od důvěryhodné certifikační autority.

Další informace naleznete v oblasti [zabezpečení mezi uzny](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Zabezpečení klient-uzel
Zabezpečení klient-uzel ověřuje klienty a pomáhá zabezpečit komunikaci mezi klientem a jednotlivými uzly v clusteru. Tento typ zabezpečení pomáhá zajistit, aby ke clusteru a aplikacím nasazeným v clusteru měli přístup pouze oprávnění uživatelé. Klienti jsou jednoznačně identifikováni pomocí svých pověření zabezpečení certifikátu X.509. Libovolný počet volitelných klientských certifikátů lze použít k ověření klientských nebo uživatelských klientů pomocí clusteru.

Kromě klientských certifikátů lze službu Azure Active Directory také nakonfigurovat k ověřování klientů pomocí clusteru.

Další informace naleznete v [seznamu Zabezpečení klient-uzel](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Řízení přístupu na základě rolí
Řízení přístupu na základě rolí (RBAC) umožňuje přiřadit jemně odstupňované ovládací prvky přístupu k prostředkům Azure.  Předplatným, skupinám prostředků a prostředkům můžete přiřadit různá pravidla přístupu.  Pravidla RBAC jsou zděděna podél hierarchie prostředků, pokud nejsou přepsána na nižší úrovni.  Můžete přiřadit libovolný uživatel nebo skupiny uživatelů na aad s pravidly RBAC tak, aby určených uživatelů a skupin můžete upravit clusteru.  Další informace najdete v [přehledu Azure RBAC](/azure/role-based-access-control/overview).

Service Fabric také podporuje řízení přístupu k omezení přístupu k určitým operacím clusteru pro různé skupiny uživatelů. To pomáhá zvýšit zabezpečení clusteru. Pro klienty, kteří se připojují ke clusteru, jsou podporovány dva typy řízení přístupu: role správce a role uživatele.  

Další informace naleznete v části [Řízení přístupu na základě rolí služby Fabric (RBAC).](service-fabric-cluster-security.md#role-based-access-control-rbac)

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě 
Skupiny zabezpečení sítě (NSG) řídí příchozí a odchozí provoz podsítě, virtuálního virtuálního zařízení nebo konkrétní síťové rozhraní.  Ve výchozím nastavení při více virtuálních počítačích jsou umístěny na stejné virtuální síti, které mohou komunikovat mezi sebou prostřednictvím libovolného portu.  Pokud chcete omezit komunikaci mezi počítači, můžete definovat sítě zabezpečení sítě k segmentaci sítě nebo izolovat virtuální počítače od sebe navzájem.  Pokud máte v clusteru více typů uzlů, můžete použít skupiny nsg pro podsítě, abyste zabránili vzájemné komunikaci počítačů patřících do různých typů uzlů.  

Další informace naleznete v informacích o [skupinách zabezpečení.](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Škálování

Požadavky aplikací se v průběhu času mění. Možná budete muset zvýšit prostředky clusteru, aby bylo možné splnit zvýšené zatížení aplikací nebo síťový provoz nebo snížit prostředky clusteru při poklesu poptávky. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně (změnit počet uzlů) nebo svisle (změnit prostředky uzlů). Cluster můžete kdykoli škálovat, i když jsou v clusteru spuštěny úlohy. Při škálování clusteru se automaticky škálují také vaše aplikace.

Další informace [našlápne te na článek Škálování clusterů Azure](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Inovace
Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně spravuje Microsoft. Společnost Microsoft je zodpovědná za opravy základního operačního systému a provádění runtime upgradů Service Fabric ve vašem clusteru. Cluster můžete nastavit tak, aby přijímali automatické inovace za běhu, když společnost Microsoft vydá novou verzi, nebo můžete vybrat požadovanou podporovanou verzi modulu runtime. Kromě inovací za běhu můžete také aktualizovat konfiguraci clusteru, jako jsou certifikáty nebo porty aplikace.

Další informace naleznete [v článek Inovace clusterů](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Podporované operační systémy
Clustery můžete vytvářet na virtuálních počítačích s těmito operačními systémy:

| Operační systém | Nejstarší podporovaná verze service fabric |
| --- | --- |
| Windows Server 2012 R2 | Všechny verze |
| Windows Server 2016 | Všechny verze |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

Další informace najdete [v tématu Podporované verze clusteru v Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-operating-systems)

> [!NOTE]
> Pokud se rozhodnete nasadit Service Fabric v systému Windows Server 1709, vezměte prosím na vědomí, že (1) se nejedná o dlouhodobou servisní větev, takže v budoucnu budete možná muset přesunout verze a (2) pokud nasazujete kontejnery, kontejnery postavené na Systému Windows Server 2016 nefungují v systému Windows Server 1709, a naopak (budete muset znovu je nasadit).
>


## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [zabezpečení](service-fabric-cluster-security.md), [škálování](service-fabric-cluster-scaling.md)a [upgradu](service-fabric-cluster-upgrade.md) clusterů Azure.

Další informace o [možnostech podpory service fabric](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
