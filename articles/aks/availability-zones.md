---
title: Použití zón dostupnosti ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit cluster, který distribuuje uzly mezi zóny dostupnosti ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596806"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Vytvoření clusteru služby Azure Kubernetes Service (AKS), který používá zóny dostupnosti

Cluster Azure Kubernetes Service (AKS) distribuuje prostředky, jako jsou uzly a úložiště napříč logickými částmi základní výpočetní infrastruktury Azure. Tento model nasazení zajišťuje, že uzly běží přes samostatné domény aktualizace a selhání v jednom datovém centru Azure. Clustery AKS nasazené s tímto výchozím chováním poskytují vysokou úroveň dostupnosti k ochraně proti selhání hardwaru nebo plánované události údržby.

Chcete-li poskytnout vyšší úroveň dostupnosti pro vaše aplikace, aks clustery mohou být distribuovány mezi zónami dostupnosti. Tyto zóny jsou fyzicky samostatná datová centra v rámci dané oblasti. Pokud jsou součásti clusteru distribuovány mezi více zón, váš cluster AKS je schopen tolerovat selhání v jedné z těchto zón. Vaše aplikace a operace správy jsou i nadále k dispozici i v případě, že má problém s jedním celým datovým centrem.

Tento článek ukazuje, jak vytvořit cluster AKS a distribuovat komponenty uzlu mezi zóny dostupnosti.

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalované a nakonfigurované azure CLI verze 2.0.76 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Omezení a dostupnost oblasti

Clustery AKS lze aktuálně vytvořit pomocí zón dostupnosti v následujících oblastech:

* USA – střed
* USA – východ 2
* USA – východ
* Francie – střed
* Japonsko – východ
* Severní Evropa
* Jihovýchodní Asie
* Spojené království – jih
* Západní Evropa
* USA – západ 2

Při vytváření clusteru AKS pomocí zón dostupnosti platí následující omezení:

* Zóny dostupnosti můžete povolit pouze při vytvoření clusteru.
* Nastavení zóny dostupnosti nelze po vytvoření clusteru aktualizovat. Nelze také aktualizovat existující cluster zón bez dostupnosti tak, aby používal zóny dostupnosti.
* Zóny dostupnosti pro cluster AKS nelze po jeho vytvoření zakázat.
* Vybraná velikost uzlu (SKU virtuálního počítače) musí být dostupná ve všech zónách dostupnosti.
* Clustery s povolenými zónami dostupnosti vyžadují použití standardních nástrojů pro vyrovnávání zatížení Azure pro distribuci napříč zónami.
* Chcete-li nasadit standardní vykladače zatížení, je nutné použít kubernetes verze 1.13.5 nebo vyšší.

