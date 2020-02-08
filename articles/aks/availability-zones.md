---
title: Použití zón dostupnosti ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit cluster, který distribuuje uzly napříč zónami dostupnosti ve službě Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.custom: fasttrack-edit
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: mlearned
ms.openlocfilehash: b73cb09f95fa2b23fb23fb719fe57143e1731ceb
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086521"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Vytvoření clusteru služby Azure Kubernetes (AKS), který používá zóny dostupnosti

Cluster Azure Kubernetes Service (AKS) distribuuje prostředky, jako jsou uzly a úložiště napříč logickými oddíly základní výpočetní infrastruktury Azure. Tento model nasazení zajišťuje, že se uzly spouštějí napříč samostatnými doménami aktualizace a selhání v jednom datovém centru Azure. Clustery AKS nasazené s tímto výchozím chováním poskytují vysokou úroveň dostupnosti pro ochranu proti selhání hardwaru nebo plánované události údržby.

Pokud chcete zajistit vyšší úroveň dostupnosti pro vaše aplikace, clustery AKS se dají distribuovat napříč zónami dostupnosti. Tyto zóny jsou fyzicky oddělená datacentry v dané oblasti. Pokud jsou komponenty clusteru distribuované napříč několika zónami, může cluster AKS tolerovat selhání v jedné z těchto zón. Vaše aplikace a operace správy jsou dál dostupné i v případě, že je v jednom datovém centru nějaký problém.

V tomto článku se dozvíte, jak vytvořit cluster AKS a distribuovat součásti uzlu napříč zónami dostupnosti.

## <a name="before-you-begin"></a>Před zahájením

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.76 nebo novější. Pro nalezení verze spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Omezení a dostupnost oblasti

Clustery AKS se teď dají vytvářet pomocí zón dostupnosti v následujících oblastech:

* Střed USA
* Východní USA 2
* USA – východ
* Francie – střed
* Japonsko – východ
* Severní Evropa
* Jihovýchodní Asie
* Velká Británie – jih
* Západní Evropa
* USA – západ 2

Při vytváření clusteru AKS pomocí zón dostupnosti platí následující omezení:

* Zóny dostupnosti můžete povolit jenom při vytváření clusteru.
* Po vytvoření clusteru se nastavení zóny dostupnosti nedá aktualizovat. Nemůžete také aktualizovat existující cluster zóny bez dostupnosti, aby bylo možné používat zóny dostupnosti.
* Po vytvoření se zóny dostupnosti pro cluster AKS nedají vypnout.
* Vybraná velikost uzlu (SKU virtuálního počítače) musí být dostupná ve všech zónách dostupnosti.
* Clustery s povolenými zónami dostupnosti vyžadují pro distribuci mezi zónami použití služby Azure Load Balancer úrovně Standard.
* Chcete-li nasadit standardní nástroje pro vyrovnávání zatížení, musíte použít Kubernetes verze 1.13.5 nebo vyšší.

