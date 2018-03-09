---
title: "Disky systému Azure pomocí AKS"
description: "Disky systému Azure pomocí AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 42814c86fdde32c99f1b5a27748aac69dfb99d96
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="volumes-with-azure-disks"></a>Svazky s disky systému Azure

Aplikace založené na kontejneru často potřebují přístup a uchovávat data v svazku externí data. Disky systému Azure můžete použít jako tohle úložiště dat externí. Tento článek údaje pomocí Azure disku jako svazek Kubernetes na clusteru Azure Container Service (AKS).

Další informace o svazcích Kubernetes najdete v tématu [Kubernetes svazky][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Vytvoření Azure disku

Před připojení Azure spravované disk jako svazek Kubernetes disk, musí existovat ve stejné skupině prostředků jako AKS prostředků clusteru. K vyhledání této skupiny prostředků, použijte [seznam skupiny az] [ az-group-list] příkaz.

```azurecli-interactive
az group list --output table
```

Hledáte skupinu prostředků s názvem podobná `MC_clustername_clustername_locaton`, kde clustername představuje název clusteru AKS a umístění je oblast Azure, kde byla nasazena do clusteru.

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Použití [vytvoření disku az] [ az-disk-create] příkaz pro vytvoření disku s Azure. 

V tomto příkladu, aktualizovat `--resource-group` s názvem skupiny prostředků a `--name` název svého výběru.

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Po vytvoření disku, měli byste vidět výstup podobný následujícímu. Tato hodnota je ID disku, který je použit při připojení k disku a Kubernetes pod.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-file-share-as-volume"></a>Připojení sdílené složky jako svazek

Připojte Azure disk do vaší pod konfigurací svazku ve specifikaci kontejneru. 

Vytvořte nový soubor s názvem `azure-disk-pod.yaml` s tímto obsahem. Aktualizace `diskName` s názvem nově vytvořený disku a `diskURI` s ID disku. Také si poznamenejte `mountPath`, jedná se o cestu, kde je Azure disk připojený v pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: neilpeterson/aks-helloworld
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

K vytvoření pod použijte kubectl.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Nyní máte spuštěný pod s diskem Azure připojen `/mnt/azure`.

## <a name="next-steps"></a>Další postup

Další informace o Kubernetes svazky s využitím disky systému Azure.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro disky Azure][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
