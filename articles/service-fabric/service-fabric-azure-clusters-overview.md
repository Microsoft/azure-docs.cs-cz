---
title: Vytváření clusterů na serverech Windows Server a Linux
description: Service Fabric clustery běží na systémech Windows Server a Linux. Můžete nasazovat a hostovat aplikace Service Fabric kdekoli, kde můžete používat Windows Server nebo Linux.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 4aed4ab38db9f8d8b95647b6662245c93778afed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520152"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Přehled clusterů Service Fabric v Azure
Cluster Service Fabric je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Počítač nebo virtuální počítač, který je součástí clusteru, se označuje jako uzel clusteru. Clustery se můžou škálovat na tisíce uzlů. Pokud do clusteru přidáte nové uzly, Service Fabric rebilance repliky oddílů služby a instance napříč rostoucím počtem uzlů. Celkový výkon aplikace vylepšuje a kolizí pro přístup k snížení velikosti paměti. Pokud se uzly v clusteru nepoužívají efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu vyrovnává repliky oddílů a instance napříč sníženým počtem uzlů, aby bylo možné lépe využívat hardware na jednotlivých uzlech.

Typ uzlu definuje velikost, číslo a vlastnosti pro sadu uzlů (virtuálních počítačů) v clusteru. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. Typy uzlů se používají k definování rolí pro sadu uzlů clusteru, například „front-end“ nebo „back-end“. Cluster může mít více než jeden typ uzlu, ale v případě produkčních clusterů musí existovat alespoň pět virtuálních počítačů primárního typu (nebo minimálně tři virtuální počítače v případě testovacích clusterů). V uzlech primárního typu jsou umístěny [systémové služby Service Fabric](service-fabric-technical-overview.md#system-services). 

## <a name="cluster-components-and-resources"></a>Součásti clusteru a prostředky
Cluster Service Fabric v Azure je prostředek Azure, který používá a komunikuje s dalšími prostředky Azure:
* Virtuální počítače a virtuální síťové karty
* škálovací sady virtuálních počítačů
* virtuální sítě
* nástroje pro vyrovnávání zatížení,
* účty úložiště
* veřejné IP adresy

![Cluster Service Fabric][Image]

### <a name="virtual-machine"></a>Virtuální počítač
[Virtuální počítač](../virtual-machines/index.yml) , který je součástí clusteru, se nazývá uzel, i když je technicky, uzel clusteru je proces Service Fabric runtime. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají charakteristiky, jako jsou [vlastnosti umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Každý počítač nebo virtuální počítač má automaticky spuštěnou službu, *FabricHost.exe*, která spouští při spuštění, a potom spustí dva spustitelné soubory, *Fabric.exe* a *FabricGateway.exe*, které tvoří uzel. Produkční nasazení je jedním uzlem na fyzický nebo virtuální počítač. V případě testovacích scénářů můžete hostovat více uzlů na jednom počítači nebo VIRTUÁLNÍm počítači spuštěním více instancí *Fabric.exe* a *FabricGateway.exe*.

Každý virtuální počítač je přidružen k virtuální síťové kartě (NIC) a každé síťové kartě je přiřazena privátní IP adresa.  Virtuální počítač je přiřazený k virtuální síti a místnímu nástroji pro vyrovnávání zatížení prostřednictvím síťového rozhraní.

Všechny virtuální počítače v clusteru jsou umístěné ve virtuální síti.  Všechny uzly ve stejné sadě typů nebo škálování jsou umístěné ve stejné podsíti ve virtuální síti.  Tyto uzly mají pouze privátní IP adresy a nejsou přímo adresovatelné mimo virtuální síť.  Klienti mají přístup ke službám v uzlech prostřednictvím nástroje pro vyrovnávání zatížení Azure.

### <a name="scale-setnode-type"></a>Sada nebo typ škálování na více uzlů
Při vytváření clusteru definujete jeden nebo více typů uzlů.  Uzly nebo virtuální počítače v typu uzlu mají stejnou velikost a charakteristiky jako počet procesorů, paměti, počet disků a vstupně-výstupní operace disku.  Například jeden typ uzlu může být pro malé, front-end virtuální počítače s porty otevřenými na internetu, zatímco jiný typ uzlu může být pro velké, back-endové virtuální počítače zpracovávající data. V clusterech Azure se každý typ uzlu mapuje na [sadu škálování virtuálního počítače](../virtual-machine-scale-sets/index.yml).

Sady škálování můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který definujete v clusteru Azure Service Fabric, nastaví samostatnou sadu škálování. Modul runtime Service Fabric je zaveden do každého virtuálního počítače v sadě škálování pomocí rozšíření virtuálního počítače Azure. Můžete nezávisle škálovat jednotlivé typy uzlů nahoru nebo dolů, měnit skladovou jednotku operačního systému spuštěnou na každém uzlu clusteru, mít různé sady portů otevřené a používat jiné metriky kapacity. Sada škálování má pět [domén upgradu](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) a pět [domén selhání](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) a může mít až 100 virtuálních počítačů.  Clustery s více než 100 uzly vytvoříte tak, že vytvoříte vícenásobné sady nebo typy uzlů pro škálování.

> [!IMPORTANT]
> Důležitým úkolem je výběr počtu typů uzlů pro váš cluster a vlastností každého typu uzlu (velikost, primární, internetový, počet virtuálních počítačů atd.).  Další informace najdete v tématu [předpoklady pro plánování kapacity clusteru](service-fabric-cluster-capacity.md).

Další informace najdete v [Service Fabric typech uzlů a virtuálních počítačů Scale Sets](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
Instance virtuálních počítačů jsou připojené za [Nástroj pro vyrovnávání zatížení Azure](../load-balancer/load-balancer-overview.md), který je přidružený k [veřejné IP adrese](../virtual-network/public-ip-addresses.md) a popisku DNS.  Když zřizujete cluster s názvem *&lt; &gt; název_clusteru*, název DNS, *&lt; název_clusteru &gt; . &lt; Location &gt; . cloudapp.Azure.com* je popisek DNS přidružený k nástroji pro vyrovnávání zatížení před nastavenou stupnicí.

Virtuální počítače v clusteru mají jenom [privátní IP adresy](../virtual-network/private-ip-addresses.md).  Provoz správy a provoz služeb jsou směrovány prostřednictvím veřejného nástroje pro vyrovnávání zatížení.  Síťový provoz se směruje na tyto počítače prostřednictvím pravidel NAT (klienti se připojují ke konkrétním uzlům/instancím) nebo pravidel vyrovnávání zatížení (provoz směřuje do virtuálních počítačů kruhového dotazování).  Nástroj pro vyrovnávání zatížení má přidruženou veřejnou IP adresu s názvem DNS ve formátu: *&lt; název_clusteru &gt; . &lt; Location &gt; . cloudapp.Azure.com*.  Veřejná IP adresa je další prostředek Azure ve skupině prostředků.  Pokud v clusteru definujete více typů uzlů, vytvoří se nástroj pro vyrovnávání zatížení pro každou sadu typů nebo škálování uzlu. Nebo můžete nastavit jeden nástroj pro vyrovnávání zatížení pro více typů uzlů.  Typ primárního uzlu má název *&lt; klastru DNS &gt; . &lt; Location &gt; . cloudapp.Azure.com*, jiné typy uzlů mají název clusteru DNS *&lt; &gt; - &lt; NodeType &gt; . &lt; Location &gt; . cloudapp.Azure.com*.

### <a name="storage-accounts"></a>Účty úložiště
Každý typ uzlu clusteru je podporovaný účtem služby [Azure Storage](../storage/common/storage-introduction.md) a spravovanými disky.

## <a name="cluster-security"></a>Zabezpečení clusteru
Cluster Service Fabric je prostředek, který vlastníte.  Je vaše zodpovědnost za zabezpečení clusterů, aby se zabránilo neoprávněným uživatelům v jejich připojení. Zabezpečený cluster je obzvláště důležitý při spuštění produkčních úloh v clusteru. 

### <a name="node-to-node-security"></a>Zabezpečení mezi uzly
Zabezpečení mezi uzly zabezpečuje komunikaci mezi virtuálními počítači nebo počítači v clusteru. Tento scénář zabezpečení zajišťuje, že se můžou účastnit hostování aplikací a služeb v clusteru jenom počítače, které jsou autorizované pro připojení ke clusteru. Service Fabric k zabezpečení clusteru a poskytování funkcí zabezpečení aplikací používá certifikáty X. 509.  Certifikát clusteru je nutný k zabezpečení provozu clusteru a k zajištění ověřování clusteru a serveru.  Certifikáty podepsané svým držitelem se dají použít pro testovací clustery, ale k zabezpečení produkčních clusterů by se měly použít certifikát od důvěryhodné certifikační autority.

Další informace najdete v článku [zabezpečení mezi](service-fabric-cluster-security.md#node-to-node-security) uzly.

### <a name="client-to-node-security"></a>Zabezpečení klient-uzel
Zabezpečení typu klient-uzel ověřuje klienty a pomáhá zabezpečit komunikaci mezi klientem a jednotlivými uzly v clusteru. Tento typ zabezpečení pomáhá zajistit, že ke clusteru a aplikacím nasazeným v clusteru mají přístup jenom autorizovaní uživatelé. Klienti se jednoznačně identifikují pomocí přihlašovacích údajů zabezpečení certifikátů X. 509. K ověřování klientů správce nebo uživatelů s clusterem lze použít libovolný počet volitelných klientských certifikátů.

Kromě klientských certifikátů je možné Azure Active Directory taky nakonfigurovat na ověřování klientů pomocí clusteru.

Další informace najdete v článku [zabezpečení mezi klienty a uzly](service-fabric-cluster-security.md#client-to-node-security) .

### <a name="role-based-access-control"></a>Řízení přístupu na základě role
Řízení přístupu na základě role Azure (Azure RBAC) umožňuje v prostředcích Azure přiřadit jemně odstupňované ovládací prvky přístupu.  K předplatným, skupinám prostředků a prostředkům můžete přiřadit různá pravidla přístupu.  Pravidla Azure RBAC jsou zděděná v hierarchii prostředků, pokud nejsou přepsána na nižší úrovni.  Můžete přiřadit všechny uživatele nebo skupiny uživatelů ve službě AAD s pravidly Azure RBAC, aby mohli určení clusterů upravovat určené uživatele a skupiny.  Další informace najdete v tématu [Přehled Azure RBAC](../role-based-access-control/overview.md).

Service Fabric také podporuje řízení přístupu pro omezení přístupu k určitým operacím clusteru pro různé skupiny uživatelů. To pomáhá zvýšit zabezpečení clusteru. Pro klienty, kteří se připojují ke clusteru, jsou podporovány dva typy řízení přístupu: role správce a role uživatele.  

Další informace najdete v [Service Fabric řízení přístupu na základě role](service-fabric-cluster-security.md#service-fabric-role-based-access-control).

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě 
Skupiny zabezpečení sítě (skupin zabezpečení sítě) řízení příchozího a odchozího provozu podsítě, virtuálního počítače nebo konkrétního síťového adaptéru.  Ve výchozím nastavení platí, že pokud je více virtuálních počítačů umístěno ve stejné virtuální síti, mohou vzájemně komunikovat prostřednictvím libovolného portu.  Pokud chcete omezit komunikaci mezi počítači, můžete definovat skupin zabezpečení sítě k segmentaci sítě nebo izolaci virtuálních počítačů od sebe navzájem.  Pokud máte v clusteru více typů uzlů, můžete použít skupin zabezpečení sítě na podsítě, abyste zabránili vzájemné komunikaci počítačů, které patří různým typům uzlů.  

Další informace najdete v článku o [skupinách zabezpečení](../virtual-network/network-security-groups-overview.md) .

## <a name="scaling"></a>Škálování

Aplikace vyžaduje změnu v průběhu času. Možná budete muset zvýšit prostředky clusteru tak, aby splňovaly zvýšené zatížení aplikace nebo síťový provoz nebo snížili prostředky clusteru, když na vyžádání klesne požadavek. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně (změnit počet uzlů) nebo vertikálně (změnit prostředky uzlů). Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru. I když se cluster škáluje, vaše aplikace se automaticky škálují.

Další informace najdete v článku [škálování clusterů Azure](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Upgrade
Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně ho spravuje Microsoft. Společnost Microsoft zodpovídá za opravy základního operačního systému a provádění Service Fabricch upgradů modulu runtime v clusteru. Cluster můžete nastavit tak, aby přijímal automatické upgrady za běhu, když společnost Microsoft vydává novou verzi, nebo vybrat podporovanou verzi modulu runtime, kterou požadujete. Kromě upgradů za běhu můžete také aktualizovat konfiguraci clusteru, jako jsou certifikáty nebo porty aplikací.

Další informace najdete v článku [upgrade clusterů](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Podporované operační systémy
Další informace najdete [v tématu podporované verze v Azure](./service-fabric-versions.md) .


## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [zabezpečení](service-fabric-cluster-security.md), [škálování](service-fabric-cluster-scaling.md)a [upgradu](service-fabric-cluster-upgrade.md) clusterů Azure.

Přečtěte si o [možnostech podpory Service Fabric](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG