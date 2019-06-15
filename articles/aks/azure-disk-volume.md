---
title: Vytvoření statické svazku pro podů ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak ručně vytvořit svazek pomocí disků v Azure pro použití s pod ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: b166f70186b063782fb2c2245e351d6dfca6f978
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072157"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ruční vytváření a používání svazku s disky Azure ve službě Azure Kubernetes Service (AKS)

Kontejnerových aplikací často potřebují přístup k a zachovat data ve svazku externí data. Pokud jeden pod potřebuje přístup k úložišti, vám pomůže disků v Azure k dispozici nativní svazku při použití aplikace. Tento článek ukazuje, jak ručně vytvořit disku Azure a připojit ho k pod ve službě AKS.

> [!NOTE]
> Disk s Azure je možné připojit pouze pro jeden pod najednou. Pokud potřebujete sdílet mezi více podů trvalý svazek, použijte [Azure Files][azure-files-volume].

Další informace o Kubernetes svazky, naleznete v tématu [možnosti úložiště pro aplikace ve službě AKS][concepts-storage].

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Také nutné mít Azure CLI verze 2.0.59 nebo později nainstalované a nakonfigurované. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="create-an-azure-disk"></a>Vytvořte disk s Azure

Při vytváření disku Azure pro použití službou AKS, můžete vytvořit prostředek disku v **uzel** skupinu prostředků. Tento přístup umožňuje přístup a Správa prostředků disku clusteru AKS. Pokud místo toho vytvořit na disku v samostatné skupiny prostředků, je nutné udělit instanční objekt služby Azure Kubernetes Service (AKS) pro váš cluster `Contributor` role do skupiny prostředků na disku.

Pro účely tohoto článku vytvořte na disku ve skupině prostředků uzlu. Nejprve získejte název skupiny prostředků s [az aks zobrazit] [ az-aks-show] příkaz a přidejte `--query nodeResourceGroup` parametr dotazu. Následující příklad získá uzlu skupiny prostředků pro AKS název clusteru *myAKSCluster* v názvu skupiny prostředků *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teď vytvořte disk s využitím [az disk vytvořit] [ az-disk-create] příkazu. Zadejte název skupiny prostředků uzlu získaný v předchozím příkazem a pak název prostředku disku, jako *myAKSDisk*. Následující příklad vytvoří *20*GiB disku a výstupy ID disku po vytvoření. Pokud potřebujete k vytvoření disku pro použití s kontejnery Windows serveru (aktuálně ve verzi preview ve službě AKS), přidejte `--os-type windows` parametr správně a disk naformátujte.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Disky Azure se účtují po SKU pro určité velikosti. Tyto skladové položky sahají od 32GiB disků S4 nebo P4 32TiB S80 nebo P80 disků (ve verzi preview). Propustnost a výkon vstupně-výstupních operací na úrovni Premium spravovaného disku závisí na SKU a velikosti instance uzlů v clusteru AKS. Zobrazit [ceny a výkonu spravované disky][managed-disk-pricing-performance].

ID prostředku disku se zobrazí, jakmile se příkaz úspěšně dokončil, jak je znázorněno v následujícím příkladu výstupu. Toto ID disku se používá pro připojení disku v dalším kroku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Připojení disku jako svazek

Připojit Azure disk do podu, nakonfigurujte u svazku v kontejneru specifikace. Vytvořte nový soubor s názvem `azure-disk-pod.yaml` s následujícím obsahem. Aktualizace `diskName` s názvem disk vytvořený v předchozím kroku, a `diskURI` s ID disku znázorňuje výstup disku vytvořit příkaz. V případě potřeby aktualizovat `mountPath`, což je cesta kde je Azure disk připojený v pod. Pro systém Windows Server kontejnery (aktuálně ve verzi preview ve službě AKS), zadejte *mountPath* pomocí konvence cestu Windows, například *"D:"* .

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
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

Teď máte spuštěné pod s připojený k disku Azure `/mnt/azure`. Můžete použít `kubectl describe pod mypod` disk je připojený úspěšně ověřit. Následující výstup zhuštěnému příkladu ukazuje svazek připojený v kontejneru:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Další postup

Přidružené osvědčené postupy, najdete v části [osvědčené postupy pro ukládání a zálohování ve službě AKS][operator-best-practices-storage].

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
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
