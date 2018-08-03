---
title: Použití disků v Azure s AKS
description: Použití disků v Azure s AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: aa9b92df84a48ef4cb706e9e89e0f6c0a25cd42a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420488"
---
# <a name="volumes-with-azure-disks"></a>Svazky s disků v Azure

Kontejnerových aplikací často potřebují přístup k a zachovat data ve svazku externí data. Disky Azure může sloužit jako úložiště tuto externí data. Tento článek podrobně popisuje použití Azure disk jako svazek Kubernetes v clusteru služby Azure Kubernetes Service (AKS).

Další informace o Kubernetes svazky, naleznete v tématu [Kubernetes svazky][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Vytvořte disk s Azure

Před připojením Azure managed Disks jako svazek Kubernetes, musí existovat na disku AKS **uzel** skupinu prostředků. Získání názvu skupiny prostředků s [az resource show] [ az-resource-show] příkazu.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Použití [az disk vytvořit] [ az-disk-create] příkaz pro vytvoření disku Azure.

Aktualizace `--resource-group` s názvem skupiny prostředků získané v předchozím kroku, a `--name` název podle vašeho výběru.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Po vytvoření disku, byste měli vidět výstup podobný následujícímu. Tato hodnota je Identifikátor disku, který je použit při připojování disku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```
> [!NOTE]
> Spravované disky Azure se účtují po SKU pro určité velikosti. Tyto skladové položky sahají od 32GiB disků S4 nebo P4 4TiB S50 nebo P50 disků. Kromě toho propustnost a výkon vstupně-výstupních operací na úrovni Premium managed disku závisí na SKU a velikosti instance uzlů v clusteru AKS. Zobrazit [ceny a výkonu spravované disky][managed-disk-pricing-performance].

> [!NOTE]
> Pokud je potřeba vytvořit disk v samostatné skupiny prostředků, musíte také přidat instanční objekt služby Azure Kubernetes Service (AKS) je pro váš cluster do skupiny prostředků obsahující disk s `Contributor` role. 
>

## <a name="mount-disk-as-volume"></a>Připojení disku jako svazek

Připojení Azure disku do podu pomocí konfigurace svazku v kontejneru specifikace.

Vytvořte nový soubor s názvem `azure-disk-pod.yaml` s následujícím obsahem. Aktualizace `diskName` s název nově vytvořeného disku a `diskURI` ID disku. Také poznamenejte si hodnotu `mountPath`, což je cesta kde je Azure disk připojený v pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Můžete vytvořit pod kubectl.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Teď máte spuštěné pod s připojený k disku Azure `/mnt/azure`.

## <a name="next-steps"></a>Další postup

Další informace o Kubernetes svazky s využitím disků v Azure.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro disky Azure][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
