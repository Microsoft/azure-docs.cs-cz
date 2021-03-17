---
title: Snížení latence pro clustery Azure Kubernetes Service (AKS) pomocí skupin umístění blízkosti
description: Naučte se používat skupiny umístění pro Proximity k omezení latence pro úlohy clusteru AKS.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: c0c1bf83bf5a816debe61cab0ceab856bfbd062f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181172"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Snížení latence se skupinami umístění blízkosti

> [!Note]
> Při použití skupin umístění blízkosti na AKS se společné umístění týká pouze uzlů agentů. Vylepšení uzlu na uzel a odpovídající hostované latenci pod Společné umístění nemá vliv na umístění řídicí plochy clusteru.

Při nasazování aplikace v Azure dojde k rozšiřování instancí virtuálních počítačů v různých oblastech nebo zónách dostupnosti k zajištění latence sítě, což může mít vliv na celkový výkon vaší aplikace. Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že jsou výpočetní prostředky Azure fyzicky umístěné blízko sebe. Některé aplikace, jako jsou hraní her, metodologie a vysoká frekvence obchodování (HFT), vyžadují nízkou latenci a úlohy, které se rychle dokončí. U scénářů s vysokým výkonem (HPC), jako jsou třeba, zvažte použití [skupin umístění blízkosti](../virtual-machines/co-location.md#proximity-placement-groups) (PPG) pro fondy uzlů vašeho clusteru.

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje, abyste spustili Azure CLI verze 2,14 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

### <a name="limitations"></a>Omezení

* Skupina umístění blízkosti se může mapovat na maximálně jednu zónu dostupnosti.
* Fond uzlů musí použít Virtual Machine Scale Sets k přidružení skupiny umístění blízkosti.
* Fond uzlů může přidružit skupinu umístění blízkosti pouze při vytváření fondu uzlů.

## <a name="node-pools-and-proximity-placement-groups"></a>Fondy uzlů a skupiny umístění blízkosti

První prostředek, který nasadíte se skupinou umístění blízkosti, se připojí ke konkrétnímu datovému centru. Další prostředky nasazené se stejnou skupinou umístění blízkosti se nacházejí ve stejném datovém centru. Jakmile se všechny prostředky, které používají skupinu umístění blízkosti, se zastavily (oddělují) nebo se odstraní, už se nepřipojí.

* K jedné skupině umístění blízkosti se dá přidružit mnoho fondů uzlů.
* Fond uzlů může být přidružen pouze k jedné skupině umístění blízkosti.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Konfigurace skupin umístění blízkosti pomocí zón dostupnosti

> [!NOTE]
> I když skupiny umístění blízkosti vyžadují, aby fond uzlů používal maximálně jednu zónu dostupnosti, je pro virtuální počítače v jedné zóně stále platná [smlouva SLA pro virtuální počítače Azure s 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) .

Skupiny umístění blízkosti jsou koncept fondu uzlů a přidružený ke každému fondu jednotlivých uzlů. Použití prostředku PPG nemá žádný vliv na dostupnost roviny ovládacího prvku AKS. To může mít vliv na to, jak by měl být cluster navržený pomocí zón. Pokud chcete zajistit, aby byl cluster rozložen mezi více zón, doporučujeme následující návrh.

* Zřídí cluster s prvním fondem systému pomocí 3 zón a není přidružená žádná skupina umístění blízkosti. Tím se zajistí, že se systémová LUSKOVÁ půda nachází ve vyhrazeném fondu uzlů, který se rozprostře mezi několik zón.
* Přidejte další fondy uživatelských uzlů s jedinečnou skupinou umístění a umístění blízkosti, která je přidružená ke každému fondu. Příkladem je nodepool1 v zóně 1 a PPG1, nodepool2 v zóně 2 a PPG2, nodepool3 v zóně 3 s PPG3. Tím zajistíte, že uzly budou rozloženy mezi více zón a každý fond uzlů je umístěn v určené zóně s vyhrazeným prostředkem PPG.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Vytvoří nový cluster AKS se skupinou umístění blízkosti.

Následující příklad používá příkaz [AZ Group Create][az-group-create] k vytvoření skupiny prostředků s názvem *myResourceGroup* v oblasti *centralus* . Pomocí příkazu [AZ AKS Create][az-aks-create] se pak vytvoří cluster AKS s názvem *myAKSCluster* .

Urychlené síťové služby významně zlepšují výkon sítě virtuálních počítačů. V ideálním případě používejte skupiny umístění pro Proximity ve spojení s akcelerovanými síťovými službami. Ve výchozím nastavení používá AKS k dispozici akcelerované síťové služby na [podporovaných instancích virtuálních počítačů](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), které zahrnují většinu virtuálních počítačů Azure se dvěma nebo více vCPU.

Vytvořte nový cluster AKS se skupinou umístění blízkosti přidruženou k prvnímu fondu uzlů systému:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Spusťte následující příkaz a uložte vrácené ID:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

Příkaz vytvoří výstup, který obsahuje hodnotu *ID* , kterou potřebujete pro nadcházející příkazy rozhraní příkazového řádku:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

V níže uvedeném příkazu použijte ID prostředku skupiny umístění blízkosti pro hodnotu *myPPGResourceID* :

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Přidání skupiny umístění blízkosti do existujícího clusteru

Skupinu umístění blízkosti můžete přidat do existujícího clusteru vytvořením nového fondu uzlů. Pak můžete volitelně migrovat existující úlohy do nového fondu uzlů a pak odstranit původní fond uzlů.

Použijte stejnou skupinu umístění pro Proximity, kterou jste vytvořili dříve, a zajistěte, aby uzly agenta v obou fondech uzlů v clusteru AKS byly fyzicky umístěné ve stejném datovém centru.

Použijte ID prostředku ze skupiny umístění blízkosti, kterou jste vytvořili dříve, a přidejte nový fond uzlů pomocí [`az aks nodepool add`][az-aks-nodepool-add] příkazu:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Vyčištění

Pokud chcete cluster odstranit, pomocí [`az group delete`][az-group-delete] příkazu odstraňte skupinu prostředků AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [skupinách umístění blízkosti][proximity-placement-groups].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete