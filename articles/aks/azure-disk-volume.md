---
title: Vytvoření statického svazku pro pody ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak ručně vytvořit svazek s disky Azure pro použití s podem ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 17795ae696c0d710f099a5c21aa754fc925953ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047944"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ruční vytvoření a použití svazku s disky Azure ve službě Azure Kubernetes Service (AKS)

Aplikace založené na kontejnerech často potřebují přístup k datům a jejich uchovávání v externím datovém svazku. Pokud jeden pod potřebuje přístup k úložišti, můžete použít disky Azure k prezentaci nativního svazku pro použití v aplikaci. Tento článek ukazuje, jak ručně vytvořit disk Azure a připojit jej k podu v AKS.

> [!NOTE]
> Disk Azure lze připojit pouze do jednoho podu najednou. Pokud potřebujete sdílet trvalý svazek mezi více pody, použijte [Soubory Azure][azure-files-volume].

Další informace o svazcích Kubernetes najdete [v tématu Možnosti úložiště pro aplikace v AKS][concepts-storage].

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované verze Azure CLI verze 2.0.59 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="create-an-azure-disk"></a>Vytvoření disku Azure

Když vytvoříte disk Azure pro použití s AKS, můžete vytvořit prostředek disku ve skupině prostředků **uzlu.** Tento přístup umožňuje clusteru AKS přistupovat k prostředku disku a spravovat ho. Pokud místo toho vytvoříte disk v samostatné skupině prostředků, musíte udělit instanční objekt služby `Contributor` Azure Kubernetes Service (AKS) pro váš cluster role skupiny prostředků disku. Případně můžete použít systém přiřazenou spravovanou identitu pro oprávnění namísto instančního objektu. Další informace naleznete v tématu [Použití spravovaných identit](use-managed-identity.md).

Pro tento článek vytvořte disk ve skupině prostředků uzlu. Nejprve získejte název skupiny prostředků pomocí příkazu [az aks show][az-aks-show] a přidejte parametr dotazu. `--query nodeResourceGroup` Následující příklad získá skupinu prostředků uzlu pro název clusteru AKS *myAKSCluster* v názvu skupiny prostředků *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Nyní vytvořte disk pomocí příkazu [az disk create.][az-disk-create] Zadejte název skupiny prostředků uzlu získaný v předchozím příkazu a potom název pro diskový prostředek, například *myAKSDisk*. Následující příklad vytvoří *20*GiB disk a výstupy ID disku po vytvoření. Pokud potřebujete vytvořit disk pro použití s kontejnery Windows Server (aktuálně ve `--os-type windows` verzi Preview v AKS), přidejte parametr pro správné formátování disku.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Disky Azure se účtují skladovou položkou pro určitou velikost. Tyto skutážse se pohybují od 32GiB pro Disky S4 nebo P4 až po 32TiB pro disky S80 nebo P80 (ve verzi Preview). Výkon propustnosta a vstupně-sady spravovaného disku Premium závisí na skladové jednotce a velikosti instance uzlů v clusteru AKS. Viz [Ceny a výkon spravovaných disků][managed-disk-pricing-performance].

ID prostředku disku se zobrazí po úspěšném dokončení příkazu, jak je znázorněno v následujícím příkladu výstupu. Toto ID disku se používá k připojení disku v dalším kroku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Připojení disku jako svazku

Chcete-li připojit disk Azure do podu, nakonfigurujte `azure-disk-pod.yaml` svazek ve specifikaci kontejneru. Aktualizace `diskName` s názvem disku vytvořeného v předchozím `diskURI` kroku a s ID disku zobrazeným ve výstupu příkazu vytvořit disk. V případě potřeby `mountPath`aktualizujte , což je cesta, kde je disk Azure připojen v podu. Pro kontejnery Windows Serveru (aktuálně ve verzi preview v AKS) zadejte *mountPath* pomocí konvence cesty systému Windows, například *"D:"*.

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

Nyní máte spuštěný pod s diskem Azure připojeným na adrese `/mnt/azure`. Můžete použít `kubectl describe pod mypod` k ověření, zda je disk úspěšně připojen. Následující kondenzovaný příklad výstupu ukazuje objem namontovaný v kontejneru:

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

Doporučené postupy najdete v [tématu Doporučené postupy pro ukládání a zálohování v AKS][operator-best-practices-storage].

Další informace o clusterech AKS, které interagují s disky Azure, najdete v [tématu Plugin Kubernetes pro Disky Azure][kubernetes-disks].

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
