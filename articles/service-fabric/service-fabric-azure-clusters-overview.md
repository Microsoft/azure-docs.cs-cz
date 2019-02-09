---
title: Vytvoření clusterů Azure Service Fabric ve Windows serveru a Linuxu | Dokumentace Microsoftu
description: Clustery Service Fabricu spouštět na Windows Server a Linux, což znamená, že budete mít k nasazení a hostování aplikací Service Fabric kdekoli můžete spustit systém Windows Server nebo Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: a968b173357bf8bcb83990b891f38306895b4ca8
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967145"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Přehled Service Fabric clustery v Azure
Cluster Service Fabric je síťově propojená sada virtuálních nebo fyzických počítačů, do které se nasazují a spravují mikroslužby. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel clusteru. Clustery je možné škálovat na tisíce uzlů. Pokud přidáte nové uzly do clusteru Service Fabric znovu vytvoří rovnováhu replik oddílů služby a instance napříč zvýšeného počtu uzlů. Celkově zlepšuje výkon aplikací a snižuje kolize pro přístup k paměti. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu znovu vytvoří rovnováhu replik oddílů a instancí napříč snížený počet uzlů, abyste měli lepší využití hardwaru na každém uzlu.

Typ uzlu definuje velikost, počet a vlastnosti pro sadu uzlů (virtuálních počítačích) v clusteru. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. Typy uzlů se používají k definování rolí pro sadu uzlů clusteru, například „front-end“ nebo „back-end“. Cluster může mít více než jeden typ uzlu, ale v případě produkčních clusterů musí existovat alespoň pět virtuálních počítačů primárního typu (nebo minimálně tři virtuální počítače v případě testovacích clusterů). V uzlech primárního typu jsou umístěny [systémové služby Service Fabric](service-fabric-technical-overview.md#system-services). 

## <a name="cluster-components-and-resources"></a>Součásti clusteru a prostředky
Cluster Service Fabric v Azure je prostředek Azure, která používá a pracuje v jiných prostředcích Azure:
* Virtuální počítače a virtuální síťové karty
* škálovací sady virtuálních počítačů
* virtuální sítě
* Nástroje pro vyrovnávání zatížení
* účty úložiště
* Veřejné IP adresy

![Cluster Service Fabric][Image]

### <a name="virtual-machine"></a>Virtuální počítač
A [virtuální počítač](/azure/virtual-machines/) , která nepatří do clusteru, i když technicky vzato uzlu clusteru je proces modulu runtime Service Fabric, se nazývá uzel. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají určité charakteristiky, jako například [vlastnosti umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Každý počítač nebo virtuální počítač má automaticky spouštěná služba, *FabricHost.exe*, která se spustí při spuštění a spustí dvě spustitelné soubory, *Fabric.exe* a *FabricGateway.exe* , které tvoří uzlu. Produkční nasazení je jeden uzel na fyzický nebo virtuální počítač. Pro testovací scénáře, může hostovat více uzlů na jeden počítač nebo virtuální počítač spuštěním několika instancí *Fabric.exe* a *FabricGateway.exe*.

Každý virtuální počítač je přidružený k virtuální síťové karty (NIC) a každé síťové rozhraní je mu přiřazená privátní IP adresu.  Virtuální počítač je přiřazena k virtuální síti a místní nástroje pro vyrovnávání prostřednictvím síťové rozhraní

Všechny virtuální počítače v clusteru jsou umístěny ve virtuální síti.  Všechny uzly v stejný uzel typu/škálovací sady jsou umístěny ve stejné podsíti ve virtuální síti.  Tyto uzly pouze privátní IP adresy a nejsou přímo adresovatelnými mimo virtuální síť.  Klienti mají přístup k službám na uzlech prostřednictvím služby Azure load balancer.

### <a name="scale-setnode-type"></a>Škálovací sada/uzel typu
Při vytváření clusteru, definovat jeden nebo více typů uzlů.  Uzly nebo virtuální počítače, typu mít stejné velikosti a vlastnosti, jako je například počet procesorů, paměti, počtu disků a diskových vstupně-výstupních operací.  Jeden typ uzlu může být například malé, front-endový virtuální počítače s porty otevřené k Internetu, i když může být jiný typ uzlu pro velké a back-end virtuální počítače, které zpracovávají data. V Azure clustery, každý typ uzlu je namapován na [škálovací sadu virtuálních počítačů](/azure/virtual-machine-scale-sets/).

Škálovací sady můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který definujete v clusteru Azure Service Fabric nastaví samostatné škálovací sady. Modul runtime Service Fabric je polohy na každý virtuální počítač ve škálovací sadě pomocí rozšíření virtuálního počítače Azure. Můžete nezávisle na sobě horizontální i vertikální škálování každého typu uzlu, změnit skladová položka operačního systému, které běží na všech uzlech clusteru, mají různé sady otevřených portů a použít různé metriky kapacity. Škálovací sady má pět [upgradovacích doménách](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) až pět [domén selhání](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) a může obsahovat až 100 virtuálních počítačů.  Clustery s více než 100 uzlů vytvoříte tak, že vytvoříte více typů škálovací sady nebo uzel.

> [!IMPORTANT]
> Výběr počet typů uzlů pro váš cluster a vlastnosti každého typu uzlu (velikost, primární, připojení k Internetu, počet virtuálních počítačů, atd.) je důležitá úloha.  Další informace najdete v článku [informace o plánování kapacity clusteru](service-fabric-cluster-capacity.md).

Další informace najdete v článku [typy uzlů Service Fabric a virtuálního počítače škálovací sady](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure
Instance virtuálních počítačů jsou připojené za [nástroji Azure load balancer](/azure/load-balancer/load-balancer-overview), který je přidružený [veřejnou IP adresu](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) a název DNS.  Při zřizování clusteru s  *&lt;clustername&gt;*, název DNS  *&lt;clustername&gt;.&lt; umístění&gt;. cloudapp.azure.com* je název DNS, který je přidružený k vyrovnávání zatížení před škálovací sady.

Virtuální počítače v clusteru mají pouze [privátních IP adres](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  Přenos pro správu a provoz služeb jsou směrovány veřejný internetový nástroj pro vyrovnávání zatížení.  Síťový provoz se směruje na těchto počítačích prostřednictvím pravidla překladu adres (klienti připojit k určité uzly/instance) nebo pravidla Vyrovnávání zatížení (provoz směruje do virtuálních počítačů round robin).  Nástroj pro vyrovnávání zatížení má přidružená veřejná IP adresa s názvem DNS ve formátu:  *&lt;clustername&gt;.&lt; umístění&gt;. cloudapp.azure.com*.  Veřejná IP adresa je dalším prostředkem Azure ve skupině prostředků.  Pokud definujete více typy uzlů v clusteru, nástroj pro vyrovnávání zatížení se vytvoří pro každý uzel typu/škálovací sady. Nebo můžete nastavit nástroj pro vyrovnávání zatížení pro víc typů uzlů.  Primární typ uzlu je název DNS  *&lt;clustername&gt;.&lt; umístění&gt;. cloudapp.azure.com*, máte název DNS jiné typy uzlů  *&lt;clustername&gt;-&lt;nodetype&gt;.&lt; umístění&gt;. cloudapp.azure.com*.

### <a name="storage-accounts"></a>Účty úložiště
U každého typu uzlu clusteru je podporována [účtu služby Azure storage](/azure/storage/common/storage-introduction) a spravované disky.

## <a name="cluster-security"></a>Zabezpečení clusteru
Cluster Service Fabric je prostředek, který vlastníte.  Je vaší odpovědností zajistit clusterům pomáhá zabránit neoprávněným uživatelům v připojení k nim. Zabezpečení clusteru je obzvláště důležité při spouštění úloh v produkčním prostředí v clusteru. 

### <a name="node-to-node-security"></a>Zabezpečení mezi uzly
Zabezpečení mezi uzly zabezpečuje komunikaci mezi virtuálními počítači nebo počítačích v clusteru. Tento scénář zabezpečení zajistíte, že jenom počítače, které mají oprávnění k připojení clusteru mohl podílet na hostování aplikací a služeb v clusteru. Service Fabric pomocí certifikátů X.509 zabezpečení clusteru a poskytuje funkce pro zabezpečení aplikací.  Certifikát clusteru je potřeba zabezpečit provoz clusteru a clusteru a serverem ověřování.  Vlastní certifikáty podepsané lze použít pro testovací clustery, ale certifikát od důvěryhodné certifikační autority by měla sloužit k zabezpečené produkční clustery.

Další informace najdete v článku [zabezpečení mezi uzly](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Uzel klienta zabezpečení
Uzel klienta zabezpečení ověřuje klienty a pomáhá zajistit komunikaci mezi klientem a jednotlivým uzlům v clusteru. Tento typ zabezpečení pomáhá zajistit, že jenom Autorizovaní uživatelé přístup ke clusteru a aplikace, které jsou nasazené na clusteru. Klienti jsou jednoznačně identifikované pomocí kteréhokoliv svoje přihlašovací údaje zabezpečení certifikátu X.509. Libovolný počet volitelné klientské certifikáty lze použít k ověřování klientů správce nebo uživatel s clusterem.

Kromě klientských certifikátů je Azure Active Directory také nakonfigurovat k ověřování klientů s clusterem.

Další informace najdete v článku [uzel klienta zabezpečení](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Řízení přístupu na základě rolí
Řízení přístupu na základě rolí (RBAC) umožňuje přiřadit velice přesně kontrolovat přístup ovládací prvky pro prostředky Azure.  Můžete přiřadit různá přístupová pravidla pro předplatná, skupiny prostředků a prostředky.  RBAC pravidla jsou odvozeny podél hierarchie prostředků na nižší úrovni nepřepíšete.  Můžete přiřadit všechny uživatele nebo skupiny uživatelů v AAD pomocí RBAC pravidel tak, aby určeným uživatelů a skupin můžete změnit váš cluster.  Další informace najdete v článku [přehled Azure RBAC](/azure/role-based-access-control/overview).

Service Fabric také podporuje řízení přístupu k omezení přístupu k určité operace clusteru pro různé skupiny uživatelů. To pomáhá lépe zabezpečit clusteru. Klienti připojující se ke clusteru podporují dva typy ovládacích prvků přístupu: Role správce a role uživatele.  

Další informace najdete v článku [Service Fabric Role-Based řízení přístupu (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě 
Skupiny zabezpečení sítě (Nsg) řídit příchozí a odchozí provoz z podsítě, virtuální počítač nebo konkrétní síťový adaptér  Ve výchozím nastavení když několik virtuálních počítačů jsou umístěny ve stejné virtuální síti komunikovat mezi sebou přes libovolný port.  Pokud chcete omezit komunikaci mezi počítače můžete definovat skupiny Nsg k segmentu sítě nebo izolaci virtuálních počítačů od sebe navzájem.  Pokud máte více typy uzlů v clusteru, můžete použít skupiny Nsg na podsítě, aby se zabránilo počítače, které patří do jiného uzlu typy komunikaci mezi sebou.  

Další informace, přečtěte si informace o [skupin zabezpečení](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Škálování

Požadavky na aplikace v průběhu času měnit. Budete muset zvýšit prostředky clusteru k zajištění provozu aplikace vyšší zatížení nebo sítě nebo omezovat prostředky clusteru, jakmile poptávka poklesne. Po vytvoření clusteru Service Fabric, je možné škálovat cluster vodorovně (změnit počet uzlů), nebo svisle (změnit prostředky uzly). Je možné škálovat cluster v okamžiku, i když spouštění úloh v clusteru. Škálování clusteru, vaše aplikace automaticky škálovat směrem také.

Další informace najdete v článku [Azure škálování clusterů](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Upgrade
Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně spravovaného společností Microsoft. Microsoft je zodpovědná za použití dílčích oprav základního operačního systému a inovacích modulu runtime Service Fabric ve vašem clusteru. Můžete nastavit cluster pro příjem upgradů automatické modulu runtime, když společnost Microsoft vydává nové verze, nebo vybrat podporovaná verze modulu runtime, který chcete. Kromě toho upgrade modulu runtime můžete také aktualizovat konfiguraci clusteru, například certifikáty nebo porty aplikací.

Další informace najdete v článku [upgradu clusterů](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Podporované operační systémy
Budete moct vytvářet clustery na virtuálních počítačích s těmito operačními systémy:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Windows Server 1803
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (podpora ve verzi preview)

> [!NOTE]
> Pokud se rozhodnete nasadit Service Fabric ve Windows serveru 1709, mějte prosím na paměti, že (1) není dlouhodobém horizontu obslužná větev, takže možná budete muset přesunout verze v budoucnu a (2) Pokud nasazujete kontejnery, kontejnery, založená na Windows serveru 2016 nefungují na Windows serveru  1709 a naopak (budete muset znovu nasadit).
>


## <a name="next-steps"></a>Další postup
Další informace o [zabezpečení](service-fabric-cluster-security.md), [škálování](service-fabric-cluster-scaling.md), a [upgrade](service-fabric-cluster-upgrade.md) clustery Azure.

Další informace o [možnosti podpory pro Service Fabric](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG