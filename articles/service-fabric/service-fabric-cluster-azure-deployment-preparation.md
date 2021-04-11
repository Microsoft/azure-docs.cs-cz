---
title: Plánování nasazení clusteru Azure Service Fabric
description: Přečtěte si o plánování a přípravě nasazení produkčního Service Fabric clusteru do Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 82521487b9a3e9438784e010a32cf6df8e7be2ef
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046313"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Plánování a příprava nasazení clusteru

Plánování a příprava nasazení v produkčním clusteru je velmi důležitá.  Je potřeba vzít v úvahu mnoho faktorů.  Tento článek vás provede jednotlivými kroky při přípravě nasazení clusteru.

## <a name="read-the-best-practices-information"></a>Přečtěte si informace o osvědčených postupech.
Aby bylo možné úspěšně spravovat aplikace a clustery Azure Service Fabric, jsou k dispozici operace, které vám umožní optimalizovat spolehlivost provozního prostředí.  Další informace najdete v tématu [Service Fabric osvědčené postupy pro aplikace a cluster](./service-fabric-best-practices-security.md).

## <a name="select-the-os-for-the-cluster"></a>Vyberte operační systém pro cluster.
Service Fabric umožňuje vytváření clusterů Service Fabric na všech virtuálních počítačích nebo počítačích se systémem Windows Server nebo Linux.  Před nasazením clusteru je nutné zvolit operační systém: Windows nebo Linux.  Každý uzel (virtuální počítač) v clusteru spouští stejný operační systém. virtuální počítače se systémem Windows a Linux nelze kombinovat ve stejném clusteru.

## <a name="capacity-planning"></a>Plánování kapacity
Důležitým krokem každého produkčního nasazení je plánování kapacity. Zde je uvedeno několik bodů, které je vhodné vzít v úvahu v rámci procesu.

* Počáteční počet typů uzlů pro váš cluster 
* Vlastnosti každého typu uzlu (velikost, počet instancí, primární, internetový přístup, počet virtuálních počítačů atd.)
* Spolehlivost a odolnost clusteru

### <a name="select-the-initial-number-of-node-types"></a>Vyberte počáteční počet typů uzlů.
Nejdřív musíte zjistit, k čemu se má vytvářený cluster používat. Jaké druhy aplikací plánujete nasadit do tohoto clusteru? Má vaše aplikace více služeb a některé z nich musí být veřejné nebo internetové? Mají vaše služby (z vaší aplikace) různé požadavky na infrastrukturu, jako je větší nebo vyšší počet cyklů procesoru? Cluster Service Fabric se může skládat z více než jednoho typu uzlu: primární typ uzlu a jeden nebo více typů neprimárních uzlů. Každý typ uzlu je namapován na sadu škálování virtuálního počítače. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. [Vlastnosti uzlu a omezení umístění][placementconstraints] je možné nastavit tak, aby se omezily konkrétní služby na konkrétní typy uzlů.  Další informace najdete v tématu [Service Fabric plánování kapacity clusteru](service-fabric-cluster-capacity.md).

### <a name="select-node-properties-for-each-node-type"></a>Vybrat vlastnosti uzlu pro každý typ uzlu
Typy uzlů definují SKU, číslo a vlastnosti virtuálních počítačů v přidružené sadě škálování.

Minimální velikost virtuálních počítačů pro každý typ uzlu je určena [úrovní odolnosti][durability] , kterou zvolíte pro typ uzlu.

Minimální počet virtuálních počítačů pro typ primárního uzlu závisí na zvolené [úrovni spolehlivosti][reliability] .