Clustery AKS, které používají zóny dostupnosti, musí používat *standardní* skladovou položku pro vyrovnávání zatížení Azure, což je výchozí hodnota pro typ nástrojů pro vyrovnávání zatížení. Tento typ vykladače zatížení lze definovat pouze v době vytvoření clusteru. Další informace a omezení standardního vykladače zatížení naleznete [v tématu Azure vyrovnávání zatížení standardní omezení skladové položky][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Omezení disků Azure

Svazky, které používají spravované disky Azure, nejsou aktuálně zonální prostředky. Pody přeplánované v jiné zóně, než je jejich původní zóna, nemohou znovu připojit své předchozí disky. Doporučujeme spouštět bezstavové úlohy, které nevyžadují trvalé úložiště, které může narazit na problémy s zonálními.

Pokud je nutné spustit stavové úlohy, použijte skvrny a tolerance ve specifikacích podu a sdělte plánovači Kubernetes k vytvoření podů ve stejné zóně jako vaše disky. Případně můžete použít síťové úložiště, jako jsou soubory Azure, které se můžou připojit k podům, jak jsou naplánované mezi zónami.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Přehled zón dostupnosti pro clustery AKS

Zóny dostupnosti je nabídka s vysokou dostupností, která chrání vaše aplikace a data před selháním datového centra. Zóny jsou jedinečná fyzická umístění v rámci oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Aby byla zajištěna odolnost proti chybám, jsou ve všech povolených oblastech minimálně tři samostatné zóny. Fyzické oddělení zón dostupnosti v rámci oblasti chrání aplikace a data před selháním datacenter. Zónově redundantní služby replikují vaše aplikace a data napříč zónami dostupnosti, aby byly chráněny před jediným bodem selhání.

Další informace najdete v tématu [Co jsou zóny dostupnosti v Azure?][az-overview].

Clustery AKS, které jsou nasazeny pomocí zón dostupnosti, mohou distribuovat uzly mezi více zón v rámci jedné oblasti. Například cluster v oblasti *VÝCHODNÍ USA 2* můžete vytvořit uzly ve všech třech zónách dostupnosti v *usa 2 východ*. Toto rozdělení prostředků clusteru AKS zlepšuje dostupnost clusteru, protože jsou odolné vůči selhání určité zóny.

![Distribuce uzlů AKS mezi zónami dostupnosti](media/availability-zones/aks-availability-zones.png)

Při výpadku zóny lze uzly ručně vyvážit nebo pomocí automatického škálovače clusteru. Pokud jedna zóna přestane být k dispozici, budou aplikace nadále spuštěny.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Vytvoření clusteru AKS napříč zónami dostupnosti

Když vytvoříte cluster pomocí příkazu [az aks create,][az-aks-create] `--zones` parametr definuje, do kterých uzlů agentů zón se nasadí. Součásti roviny řízení AKS pro váš cluster jsou také rozloženy `--zones` mezi zóny v nejvyšší dostupné konfiguraci, když definujete parametr v době vytvoření clusteru.

Pokud při vytváření clusteru AKS nedefinujete žádné zóny pro výchozí fond agentů, součásti roviny ovládacího prvku AKS pro váš cluster nebudou používat zóny dostupnosti. Můžete přidat další fondy uzlů pomocí příkazu [az aks nodepool add][az-aks-nodepool-add] a určit `--zones` pro tyto nové uzly, ale součásti roviny ovládacího prvku zůstanou bez povědomí o zóně dostupnosti. Povědomí o zónách pro fond uzlů nebo součásti roviny ovládacího prvku AKS nelze po jejich nasazení změnit.

Následující příklad vytvoří cluster AKS s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*. Celkem jsou vytvořeny *3* uzly - jeden agent v zóně *1*, jeden z *2*a pak jeden ze *3*. Součásti roviny ovládacího prvku AKS jsou také distribuovány mezi zónami v nejvyšší dostupné konfiguraci, protože jsou definovány jako součást procesu vytváření clusteru.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Vytvoření clusteru AKS trvá několik minut.

## <a name="verify-node-distribution-across-zones"></a>Ověření rozdělení uzlů mezi zónami

Až bude cluster připravený, uveďte uzly agenta v škálovací sadě, abyste zjistili, v jaké zóně dostupnosti jsou nasazeny.

Nejprve získejte pověření clusteru AKS pomocí příkazu [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Dále použijte příkaz [kubectl describe][kubectl-describe] k zobrazení uzlů v clusteru. Filtrujte hodnotu *failure-domain.beta.kubernetes.io/zone,* jak je znázorněno v následujícím příkladu:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Následující příklad výstupu ukazuje tři uzly distribuované mezi zadanou oblast a zóny dostupnosti, jako je *například eastus2-1* pro první zónu dostupnosti a *eastus2-2* pro druhou zónu dostupnosti:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Při přidávání dalších uzlů do fondu agentů platforma Azure automaticky distribuuje základní virtuální počítače přes zadané zóny dostupnosti.

Všimněte si, že v novějších verzích Kubernetes (1.17.0 a `topology.kubernetes.io/zone` novější) používá AKS kromě zastaralého štítku novější `failure-domain.beta.kubernetes.io/zone`popisek.

## <a name="verify-pod-distribution-across-zones"></a>Ověření distribuce podů mezi zónami

Jak je popsáno ve [známých popiscích, poznámkách a vlastnostech][kubectl-well_known_labels], Kubernetes používá `failure-domain.beta.kubernetes.io/zone` popisek k automatické distribuci podů v řadiči replikace nebo ve službě v různých dostupných zónách. Chcete-li to otestovat, můžete vertikálně navýšit kapacitu clusteru ze 3 na 5 uzlů, abyste ověřili správné rozprostření podu:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Po dokončení operace škálování po několika `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` minutách by měl příkaz poskytnout výstup podobný této ukázce:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Jak můžete vidět, nyní máme dva další uzly v zónách 1 a 2. Můžete nasadit aplikaci skládající se ze tří replik. Jako příklad použijeme NGINX:

```console
kubectl run nginx --image=nginx --replicas=3
```

Pokud ověříte, že uzly, kde jsou spuštěny pody, uvidíte, že pody jsou spuštěny na pody odpovídající tři různé zóny dostupnosti. Například s `kubectl describe pod | grep -e "^Name:" -e "^Node:"` příkazem byste získat výstup podobný tomuto:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Jak můžete vidět z předchozího výstupu, první pod je spuštěn na uzlu `eastus2-1`0, který se nachází v zóně dostupnosti . Druhý pod je spuštěn na uzlu 2, `eastus2-3`který odpovídá , a třetí `eastus2-2`v uzlu 4, v . Bez jakékoli další konfigurace Kubernetes správně šíří pody ve všech třech zónách dostupnosti.

## <a name="next-steps"></a>Další kroky

Tento článek podrobně popisuje, jak vytvořit cluster AKS, který používá zóny dostupnosti. Další informace o vysoce dostupných clusterech naleznete v [tématu Doporučené postupy pro kontinuitu provozu a zotavení po havárii v AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
