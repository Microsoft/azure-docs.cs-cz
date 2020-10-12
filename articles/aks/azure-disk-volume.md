---
title: Vytvoření statického svazku pro lusky ve službě Azure Kubernetes (AKS)
description: Přečtěte si, jak ručně vytvořit svazek s disky Azure pro použití s níže v Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 32e9da592d4c8f3997d5b1844065bf550d7d7d48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82207509"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ruční vytvoření a použití svazku s disky Azure ve službě Azure Kubernetes Service (AKS)

Aplikace založené na kontejnerech často potřebují přístup k datům v externím datovém svazku a jejich uchovávání. Pokud jeden z nich potřebuje přístup k úložišti, můžete použít disky Azure k prezentaci nativního svazku pro použití aplikací. V tomto článku se dozvíte, jak ručně vytvořit disk Azure a připojit ho k objektu pod v AKS.

> [!NOTE]
> Disk Azure se dá v jednom okamžiku připojit jenom k jednomu pod. Pokud potřebujete sdílet trvalý svazek mezi více lusky, použijte [soubory Azure][azure-files-volume].

Další informace o Kubernetes svazcích najdete v tématu [Možnosti úložiště pro aplikace v AKS][concepts-storage].

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější.  `az --version`Verzi zjistíte spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="create-an-azure-disk"></a>Vytvoření disku Azure

Když vytvoříte disk Azure pro použití s AKS, můžete vytvořit prostředek disku ve skupině prostředků **uzlu** . Tento přístup umožňuje clusteru AKS získat přístup k prostředku disku a jeho správu. Pokud místo toho vytvoříte disk v samostatné skupině prostředků, musíte instančnímu objektu služby Azure Kubernetes (AKS) pro váš cluster udělit `Contributor` roli pro skupinu prostředků na disku. Případně můžete použít spravovanou identitu přiřazenou systémem pro oprávnění místo instančního objektu. Další informace najdete v tématu [použití spravovaných identit](use-managed-identity.md).

V tomto článku Vytvořte disk ve skupině prostředků uzlu. Nejprve Získejte název skupiny prostředků pomocí příkazu [AZ AKS show][az-aks-show] a přidejte `--query nodeResourceGroup` parametr dotazu. Následující příklad načte skupinu prostředků uzlu pro název clusteru AKS *myAKSCluster* v názvu skupiny prostředků *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Nyní vytvořte disk pomocí příkazu [AZ disk Create][az-disk-create] . Zadejte název skupiny prostředků uzlu získaný v předchozím příkazu a potom název diskového prostředku, například *myAKSDisk*. Následující příklad vytvoří *20*GIB disk a po vytvoření vytvoří výstup ID disku. Pokud potřebujete vytvořit disk pro použití s kontejnery Windows serveru, přidejte `--os-type windows` do správného formátování disku parametr.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Disky Azure se účtují podle skladové jednotky za konkrétní velikost. Tyto SKU jsou v rozsahu od 32GiB pro disky S4 nebo P4 do 32TiB pro disky S80 nebo P80 (ve verzi Preview). Výkon propustnosti a IOPS na spravovaných discích úrovně Premium závisí na skladové jednotce (SKU) i na velikosti instancí uzlů v clusteru AKS. Podívejte [se na ceny a výkon Managed disks][managed-disk-pricing-performance].

ID prostředku disku se zobrazí po úspěšném dokončení příkazu, jak je znázorněno v následujícím příkladu výstupu. Toto ID disku se používá k připojení disku v dalším kroku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Připojit disk jako svazek

Pokud chcete připojit disk Azure do svého zařízení pod, nakonfigurujte svazek ve specifikaci kontejneru. Vytvořte nový soubor s názvem `azure-disk-pod.yaml` s následujícím obsahem. Aktualizujte `diskName` název disku, který jste vytvořili v předchozím kroku, a `diskURI` s ID disku zobrazeným ve výstupu příkazu pro vytvoření disku. V případě potřeby aktualizujte `mountPath` cestu, která je cesta k disku Azure připojeného k části pod. V případě kontejnerů Windows serveru určete *mountPath* pomocí konvence cesty Windows, třeba *:*.

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

Pomocí `kubectl` příkazu vytvořte pod.

```console
kubectl apply -f azure-disk-pod.yaml
```

Teď máte spuštěný pod s diskem Azure připojeným na `/mnt/azure` . `kubectl describe pod mypod`K ověření, zda je disk úspěšně připojen, můžete použít. Následující zhuštěný příklad výstupu ukazuje svazek připojený do kontejneru:

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

## <a name="next-steps"></a>Další kroky

Související osvědčené postupy najdete [v tématu osvědčené postupy pro úložiště a zálohování v AKS][operator-best-practices-storage].

Další informace o spolupráci AKS clusterů s disky Azure najdete v tématu [modul plug-in Kubernetes pro disky Azure][kubernetes-disks].

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