Prohlédněte si minimální doporučení pro [typy primárních uzlů](service-fabric-cluster-capacity.md#primary-node-type), [stavové úlohy na neprimárních typech uzlů](service-fabric-cluster-capacity.md#stateful-workloads)a [bezstavové úlohy na neprimárních typech uzlů](service-fabric-cluster-capacity.md#stateless-workloads).

Jakékoli více než minimální počet uzlů by měl být založen na počtu replik aplikace nebo služeb, které chcete spustit v tomto typu uzlu.  [Plánování kapacity pro aplikace Service Fabric](service-fabric-capacity-planning.md) vám pomůže odhadnout prostředky, které potřebujete ke spuštění svých aplikací. Kdykoli můžete cluster škálovat nahoru nebo dolů později, aby se změnila změna aplikační úlohy. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Používejte dočasné disky s operačním systémem pro služby Virtual Machine Scale Sets

*Dočasné disky s operačním systémem* se vytvářejí na místním virtuálním počítači a neukládají se do vzdáleného Azure Storage. Doporučuje se pro všechny Service Fabric typy uzlů (primární a sekundární), protože jsou v porovnání s tradičními trvalými disky s operačním systémem a s dočasnými disky operačního systému:

* Snížit latenci čtení/zápisu na disk s operačním systémem
* Povolení rychlejšího resetování/obnovení operací správy uzlů na obrázku
* Snižte celkové náklady (disky jsou bezplatné a neúčtují se žádné další náklady na úložiště).

Dočasné disky s operačním systémem nejsou konkrétní funkcí Service Fabric, ale spíše funkcí *sady škálování virtuálních počítačů* Azure, které jsou namapované na Service Fabric typy uzlů. Použití s Service Fabric v šabloně Azure Resource Manager clusteru vyžaduje následující:

1. Ujistěte se, že typy uzlů určují [podporované velikosti virtuálních počítačů Azure](../virtual-machines/ephemeral-os-disks.md) pro dočasné disky s operačním systémem a že velikost virtuálního počítače má dostatečnou velikost mezipaměti, aby podporovala velikost disku s operačním systémem (viz *Poznámka* níže). Například:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Ujistěte se, že jste vybrali velikost virtuálního počítače, která je stejná nebo větší než velikost disku operačního systému samotného virtuálního počítače. v opačném případě může při nasazení Azure dojít k chybě (i v případě, že se původně přijal).

2. Zadejte verzi sady škálování sady virtuálních počítačů ( `vmssApiVersion` ) `2018-06-01` nebo novější:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. V části sada škálování virtuálního počítače v šabloně nasazení zadejte `Local` možnost pro `diffDiskSettings` :

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
> Uživatelské aplikace by neměly mít na disku s operačním systémem žádné závislosti, soubory nebo artefakty, protože by došlo ke ztrátě disku s operačním systémem v případě upgradu operačního systému.

> [!NOTE]
> Stávající nedočasný VMSS nelze upgradovat na místě, aby bylo možné používat dočasné disky.
> Aby bylo možné provést migraci, uživatelé budou muset [Přidat](./virtual-machine-scale-set-scale-node-type-scale-out.md) nový typ NodeType s dočasnými disky, přesunout úlohy do nového uzlu NodeType & [Odebrat](./service-fabric-how-to-remove-node-type.md) existující uzel NodeType.
>

Další informace a další možnosti konfigurace najdete v tématu [dočasné disky s operačním systémem pro virtuální počítače Azure](../virtual-machines/ephemeral-os-disks.md) . 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Vybrat úrovně trvanlivosti a spolehlivosti pro cluster
Úroveň odolnosti se používá k označení systému oprávnění, která vaše virtuální počítače mají s podkladovou infrastrukturou Azure. V primárním typu uzlu toto oprávnění umožňuje Service Fabric pozastavit jakoukoli žádost o infrastrukturu na úrovni virtuálního počítače (třeba restartování virtuálního počítače, obnovení virtuálního počítače nebo migraci virtuálního počítače), která mají vliv na požadavky kvora pro systémové služby a stavové služby. V neprimárních typech uzlů toto oprávnění umožňuje Service Fabric pozastavit všechny požadavky infrastruktury na úrovni virtuálního počítače (třeba restartování virtuálního počítače, obnovení bitové kopie virtuálního počítače a migrace virtuálních počítačů), které mají vliv na požadavky na kvorum pro stavové služby.  Výhody různých úrovní a doporučení, na kterých úroveň použít a kdy, najdete v tématu [charakteristiky odolnosti clusteru][durability].

Úroveň spolehlivosti slouží k nastavení počtu replik systémových služeb, které chcete v tomto clusteru spustit na primárním typu uzlu. Větší počet replik, což je spolehlivější systémové služby ve vašem clusteru.  Výhody různých úrovní a doporučení, na kterých úroveň použít a kdy, najdete v tématu [charakteristiky spolehlivosti clusteru][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Povolení reverzního proxy serveru a/nebo DNS
Služby, které se vzájemně připojují v rámci clusteru, můžou mít přímý přístup k koncovým bodům jiných služeb, protože uzly v clusteru jsou ve stejné místní síti. Aby bylo snazší se připojit mezi službami, Service Fabric poskytuje další služby: [službu DNS](service-fabric-dnsservice.md) a [službu reverzního proxy serveru](service-fabric-reverseproxy.md).  Při nasazování clusteru je možné povolit obě služby.

Vzhledem k tomu, že řada služeb, zejména služba s využitím kontejnerů, může mít existující název adresy URL, který je schopný vyřešit pomocí standardního protokolu DNS (místo Naming Service protokolu), zejména ve scénářích "výtah a Shift". To je přesně to, co dělá služba DNS. Umožňuje mapovat názvy DNS na název služby, a proto vyřešte IP adresy koncových bodů.

Servery reverzního proxy adresují služby v clusteru, které zveřejňují koncové body HTTP (včetně protokolu HTTPS). Reverzní proxy značně zjednodušují volání dalších služeb poskytnutím specifického formátu identifikátoru URI.  Reverzní proxy server také zpracovává kroky vyřešit, připojit a opakovat vyžadované pro komunikaci jedné služby s jinou službou.

## <a name="prepare-for-disaster-recovery"></a>Příprava na zotavení po havárii
Kritická součást poskytování vysoké dostupnosti zajišťuje, aby služby mohly zaručovat všechny různé typy selhání. To je obzvláště důležité pro neplánované chyby a mimo vaši kontrolu. [Příprava na zotavení po havárii](service-fabric-disaster-recovery.md) popisuje některé běžné režimy selhání, které by mohly selhat, pokud není modelem a spravováno správně. Zabývá se taky riziky a akcemi, které se mají provést, pokud se přesto stala havárie.

## <a name="production-readiness-checklist"></a>Kontrolní seznam připravenosti k produkci
Je vaše aplikace a cluster připravený přijmout provozní provoz? Před nasazením clusteru do produkčního prostředí spouštějte prostřednictvím [kontrolního seznamu připravenosti na produkci](service-fabric-production-readiness-checklist.md). Sledujte položky v tomto kontrolním seznamu a sledujte tak bezproblémové fungování aplikace a clusteru. Než se pustíte do produkčního prostředí, důrazně doporučujeme, abyste všechny tyto položky kontrolovali.

## <a name="next-steps"></a>Další kroky
* [Vytvoření clusteru Service Fabric se systémem Windows](./service-fabric-best-practices-security.md)
* [Vytvoření clusteru Service Fabric se systémem Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster