---
title: Použití zón dostupnosti ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit cluster, který distribuuje uzly napříč zónami dostupnosti ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 4c5b0ceb3f8e0b96f18a67ed0c7dbf1b56ac30da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583543"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Vytvoření clusteru služby Azure Kubernetes (AKS), který používá zóny dostupnosti

Cluster Azure Kubernetes Service (AKS) distribuuje prostředky jako uzly a úložiště napříč logickými oddíly základní infrastruktury Azure. Tento model nasazení při použití zón dostupnosti zajišťuje, aby uzly v dané zóně dostupnosti byly fyzicky oddělené od těch, které jsou definované v jiné zóně dostupnosti. Clustery AKS nasazené s několika zónami dostupnosti nakonfigurovanými napříč clusterem poskytují vyšší úroveň dostupnosti pro ochranu proti selhání hardwaru nebo plánované události údržby.

Když v clusteru definujete fondy uzlů, které budou zahrnovat více zón, uzly v daném fondu uzlů budou moci i nadále pracovat i v případě, že jedna zóna zmizela. Vaše aplikace můžou být i nadále dostupné i v případě, že dojde k fyzickému selhání v jednom datovém centru, pokud se orchestruje pro tolerovat selhání podmnožiny uzlů.

V tomto článku se dozvíte, jak vytvořit cluster AKS a distribuovat součásti uzlu napříč zónami dostupnosti.

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.76 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Omezení a dostupnost oblasti

Clustery AKS se teď dají vytvářet pomocí zón dostupnosti v následujících oblastech:

* Austrálie – východ
* Brazílie – jih
* Střední Kanada
* Střední USA
* East US 
* USA – východ 2
* Francie – střed
* Německo – středozápad
* Japonsko – východ
* Severní Evropa
* Jihovýchodní Asie
* Středojižní USA
* Spojené království – jih
* USA (Gov) – Virginia
* West Europe
* Západní USA 2

Při vytváření clusteru AKS pomocí zón dostupnosti platí následující omezení:

* Zóny dostupnosti můžete definovat jenom v případě, že se vytváří cluster nebo fond uzlů.
* Po vytvoření clusteru se nastavení zóny dostupnosti nedá aktualizovat. Nemůžete také aktualizovat existující cluster zóny bez dostupnosti, aby bylo možné používat zóny dostupnosti.
* Vybraná velikost uzlu (SKU virtuálního počítače) musí být dostupná ve všech vybraných zónách dostupnosti.
* Clustery s povolenými zónami dostupnosti vyžadují pro distribuci mezi zónami použití služby Azure Load Balancer úrovně Standard. Tento typ nástroje pro vyrovnávání zatížení se dá definovat jenom v době vytváření clusteru. Další informace a omezení standardního nástroje pro vyrovnávání zatížení najdete v tématu [omezení standardních SKU služby Azure Load Balancer][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Omezení disků Azure

Svazky, které používají službu Azure Managed disks, nejsou momentálně redundantní prostředky v zóně. Svazky nelze připojit mezi zónami a musí být umístěny společně umístěné ve stejné zóně jako daný uzel, který je hostitelem cíle pod.

Kubernetes má informace o zónách dostupnosti Azure od verze 1,12. Objekt PersistentVolumeClaim, který odkazuje na spravovaný disk Azure, můžete nasadit v clusteru s více zónami AKS a [Kubernetes se postará o plánování](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) všech objektů pod tím, že tento trvalý virtuální okruh nárokuje ve správné zóně dostupnosti.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Přehled zón dostupnosti pro clustery AKS

Zóny dostupnosti jsou nabídka s vysokou dostupností, která chrání vaše aplikace a data při selhání datacentra. Zóny jsou jedinečné fyzické umístění v oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Aby se zajistila odolnost, existuje ve všech oblastech povolených v zóně vždycky více než jedna zóna. Fyzické oddělení zón dostupnosti v rámci oblasti chrání aplikace a data před selháním datacenter.

Další informace najdete v tématu [co jsou zóny dostupnosti v Azure?][az-overview].

Clustery AKS, které jsou nasazené pomocí zón dostupnosti, můžou distribuovat uzly napříč několika zónami v rámci jedné oblasti. Například cluster v oblasti  *východní USA 2*   může vytvářet uzly ve všech třech zónách dostupnosti v *východní USA 2*. Tato distribuce prostředků clusteru AKS vylepšuje dostupnost clusteru, protože je odolná vůči selhání konkrétní zóny.

![Distribuce uzlů AKS napříč zónami dostupnosti](media/availability-zones/aks-availability-zones.png)

Pokud se jedna zóna stane nedostupnou, aplikace se budou spouštět i v případě, že je cluster rozložený do několika zón.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Vytvoření clusteru AKS napříč zónami dostupnosti

Když vytvoříte cluster pomocí příkazu [AZ AKS Create][az-aks-create] , `--zones` určí parametr, do kterého se nasadí uzly agenta. Komponenty roviny ovládacího prvku, jako je etcd nebo rozhraní API, jsou rozloženy mezi dostupné zóny v oblasti, pokud definujete `--zones` parametr v době vytváření clusteru. Konkrétní zóny, které jsou rozloženy mezi komponenty roviny ovládacího prvku, jsou nezávisle na tom, jaké explicitní zóny jsou vybrány pro počáteční fond uzlů.

Pokud při vytváření clusteru AKS nedefinujete žádné zóny pro výchozí fond agentů, není zaručeno rozprostření komponent řídicí plochy mezi zónami dostupnosti. Další fondy uzlů můžete přidat pomocí příkazu [AZ AKS nodepool Add][az-aks-nodepool-add] a zadat `--zones` pro nové uzly, ale nezmění se způsob rozložení roviny ovládacího prvku mezi zónami. Nastavení zóny dostupnosti se dá definovat jenom v době vytváření clusteru nebo fondu uzlů.

Následující příklad vytvoří cluster AKS s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*. Vytvoří se celkem *3* uzlů – jeden agent v zóně *1*, jeden v *2* a potom jeden ve *3*.

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

Vytvoření clusteru AKS bude trvat několik minut.

Při rozhodování o tom, do jaké zóny má nový uzel patřit, bude daný fond uzlů AKS používat [nejlepší vyrovnávání zóny, které nabízí základní Azure Virtual Machine Scale Sets][vmss-zone-balancing]. Daný fond uzlů AKS se považuje za vyvážený, pokud má každá zóna stejný počet virtuálních počítačů nebo + \- 1 virtuální počítač ve všech ostatních zónách pro sadu škálování.

## <a name="verify-node-distribution-across-zones"></a>Ověření distribuce uzlu mezi zónami

Až bude cluster připravený, zobrazte seznam uzlů agentů v sadě škálování, abyste viděli, do jaké zóny dostupnosti se nasazují.

Nejdřív Získejte přihlašovací údaje clusteru AKS pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

V dalším kroku použijte příkaz [kubectl popsat][kubectl-describe] pro výpis uzlů v clusteru a filtr na hodnotu *Failure-Domain.beta.Kubernetes.IO/Zone* . Následující příklad je pro prostředí bash.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Následující příklad výstupu ukazuje tři uzly distribuované v rámci zadané oblasti a zón dostupnosti, jako je *eastus2-1* pro první zónu dostupnosti a *eastus2-2* pro druhou zónu dostupnosti:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Při přidávání dalších uzlů do fondu agentů platforma Azure automaticky distribuuje příslušné virtuální počítače v rámci zadané zóny dostupnosti.

Všimněte si, že v novějších verzích Kubernetes (1.17.0 a novějších) AKS používá novější popisek `topology.kubernetes.io/zone` kromě zastaralého `failure-domain.beta.kubernetes.io/zone` . Stejný výsledek můžete získat tak, že spustíte následující skript:

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

Což vám poskytne stručnější výstup:

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>Ověřit distribuci pod mezi zónami

Jak je uvedeno v [dobře známých popisech, poznámkách a rukopisech][kubectl-well_known_labels]Kubernetes používá `failure-domain.beta.kubernetes.io/zone` popisek k automatické distribuci lusků do řadiče replikace nebo služby v různých zónách, které jsou k dispozici. Aby bylo možné tuto možnost otestovat, můžete rozšířit cluster na 3 až 5 uzlů a ověřit správnou rozprostření pod:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Když se operace škálování dokončí po několika minutách, příkaz `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` v prostředí bash by měl poskytnout výstup podobný této ukázce:

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

V zónách 1 a 2 teď máme dva další uzly. Můžete nasadit aplikaci, která se skládá ze tří replik. NGINX budeme používat jako příklad:

```console
kubectl create deployment nginx --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
kubectl scale deployment nginx --replicas=3
```

Zobrazením uzlů, kde jsou vaše lusky spuštěné, vidíte, že se na uzlech, které odpovídají třem různým zónám dostupnosti, spouštějí lusky. Například s příkazem `kubectl describe pod | grep -e "^Name:" -e "^Node:"` v prostředí bash byste získali výstup podobný tomuto:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Jak vidíte předchozí výstup, první uzel pod je spuštěný v uzlu 0, který je umístěný v zóně dostupnosti `eastus2-1` . Druhý pod je spuštěn v uzlu 2, který odpovídá `eastus2-3` a třetí z nich v uzlu 4 v `eastus2-2` . Bez jakékoli další konfigurace Kubernetes rozšíří lusky správně ve všech třech zónách dostupnosti.

## <a name="next-steps"></a>Další kroky

V tomto článku se dozvíte, jak vytvořit cluster AKS, který používá zóny dostupnosti. Další informace o clusterech s vysokou dostupností najdete v tématu [osvědčené postupy pro kontinuitu podnikových procesů a zotavení po havárii v AKS][best-practices-bc-dr].

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
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/