Clustery AKS, které používají zóny dostupnosti, musí používat *standardní* SKU nástroje pro vyrovnávání zatížení Azure, což je výchozí hodnota pro typ nástroje pro vyrovnávání zatížení. Tento typ nástroje pro vyrovnávání zatížení se dá definovat jenom v době vytváření clusteru. Další informace a omezení standardního nástroje pro vyrovnávání zatížení najdete v tématu [omezení standardních SKU služby Azure Load Balancer][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Omezení disků Azure

Svazky, které používají službu Azure Managed disks, nejsou aktuálně rozpracovatelné prostředky. Lusky Přeplánované v jiné zóně z původní zóny se nemůžou znovu připojit k předchozím diskům. Doporučuje se spouštět bezstavové úlohy, které nevyžadují trvalé úložiště, které se může nacházet v případě problémů s oblastmi.

Pokud musíte spustit stavové úlohy, pomocí specifikací v poli pod a jejich tolerování sdělte nástroji Scheduler Kubernetes, aby vytvořil lusky ve stejné zóně jako vaše disky. Alternativně můžete použít síťové úložiště, například soubory Azure, které se mohou připojit k luskům při jejich plánování mezi zónami.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Přehled zón dostupnosti pro clustery AKS

Zóny dostupnosti jsou nabídka s vysokou dostupností, která chrání vaše aplikace a data před selháním datových center. Zóny jsou jedinečné fyzické umístění v oblasti Azure. Každá zóna se skládá z jednoho nebo více datových Center vybavených nezávislým napájením, chlazením a sítí. Aby se zajistila odolnost, existuje minimálně tři samostatné zóny ve všech povolených oblastech. Fyzické oddělení zón dostupnosti v rámci oblasti chrání aplikace a data před selháními datových center. Redundantní služby v zóně replikují aplikace a data napříč zónami dostupnosti, aby se chránily před jednotlivými chybami.

Další informace najdete v tématu [co jsou zóny dostupnosti v Azure?][az-overview].

Clustery AKS, které jsou nasazené pomocí zón dostupnosti, můžou distribuovat uzly napříč několika zónami v rámci jedné oblasti. Například cluster v oblasti *Východní USA 2* může vytvořit uzly ve všech třech zónách dostupnosti v *východní USA 2*. Tato distribuce prostředků clusteru AKS vylepšuje dostupnost clusteru, protože je odolná vůči selhání konkrétní zóny.

![Distribuce uzlů AKS napříč zónami dostupnosti](media/availability-zones/aks-availability-zones.png)

V případě výpadku zóny se uzly dají znovu vyrovnávat ručně nebo pomocí automatického škálování clusteru. Pokud se jedna zóna stane nedostupnou, vaše aplikace se budou dál spouštět.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Vytvoření clusteru AKS napříč zónami dostupnosti

Když vytvoříte cluster pomocí příkazu [AZ AKS Create][az-aks-create] , definuje parametr `--zones`, do kterého se nasadí uzly agenta. Komponenty roviny ovládacího prvku AKS pro váš cluster jsou rozloženy i mezi zóny v nejvyšší dostupné konfiguraci při definování parametru `--zones` v době vytváření clusteru.

Pokud při vytváření clusteru AKS nedefinujete žádné zóny pro výchozí fond agentů, nepoužijí komponenty řízení AKS pro váš cluster zóny dostupnosti. Další fondy uzlů můžete přidat pomocí příkazu [AZ AKS nodepool Add][az-aks-nodepool-add] a zadáním `--zones` pro tyto nové uzly, ale komponenty roviny ovládacího prvku zůstanou bez vědomí zóny dostupnosti. Po nasazení nemůžete změnit povědomí o zónách pro fond uzlů nebo AKS součásti ovládacího prvku.

Následující příklad vytvoří cluster AKS s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*. Vytvoří se celkem *3* uzlů – jeden agent v zóně *1*, jeden v *2*a potom jeden ve *3*. Komponenty roviny ovládacího prvku AKS jsou také distribuovány mezi zóny v nejvyšší dostupné konfiguraci, protože jsou definovány jako součást procesu vytváření clusteru.

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

## <a name="verify-node-distribution-across-zones"></a>Ověření distribuce uzlu mezi zónami

Až bude cluster připravený, zobrazte seznam uzlů agentů v sadě škálování, abyste viděli, do jaké zóny dostupnosti se nasazují.

Nejdřív Získejte přihlašovací údaje clusteru AKS pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

V dalším kroku pomocí příkazu [kubectl popsat][kubectl-describe] seznam uzlů v clusteru. Vyfiltrujte hodnotu *Failure-Domain.beta.Kubernetes.IO/Zone* , jak je znázorněno v následujícím příkladu:

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

Všimněte si, že v novějších verzích Kubernetes (1.17.0 a novějších) používá AKS kromě zastaralých `failure-domain.beta.kubernetes.io/zone`novější popisek `topology.kubernetes.io/zone`.

## <a name="verify-pod-distribution-across-zones"></a>Ověřit distribuci pod mezi zónami

Jak je uvedeno v [dobře známých popisech, poznámkách a][kubectl-well_known_labels]přístupních, Kubernetes používá popisek `failure-domain.beta.kubernetes.io/zone` k automatické distribuci lusků do řadiče replikace nebo služby v různých zónách, které jsou k dispozici. Aby bylo možné tuto možnost otestovat, můžete rozšířit cluster na 3 až 5 uzlů a ověřit správnou rozprostření pod:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Pokud se operace škálování dokončí po několika minutách, příkaz `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` by měl poskytnout výstup podobný této ukázce:

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

Jak vidíte, teď máme na zónách 1 a 2 dva další uzly. Můžete nasadit aplikaci, která se skládá ze tří replik. NGINX budeme používat jako příklad:

```console
kubectl run nginx --image=nginx --replicas=3
```

Pokud ověříte, že jsou na uzlech, kde jsou spuštěné vaše lusky, vidíte, že lusky jsou spuštěné v luskech odpovídajících třem různým zónám dostupnosti. Například s příkazem `kubectl describe pod | grep -e "^Name:" -e "^Node:"` byste získali výstup podobný tomuto:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Jak vidíte z předchozího výstupu, první uzel pod je spuštěný v uzlu 0, který je umístěný v zóně dostupnosti `eastus2-1`. Druhý pod je spuštěn v uzlu 2, který odpovídá `eastus2-3`a třetí z nich v uzlu 4 v `eastus2-2`. Bez jakékoli další konfigurace Kubernetes rozšíří lusky správně ve všech třech zónách dostupnosti.

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
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
