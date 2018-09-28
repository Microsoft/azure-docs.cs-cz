---
title: Vytvoření statické svazku pro podů ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak ručně vytvořit svazek pomocí disků v Azure pro použití s podů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: 20c7d20399392e653668953029bcb81886863ce4
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404615"
---
# <a name="manually-create-and-use-kubernetes-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ruční vytváření a používání Kubernetes svazku s disky Azure ve službě Azure Kubernetes Service (AKS)

Kontejnerových aplikací často potřebují přístup k a zachovat data ve svazku externí data. Disky Azure může sloužit jako úložiště tuto externí data. Ve službě AKS svazky, je možné vytvářet dynamicky pomocí deklarací identity trvalý svazek, nebo můžete ručně vytvořit a připojit disku Azure přímo. Tento článek ukazuje, jak ručně vytvořit disku Azure a připojit ho k pod ve službě AKS.

Další informace o Kubernetes svazky, naleznete v tématu [Kubernetes svazky][kubernetes-volumes].

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Také nutné mít Azure CLI verze 2.0.46 nebo později nainstalované a nakonfigurované. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="create-an-azure-disk"></a>Vytvořte disk s Azure

Při vytváření disku Azure pro použití službou AKS, můžete vytvořit prostředek disku v **uzel** skupinu prostředků. Tento přístup umožňuje přístup a Správa prostředků disku clusteru AKS. Pokud místo toho vytvořit na disku v samostatné skupiny prostředků, je nutné udělit instanční objekt služby Azure Kubernetes Service (AKS) pro váš cluster `Contributor` role do skupiny prostředků na disku.

Pro účely tohoto článku vytvořte na disku ve skupině prostředků uzlu. Nejprve získejte název skupiny prostředků s [az aks zobrazit] [ az-aks-show] příkaz a přidejte `--query nodeResourceGroup` parametr dotazu. Následující příklad získá uzlu skupiny prostředků pro AKS název clusteru *myAKSCluster* v názvu skupiny prostředků *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teď vytvořte disk s využitím [az disk vytvořit] [ az-disk-create] příkazu. Zadejte název skupiny prostředků uzlu získaný v předchozím příkazem a pak název prostředku disku, jako *myAKSDisk*. Následující příklad vytvoří *20*GiB disku a výstupy ID disku po vytvoření:

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Disky Azure se účtují po SKU pro určité velikosti. Tyto skladové položky sahají od 32GiB disků S4 nebo P4 8TiB S60 nebo P60 disků. Propustnost a výkon vstupně-výstupních operací na úrovni Premium spravovaného disku závisí na SKU a velikosti instance uzlů v clusteru AKS. Zobrazit [ceny a výkonu spravované disky][managed-disk-pricing-performance].

ID prostředku disku se zobrazí, jakmile se příkaz úspěšně dokončil, jak je znázorněno v následujícím příkladu výstupu. Toto ID disku se používá pro připojení disku v dalším kroku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Připojení disku jako svazek

Připojit Azure disk do podu, nakonfigurujte u svazku v kontejneru specifikace. Vytvořte nový soubor s názvem `azure-disk-pod.yaml` s následujícím obsahem. Aktualizace `diskName` s názvem disk vytvořený v předchozím kroku, a `diskURI` s ID disku znázorňuje výstup disku vytvořit příkaz. V případě potřeby aktualizovat `mountPath`, což je cesta kde je Azure disk připojený v pod.

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

Použití `kubectl` příkaz pro vytvoření pod.

```console
kubectl apply -f azure-disk-pod.yaml
```

Teď máte spuštěné pod s připojený k disku Azure `/mnt/azure`. Můžete použít `kubectl describe pod azure-disk-pod` disk je připojený úspěšně ověřit.

## <a name="next-steps"></a>Další postup

Další informace o službě AKS clustery interakci s disky Azure, najdete v článku [modul plug-in Kubernetes pro Azure Disks][kubernetes-disks].

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli