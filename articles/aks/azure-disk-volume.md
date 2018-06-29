---
title: Disky systému Azure pomocí AKS
description: Disky systému Azure pomocí AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: cfad5ebd1212df03cee86b71340d8a73c2594f57
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096089"
---
# <a name="volumes-with-azure-disks"></a>Svazky s disky systému Azure

Aplikace založené na kontejneru často potřebují přístup a uchovávat data v svazku externí data. Disky systému Azure můžete použít jako tohle úložiště dat externí. Tento článek údaje pomocí Azure disku jako svazek Kubernetes v clusteru služby Azure Kubernetes služby (AKS).

Další informace o svazcích Kubernetes najdete v tématu [Kubernetes svazky][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Vytvoření Azure disku

Před připojení k Azure spravované disk jako svazek Kubernetes, musí existovat na disku v AKS **uzlu** skupinu prostředků. Získat název skupiny prostředků s [az prostředků zobrazit] [ az-resource-show] příkaz.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Použití [vytvoření disku az] [ az-disk-create] příkaz pro vytvoření disku s Azure.

Aktualizace `--resource-group` s názvem skupiny prostředků získané v posledním kroku, a `--name` název svého výběru.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Po vytvoření disku, měli byste vidět výstup podobný následujícímu. Tato hodnota je ID disku, který je použit při připojení disku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Připojte disk jako svazek

Připojte Azure disk do vaší pod konfigurací svazku ve specifikaci kontejneru.

Vytvořte nový soubor s názvem `azure-disk-pod.yaml` s tímto obsahem. Aktualizace `diskName` s názvem nově vytvořený disku a `diskURI` s ID disku. Také si poznamenejte `mountPath`, což je cestu, kde je připojené Azure disku pod.

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
[az-resource-show]: /cli/azure/resource#az-resource-show
