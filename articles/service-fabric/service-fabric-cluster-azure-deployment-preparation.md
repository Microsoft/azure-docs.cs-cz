---
title: Plánování nasazení clusteru Azure Service Fabric
description: Přečtěte si o plánování a přípravě na produkční nasazení clusteru Service Fabric do Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: ad6a7a6ea9a90bea4a3b6bc553da67a46144dc03
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422280"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Plánování a příprava na nasazení clusteru

Plánování a příprava na nasazení produkčního clusteru je velmi důležité.  Existuje mnoho faktorů, aby zvážila.  Tento článek vás provede kroky přípravy nasazení clusteru.

## <a name="read-the-best-practices-information"></a>Přečtěte si informace o osvědčených postupech
Chcete-li úspěšně spravovat aplikace a clustery Azure Service Fabric, existují operace, které důrazně doporučujeme provést k optimalizaci spolehlivosti vašeho produkčního prostředí.  Další informace naleznete v [doporučených postupech aplikace Service Fabric a clusteru](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Výběr operačního systému pro cluster
Service Fabric umožňuje vytváření clusterů Service Fabric na všech virtuálních počítačích nebo počítačích se systémem Windows Server nebo Linux.  Před nasazením clusteru musíte zvolit operační systém: Windows nebo Linux.  Každý uzel (virtuální počítač) v clusteru běží stejný operační systém, nelze kombinovat windows a linuxové virtuální počítače ve stejném clusteru.

## <a name="capacity-planning"></a>Plánování kapacity
Důležitým krokem každého produkčního nasazení je plánování kapacity. Zde je uvedeno několik bodů, které je vhodné vzít v úvahu v rámci procesu.

* Počáteční počet typů uzlů pro váš cluster 
* Vlastnosti každého typu uzlu (velikost, počet instancí, primární, internet čelí, počet virtuálních počítače, atd.)
* Spolehlivost a odolnost clusteru

### <a name="select-the-initial-number-of-node-types"></a>Výběr počátečního počtu typů uzlů
Nejprve je třeba zjistit, k čemu bude cluster, který vytváříte, použit. Jaké typy aplikací plánujete nasadit do tohoto clusteru? Má vaše aplikace více služeb a musí být kterákoli z nich veřejná nebo internetová? Mají vaše služby (které tvoří vaši aplikaci) různé potřeby infrastruktury, jako je větší paměť RAM nebo vyšší cykly procesoru? Cluster Service Fabric se může skládat z více než jednoho typu uzlu: primárního typu uzlu a jednoho nebo více typů neprimárních uzlů. Každý typ uzlu je mapován na škálovací sadu virtuálních strojů. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. [Vlastnosti uzlů a omezení umístění][placementconstraints] lze nastavit tak, aby omezovala určité služby na určité typy uzlů.  Další informace naleznete [v části Počet typů uzlů, které cluster potřebuje ke spuštění.](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)

### <a name="select-node-properties-for-each-node-type"></a>Výběr vlastností uzlu pro každý typ uzlu
Typy uzlů definují skladovou položku virtuálního počítače, číslo a vlastnosti virtuálních počítačů v přidružené škálovací sadě.

Minimální velikost virtuálních počítače pro každý typ uzlu je určena [úrovní odolnosti,][durability] kterou zvolíte pro typ uzlu.

Minimální počet virtuálních her pro typ primárního uzlu je určen [úrovní spolehlivosti,][reliability] kterou zvolíte.

Podívejte se na minimální doporučení pro [typy primárních uzlů](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [stavové úlohy na neprimární chod ové typy](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)uzlů a [bezstavové úlohy na neprimárních typech uzlů](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads).

Onic více než minimální počet uzlů by měl být založen na počtu replik aplikace nebo služeb, které chcete spustit v tomto typu uzlu.  [Plánování kapacity pro aplikace Service Fabric](service-fabric-capacity-planning.md) vám pomůže odhadnout prostředky, které potřebujete ke spuštění aplikací. Cluster můžete vždy škálovat nahoru nebo dolů později a upravit tak pro změnu zatížení aplikace. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Použití dočasných disků operačního systému pro škálovací sady virtuálních počítačů

*Dočasné disky operačního systému* jsou úložiště vytvořené na místním virtuálním počítači (VM) a neuložené do vzdáleného úložiště Azure. Jsou doporučeny pro všechny typy uzlů Service Fabric (primární a sekundární), protože ve srovnání s tradičními disky trvalého operačního systému, dočasné disky operačního systému:

* Snížení latence čtení a zápisu na disk operačního systému
* Povolit rychlejší operace správy uzlu resetování a opětovného zobrazení obrazu
* Snižte celkové náklady (disky jsou zdarma a nevznikají žádné další náklady na úložiště)

Dočasné disky operačního systému není konkrétní funkce Service Fabric, ale spíše funkce *škálovací sady virtuálních počítačů* Azure, které jsou mapovány na typy uzlů Service Fabric. Jejich použití s Service Fabric vyžaduje následující v šabloně clusteru Azure Resource Manager:

1. Ujistěte se, že typy uzlů určují [podporované velikosti virtuálních počítačů Azure](../virtual-machines/windows/ephemeral-os-disks.md) pro dočasné disky operačního systému a že velikost virtuálního počítače má dostatečnou velikost mezipaměti pro podporu velikosti disku operačního systému (viz *poznámka* níže.) Například:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Nezapomeňte vybrat velikost virtuálního počítače s velikostí mezipaměti, která je stejná nebo větší než velikost disku operačního systému samotného virtuálního počítače, jinak může mít vaše nasazení Azure za následek chybu (i když je původně přijata).

2. Zadejte verzi škálovací`vmssApiVersion`sady `2018-06-01` virtuálních strojů ( ) nebo novější:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. V části škálovací sady virtuálních strojů `Local` v `diffDiskSettings`šabloně nasazení zadejte možnost pro :

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> Uživatelské aplikace by neměly mít na disku s osem žádnou závislost/soubor/artefakt, protože disk operačního systému by byl ztracen v případě upgradu operačního systému.
> Proto se nedoporučuje používat [PatchOrchestrationApplication](https://github.com/microsoft/Service-Fabric-POA) s dočasnými disky.
>

> [!NOTE]
> Existující non-efemorální VMSS nelze upgradovat na místě používat dočasné disky.
> Chcete-li migrovat, uživatelé budou muset [přidat](./virtual-machine-scale-set-scale-node-type-scale-out.md) nový nodeType s dočasnými disky, přesunout úlohy do nového nodeType & [odebrat](./service-fabric-how-to-remove-node-type.md) existující nodeType.
>

Další informace a další možnosti konfigurace najdete v tématu [Dočasné disky operačního systému pro virtuální počítače Azure](../virtual-machines/windows/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Vyberte úrovně odolnosti a spolehlivosti clusteru
Úroveň odolnosti se používá k označení systému oprávnění, která vaše virtuální počítače mají s základní infrastrukturou Azure. V primárním typu uzlu toto oprávnění umožňuje service fabric pozastavit všechny požadavky na infrastrukturu na úrovni virtuálního počítače (například restartování virtuálního počítače, reimage virtuálního počítače nebo migrace virtuálního počítače), které mají vliv na požadavky na kvorum pro systémové služby a stavové služby. V typech neprimárních uzlů toto oprávnění umožňuje service fabric pozastavit všechny požadavky infrastruktury na úrovni virtuálního počítače (například restartování virtuálního počítače, opětovné image virtuálního počítače a migrace virtuálních počítačů), které mají vliv na požadavky na kvora pro vaše stavové služby.  Výhody různých úrovní a doporučení, na které úrovni použít a kdy, viz [Charakteristika trvanlivosti clusteru][durability].

Úroveň spolehlivosti se používá k nastavení počtu replik systémových služeb, které chcete spustit v tomto clusteru na typu primárního uzlu. Čím více počtu replik, tím spolehlivější jsou systémové služby v clusteru.  Výhody různých úrovní a doporučení, na které úrovni použít a kdy, viz [Charakteristika spolehlivosti clusteru][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Povolení reverzního proxy serveru nebo DNS
Služby, které se vzájemně připojují v rámci clusteru, mají obecně přímý přístup ke koncovým bodům jiných služeb, protože uzly v clusteru jsou ve stejné místní síti. Pro usnadnění připojení mezi službami poskytuje Service Fabric další služby: [Službu DNS](service-fabric-dnsservice.md) a [službu reverzníproxy](service-fabric-reverseproxy.md).  Obě služby lze povolit při nasazování clusteru.

Vzhledem k tomu, že mnoho služeb, zejména kontejnerizované služby, může mít existující název adresy URL, je možné je vyřešit pomocí standardního protokolu DNS (spíše než protokolu pojmenování), zejména ve scénářích aplikace "lift and shift". To je přesně to, co služba DNS dělá. Umožňuje mapovat názvy DNS na název služby a tím předávat adresy IP koncového bodu.

Reverzní proxy server adresy služby v clusteru, které zveřejňují koncové body HTTP (včetně protokolu HTTPS). Reverzní proxy výrazně zjednodušuje volání jiných služeb tím, že poskytuje konkrétní formát URI.  Reverzní proxy také zpracovává řešení, připojení a opakování kroky potřebné pro jednu službu komunikovat s jinou.

## <a name="prepare-for-disaster-recovery"></a>Příprava na zotavení po havárii
Důležitou součástí poskytování vysoké dostupnosti je zajistit, aby služby mohly přežít všechny různé typy selhání. To je zvláště důležité pro selhání, které jsou neplánované a mimo vaši kontrolu. [Příprava na zotavení po havárii](service-fabric-disaster-recovery.md) popisuje některé běžné režimy selhání, které by mohly být katastrofy, pokud nejsou modelovány a spravovány správně. Popisuje také skutečnosti snižující závažnost rizika a opatření, která je třeba provést, pokud došlo ke katastrofě.

## <a name="production-readiness-checklist"></a>Kontrolní seznam připravenosti k produkci
Je vaše aplikace a cluster připraven k převzetí produkčního provozu? Před nasazením clusteru do produkčního prostředí spusťte [kontrolní seznam připravenosti produkčního prostředí](service-fabric-production-readiness-checklist.md). Udržujte aplikaci a cluster běží hladce tím, že pracuje prostřednictvím položek v tomto kontrolním seznamu. Důrazně doporučujeme, aby všechny tyto položky byly odškrtnuty před vstupem do výroby.

## <a name="next-steps"></a>Další kroky
* [Vytvoření clusteru Service Fabric se systémem Windows](service-fabric-best-practices-overview.md)
* [Vytvoření clusteru Service Fabric se systémem Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster