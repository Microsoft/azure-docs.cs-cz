---
title: Dynamicky vytvořit svazek disků Azure
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak dynamicky vytvořit trvalý svazek s disky Azure ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: fd2bc698a107599dccf8f142b0d318400b40aaf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91299319"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dynamické vytvoření a použití trvalého svazku s disky Azure ve službě Azure Kubernetes Service (AKS)

Trvalý svazek představuje část úložiště, která byla zřízena pro použití s Kubernetes lusky. Trvalý svazek lze použít v jednom nebo mnoha luskech a lze jej dynamicky nebo staticky zřídit. V tomto článku se dozvíte, jak dynamicky vytvářet trvalé svazky s disky Azure, které používá jeden z nich v clusteru Azure Kubernetes Service (AKS).

> [!NOTE]
> Disk Azure se dá připojit jenom k typu *režimu přístupu* *ReadWriteOnce*, který je dostupný pro jeden uzel v AKS. Pokud potřebujete sdílet trvalý svazek mezi více uzly, použijte [soubory Azure][azure-files-pvc].

Další informace o Kubernetes svazcích najdete v tématu [Možnosti úložiště pro aplikace v AKS][concepts-storage].

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější.  `az --version`Verzi zjistíte spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="built-in-storage-classes"></a>Předdefinované třídy úložiště

Třída úložiště se používá k definování způsobu, jakým se jednotka úložiště dynamicky vytvoří s trvalým svazkem. Další informace o třídách úložiště Kubernetes naleznete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes].

Každý cluster AKS obsahuje čtyři předem vytvořené třídy úložiště, dva z nich jsou nakonfigurovány pro práci s disky Azure:

* *Výchozí* třída úložiště zřídí standardní disk SSD Azure.
    * Služba Storage úrovně Standard je založená na standardu SSD a poskytuje nákladově efektivní úložiště, a přitom stále poskytuje spolehlivý výkon. 
* Třída úložiště *Managed-Premium* zřídí disk Azure úrovně Premium.
    * Disky Premium jsou založené na vysoce výkonných discích SSD s nízkou latencí. Jsou ideální pro virtuální počítače s produkčními úlohami. Pokud uzly AKS v clusteru používají Storage úrovně Premium, vyberte třídu *Managed-Premium* .
    
Pokud používáte jednu z výchozích tříd úložiště, nemůžete po vytvoření třídy úložiště aktualizovat velikost svazku. Aby bylo možné aktualizovat velikost svazku po vytvoření třídy úložiště, přidejte řádek `allowVolumeExpansion: true` do jedné z výchozích tříd úložiště, nebo můžete vytvořit vlastní třídu úložiště. Počítejte s tím, že není podporováno snížení velikosti trvalého virtuálního počítače (aby nedošlo ke ztrátě dat). Existující třídu úložiště můžete upravit pomocí `kubectl edit sc` příkazu. 

Pokud například chcete použít disk o velikosti 4 TiB, musíte vytvořit třídu úložiště, která definuje, `cachingmode: None` protože [ukládání disku do mezipaměti není podporováno pro disky 4 TIB a větší](../virtual-machines/premium-storage-performance.md#disk-caching).

Další informace o třídách úložiště a vytváření vlastních tříd úložiště najdete v tématu [Možnosti úložiště pro aplikace v AKS][storage-class-concepts].

Pomocí příkazu [kubectl Get SC][kubectl-get] zobrazte předem vytvořené třídy úložiště. Následující příklad ukazuje třídy úložiště před vytvořením dostupné v rámci clusteru AKS:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> V GiB se zadává deklarace trvalého svazku, ale Azure Managed disks se fakturuje podle skladové jednotky za konkrétní velikost. Tyto SKU jsou v rozsahu od 32GiB pro disky S4 nebo P4 do 32TiB pro disky S80 nebo P80 (ve verzi Preview). Výkon propustnosti a IOPS na spravovaném disku úrovně Premium závisí na skladové jednotce (SKU) i na velikosti instancí uzlů v clusteru AKS. Další informace najdete v tématu [ceny a výkon Managed disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Vytvoření deklarace identity trvalého svazku

Deklarace identity trvalého svazku (PVC) se používá k automatickému zřízení úložiště na základě třídy úložiště. V takovém případě může virtuální okruh použít jednu z předem vytvořených tříd úložiště k vytvoření spravovaného disku Azure úrovně Standard nebo Premium.

Vytvořte soubor s názvem `azure-premium.yaml` a zkopírujte ho do následujícího manifestu. Deklarace identity vyžádá disk s názvem `azure-managed-disk` , který má velikost *5 GB* přístup *ReadWriteOnce* . Třída úložiště *Managed-Premium* je zadaná jako třída úložiště.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Pokud chcete vytvořit disk, který využívá úložiště úrovně Standard, použijte `storageClassName: default` místo *spravovaného na Premium*.

Pomocí příkazu [kubectl Apply][kubectl-apply] vytvořte deklaraci trvalého svazku a zadejte soubor *Azure-Premium. yaml* :

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Použití trvalého svazku

Po vytvoření deklarace identity trvalého svazku a úspěšném zřízení disku je možné vytvořit pod s přístupem k disku. Následující manifest vytvoří základní NGINX pod, který používá deklaraci trvalého svazku s názvem *Azure-Managed-disk* k připojení disku Azure v cestě `/mnt/azure` . V případě kontejnerů Windows serveru určete *mountPath* pomocí konvence cesty Windows, třeba *:*.

Vytvořte soubor s názvem `azure-pvc-disk.yaml` a zkopírujte ho do následujícího manifestu.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Vytvořte pod příkazem [kubectl Apply][kubectl-apply] , jak je znázorněno v následujícím příkladu:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Teď máte spuštěný pod diskem Azure připojeným k `/mnt/azure` adresáři. Tato konfigurace se může zobrazit při kontrole pod tím `kubectl describe pod mypod` , jak je znázorněno v následujícím zhuštěném příkladu:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="use-ultra-disks"></a>Použít disky Ultra
Pokud chcete využít Ultra disk, přečtěte si téma [použití Ultra Discs ve službě Azure Kubernetes Service (AKS)](use-ultra-disks.md).

## <a name="back-up-a-persistent-volume"></a>Zálohování trvalého svazku

Pokud chcete zálohovat data v trvalém svazku, požádejte o tento svazek snímek spravovaného disku. Pak můžete pomocí tohoto snímku vytvořit obnovený disk a připojit se k luskům jako způsob obnovení dat.

Nejdřív Získejte název svazku pomocí `kubectl get pvc` příkazu, například pro okruh PVC s názvem *Azure-Managed-disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Tento název svazku tvoří základní název disku Azure. Dotaz na ID disku pomocí příkazu [AZ disk list][az-disk-list] a zadejte název svazku PVC, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Pomocí ID disku vytvořte snímek disku pomocí [AZ Snapshot Create][az-snapshot-create]. Následující příklad vytvoří snímek s názvem *pvcSnapshot* ve stejné skupině prostředků jako cluster AKS (*MC_myResourceGroup_myAKSCluster_eastus*). Pokud vytvoříte snímky a obnovíte disky ve skupinách prostředků, ke kterým cluster AKS nemá přístup, může dojít k problémům s oprávněním.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

V závislosti na množství dat na disku může trvat několik minut, než se snímek vytvoří.

## <a name="restore-and-use-a-snapshot"></a>Obnovení a použití snímku

Pokud chcete disk obnovit a použít ho s Kubernetes pod, při vytváření disku pomocí [AZ disk Create][az-disk-create]použijte snímek jako zdroj. Tato operace zachová původní prostředek, pokud budete potřebovat přístup k původnímu snímku dat. Následující příklad vytvoří z snímku s názvem *pvcSnapshot*disk s názvem *pvcRestored* :

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Chcete-li použít obnovený disk s pod, zadejte ID disku v manifestu. ID disku získáte pomocí příkazu [AZ disk show][az-disk-show] . Následující příklad získá ID disku pro *pvcRestored* vytvořené v předchozím kroku:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Vytvořte manifest pod názvem `azure-restored.yaml` a zadejte identifikátor URI disku, který jste získali v předchozím kroku. Následující příklad vytvoří základní webový server NGINX s obnoveným diskem připojeným jako svazek na */mnt/Azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Vytvořte pod příkazem [kubectl Apply][kubectl-apply] , jak je znázorněno v následujícím příkladu:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Můžete použít `kubectl describe pod mypodrestored` k zobrazení podrobností na poli pod, jako je například následující zhuštěný příklad, který ukazuje informace o svazku:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Další kroky

Související osvědčené postupy najdete [v tématu osvědčené postupy pro úložiště a zálohování v AKS][operator-best-practices-storage].

Přečtěte si další informace o Kubernetes trvalých svazcích pomocí disků Azure.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro disky Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
