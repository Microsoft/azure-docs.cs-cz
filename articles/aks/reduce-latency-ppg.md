---
title: Snížení latence pro clustery Azure Kubernetes Service (AKS) pomocí skupin umístění blízkosti
description: Naučte se používat skupiny umístění pro Proximity k omezení latence pro úlohy clusteru AKS.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/22/2020
ms.openlocfilehash: f5b87d048127bf856a5a683397540769a7040fd6
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323437"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>Snížení latence se skupinami umístění blízkosti (Preview)

> [!Note]
> Když použijete skupiny umístění pro Proximity s AKS, bude se toto umístění vztahovat jenom na uzly agenta. Vylepšení uzlu na uzel a odpovídající hostované latenci pod Společné umístění nemá vliv na umístění řídicí plochy clusteru.

Při nasazování aplikace v Azure dojde k rozšiřování instancí virtuálních počítačů v různých oblastech nebo zónách dostupnosti k zajištění latence sítě, což může mít vliv na celkový výkon vaší aplikace. Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že jsou výpočetní prostředky Azure fyzicky umístěné blízko sebe. Některé aplikace, jako jsou hraní her, metodologie a vysoká frekvence obchodování (HFT), vyžadují nízkou latenci a úlohy, které se rychle dokončí. U scénářů s vysokým výkonem (HPC), jako jsou třeba, zvažte použití [skupin umístění blízkosti](https://docs.microsoft.com/azure/virtual-machines/linux/co-location#proximity-placement-groups) pro fondy uzlů clusteru.

## <a name="limitations"></a>Omezení

* Skupina umístění blízkosti zahrnuje jednu zónu dostupnosti.
* Neexistuje žádná aktuální podpora pro clustery AKS, které používají skupiny dostupnosti virtuálních počítačů.
* Stávající fondy uzlů nejde změnit tak, aby používaly skupinu umístění s blízkostí.

> [!IMPORTANT]
> Funkce AKS ve verzi Preview jsou k dispozici na samoobslužné službě, na základě souhlasu. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepších úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace najdete v následujících článcích podpory:
>
> - [Zásady podpory AKS](support-policies.md)
> - [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="before-you-begin"></a>Než začnete

Musíte mít nainstalované následující zdroje:

- Rozšíření AKS-Preview 0.4.53

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>Nastavte funkci Preview pro skupiny umístění pro Proximity.

> [!IMPORTANT]
> Když použijete skupiny umístění pro Proximity s AKS, bude se toto umístění vztahovat jenom na uzly agenta. Vylepšení uzlu na uzel a odpovídající hostované latenci pod Společné umístění nemá vliv na umístění řídicí plochy clusteru.

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

Registrace může trvat několik minut. Pomocí níže uvedeného příkazu ověřte, že je funkce zaregistrovaná:

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

Během období Preview budete potřebovat rozšíření CLI *AKS-Preview* , které používá skupiny umístění pro Proximity. Použijte příkaz [AZ Extension Add][az-extension-add] a potom vyhledejte všechny dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
## <a name="node-pools-and-proximity-placement-groups"></a>Fondy uzlů a skupiny umístění blízkosti

První prostředek, který nasadíte se skupinou umístění blízkosti, se připojí ke konkrétnímu datovému centru. Další prostředky nasazené se stejnou skupinou umístění blízkosti se nacházejí ve stejném datovém centru. Jakmile se všechny prostředky, které používají skupinu umístění blízkosti, se zastavily (oddělují) nebo se odstraní, už se nepřipojí.

* K jedné skupině umístění blízkosti se dá přidružit mnoho fondů uzlů.
* Fond uzlů může být přidružen pouze k jedné skupině umístění blízkosti.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Vytvoří nový cluster AKS se skupinou umístění blízkosti.

Následující příklad používá příkaz [AZ Group Create][az-group-create] k vytvoření skupiny prostředků s názvem *myResourceGroup* v oblasti *centralus* . Pomocí příkazu [AZ AKS Create][az-aks-create] se pak vytvoří cluster AKS s názvem *myAKSCluster* . 

Urychlené síťové služby významně zlepšují výkon sítě virtuálních počítačů. V ideálním případě používejte skupiny umístění pro Proximity ve spojení s akcelerovanými síťovými službami. Ve výchozím nastavení používá AKS k dispozici akcelerované síťové služby na [podporovaných instancích virtuálních počítačů](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), které zahrnují většinu virtuálních počítačů Azure se dvěma nebo více vCPU.

Vytvořte nový cluster AKS se skupinou umístění blízkosti:

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
# Create an AKS cluster that uses a proximity placement group for the initial node pool
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
[proximity-placement-groups]: /virtual-machines/windows/co-location.md#proximity-placement-groups/virtual-machines/linux/co-location
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete

