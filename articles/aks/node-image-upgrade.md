---
title: Upgrade imagí uzlu služby Azure Kubernetes Service (AKS)
description: Naučte se upgradovat image na uzlech clusteru AKS a fondech uzlů.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 83d7d48922806334e2b49494fe0ef1d15e1a7a6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96531475"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Upgrade image uzlu služby Azure Kubernetes (AKS)

AKS podporuje upgrade imagí na uzlu, takže budete mít nejnovější aktualizace operačního systému a modulu runtime. AKS poskytuje za týden jednu novou image s nejnovějšími aktualizacemi, takže je vhodné pravidelně upgradovat image vašeho uzlu na nejnovější funkce, včetně aktualizací pro Linux nebo Windows. V tomto článku se dozvíte, jak upgradovat image uzlů clusteru AKS a jak aktualizovat image fondu uzlů bez upgradu verze Kubernetes.

Další informace o nejnovějších obrázcích, které poskytuje AKS, najdete v [poznámkách k verzi AKS](https://github.com/Azure/AKS/releases).

Informace o tom, jak upgradovat verzi Kubernetes pro váš cluster, najdete v tématu [upgrade clusteru AKS][upgrade-cluster].

> [!NOTE]
> Cluster AKS musí pro uzly používat sadu škálování virtuálních počítačů.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Ověřte, jestli je fond uzlů na nejnovější imagi uzlu.

Můžete zjistit, jaká je nejnovější verze image uzlu dostupná pro váš fond uzlů, a to pomocí následujícího příkazu: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

Ve výstupu vidíte `latestNodeImageVersion` podobně jako v následujícím příkladu:

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

Proto `nodepool1` je k dispozici nejnovější obrázek uzlu `AKSUbuntu-1604-2020.10.28` . Teď ji můžete porovnat s aktuální verzí image uzlu, kterou používá fond uzlů, a to spuštěním:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Příklad výstupu:

```output
"AKSUbuntu-1604-2020.10.08"
```

V tomto příkladu můžete upgradovat z aktuální `AKSUbuntu-1604-2020.10.08` verze image na nejnovější verzi `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Upgradovat všechny uzly ve všech fondech uzlů

Upgrade bitové kopie uzlu se provádí pomocí nástroje `az aks upgrade` . Chcete-li upgradovat image uzlu, použijte následující příkaz:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Během upgradu ověřte stav imagí uzlů pomocí následujícího `kubectl` příkazu, abyste získali popisky a vyfiltroval informace o aktuálním obrázku uzlu:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Po dokončení upgradu použijte `az aks show` k získání aktualizovaného podrobností o fondu uzlů. Ve vlastnosti se zobrazí aktuální obrázek uzlu `nodeImageVersion` .

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Upgrade konkrétního fondu uzlů

Upgrade image ve fondu uzlů je podobný jako upgrade image v clusteru.

Pokud chcete aktualizovat image operačního systému fondu uzlů bez provedení upgradu clusteru Kubernetes, použijte `--node-image-only` možnost v následujícím příkladu:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Během upgradu ověřte stav imagí uzlů pomocí následujícího `kubectl` příkazu, abyste získali popisky a vyfiltroval informace o aktuálním obrázku uzlu:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Po dokončení upgradu použijte `az aks nodepool show` k získání aktualizovaného podrobností o fondu uzlů. Ve vlastnosti se zobrazí aktuální obrázek uzlu `nodeImageVersion` .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Upgrade imagí uzlů pomocí přepětí uzlů

Chcete-li urychlit proces upgradu image uzlu, můžete upgradovat image uzlů pomocí přizpůsobitelné hodnoty přepětí uzlu. Ve výchozím nastavení používá AKS ke konfiguraci upgradů jeden další uzel.

Pokud byste chtěli zvýšit rychlost upgradů, použijte `--max-surge` hodnotu ke konfiguraci počtu uzlů, které se mají použít pro upgrady, aby byly dokončeny rychleji. Další informace o kompromisech různých `--max-surge` nastavení najdete v tématu [přizpůsobení upgradu uzlu přepětí][max-surge].

Následující příkaz nastaví maximální hodnotu nárůstu pro provádění upgradu bitové kopie uzlu:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Během upgradu ověřte stav imagí uzlů pomocí následujícího `kubectl` příkazu, abyste získali popisky a vyfiltroval informace o aktuálním obrázku uzlu:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Použijte `az aks nodepool show` k získání aktualizovaných podrobností o fondu uzlů. Ve vlastnosti se zobrazí aktuální obrázek uzlu `nodeImageVersion` .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Další kroky

- Informace o nejnovějších imagích uzlů najdete v [poznámkách k vydání verze AKS](https://github.com/Azure/AKS/releases) .
- Přečtěte si, jak upgradovat verzi Kubernetes pomocí [upgradu clusteru AKS][upgrade-cluster].
- [Automatické použití upgradu clusteru a fondu uzlů s akcemi GitHubu][github-schedule]
- Přečtěte si další informace o více fondech uzlů a o tom, jak upgradovat fondy uzlů pomocí [vytváření a správy fondů více uzlů][use-multiple-node-pools].

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
