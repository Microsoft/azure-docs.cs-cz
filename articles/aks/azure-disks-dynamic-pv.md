---
title: Vytvářet trvalé svazky pomocí služby Azure Kubernetes Service
description: Další informace o použití disků v Azure k vytvoření trvalého svazků pro podů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: 7048ab4e08d25fd5181857a4e7592d0bcb7d3b5f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885590"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Vytvářet trvalé svazky s disky Azure pro Azure Kubernetes Service (AKS)

Trvalý svazek představuje část úložiště, která byla zřízena pro použití s podů Kubernetes. Trvalý svazek může využívat jeden nebo více podů a je možné dynamicky nebo staticky zřídit. Další informace o Kubernetes trvalé svazky, naleznete v tématu [trvalé svazky Kubernetes][kubernetes-volumes]. Tento článek ukazuje, jak použít trvalé svazky s Azure disky v clusteru služby Azure Kubernetes Service (AKS).

> [!NOTE]
> Disk s Azure je možné připojit pouze s *režim přístupu* typ *ReadWriteOnce*, takže je k dispozici pouze jeden uzel AKS. Pokud museli trvalý svazek sdílet mezi více uzlů, zvažte použití [Azure Files][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Součástí třídy úložiště

Třída úložiště se používá k definování, jak se jednotka úložiště dynamicky vytvoří s trvalý svazek. Další informace o Kubernetes třídy úložiště najdete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes].

Každý cluster AKS obsahuje dvě třídy předem vytvořené úložiště, nakonfigurovány pro práci s Azure disky:

* *Výchozí* třídu úložiště mu standardní disk Azure.
    * Storage úrovně Standard je založená na jednotkách HDD a poskytuje nákladově efektivní úložiště se zachováním výkonu. Disky Standard jsou ideální pro nákladově efektivní vývoj a testování.
* *Spravované premium* třídu úložiště zřídí disku Azure na úrovni premium.
    * Disky Premium jsou založené na vysoce výkonných discích SSD s nízkou latencí. Jsou ideální pro virtuální počítače s produkčními úlohami. Pokud uzlů AKS ve vašem clusteru používat premium storage, vyberte *spravované premium* třídy.

Použití [kubectl get sc] [ kubectl-get] příkazu naleznete v tématu třídy předem vytvořené úložiště. Následující příklad ukazuje předem vytvořit třídy úložiště k dispozici v rámci clusteru AKS:

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Trvalý svazek deklarace identity jsou uvedeny v GiB, ale Azure managed disks se účtují po SKU pro určité velikosti. Tyto skladové položky sahají od 32GiB disků S4 nebo P4 4TiB S50 nebo P50 disků. Propustnost a výkon vstupně-výstupních operací na úrovni Premium spravovaného disku závisí na obou SKU a velikosti instance uzlů v clusteru AKS. Další informace najdete v tématu [ceny a výkonu Managed Disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Vytvořit deklaraci identity trvalý svazek

Trvalý svazek deklarace identity (PVC) se používá k automatickému zřízení úložiště založené na třídě úložiště. V takovém případě PVC můžete použít jednu z tříd předem vytvořené úložiště k vytvoření Azure úrovně standard nebo premium spravovaného disku.

Vytvořte soubor s názvem `azure-premium.yaml`a zkopírujte do následující manifestu. Disk s názvem vyžádá deklarace identity `azure-managed-disk` , který je *5GB* velikost s *ReadWriteOnce* přístup. *Spravované premium* třídu úložiště je zadán jako třídy úložiště.

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
> Chcete-li vytvořit disk, který používá služba storage úrovně standard, použijte `storageClassName: default` spíše než *spravované premium*.

Vytvořit deklaraci trvalý svazek s [kubectl vytvořit] [ kubectl-create] příkaz a zadejte vaše *azure premium.yaml* souboru:

```
$ kubectl create -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Použít trvalý svazek

Po vytvoření deklarace identity trvalý svazek a disku úspěšně zřízený, pod je možné vytvořit s přístupem k disku. Následující manifest vytvoří základní pod NGINX, která používá deklarace identity trvalý svazek s názvem *azure managed Disks* připojení Azure disku v cestě `/mnt/azure`.

Vytvořte soubor s názvem `azure-pvc-disk.yaml`a zkopírujte do následující manifestu.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Vytvořte pod s [kubectl vytvořit] [ kubectl-create] příkaz, jak je znázorněno v následujícím příkladu:

```
$ kubectl create -f azure-pvc-disk.yaml

pod/mypod created
```

Teď máte spuštěné pod s Azure disku připojené `/mnt/azure` adresáře. Tato konfigurace se zobrazí při kontrole podu prostřednictvím `kubectl describe pod mypod`, jak je znázorněno v následujícímu zhuštěnému příkladu:

```
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

Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Zálohování trvalý svazek

Zálohovat data ve svazku trvalé, pořízení snímku spravovaného disku pro svazek. Tento snímek pak můžete vytvořit obnovený disk a připojit k podů jako způsob obnovení dat.

Nejprve získejte název svazku se `kubectl get pvc` příkazu, například pro připojení s názvem *azure managed Disks*:

```
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Tento název svazku tvoří základní název disku. Dotaz na ID disku s [az disk list] [ az-disk-list] a zadejte název svazku PVC, jak je znázorněno v následujícím příkladu:

```
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Použít k vytvoření snímku disku s ID disku [vytvoření snímku az][az-snapshot-create]. Následující příklad vytvoří snímek s názvem *pvcSnapshot* ve stejné skupině prostředků jako AKS cluster (*MC_myResourceGroup_myAKSCluster_eastus*). Problémům s oprávněním může dojít, pokud chcete vytvořit snímky a obnovte disky ve skupinách prostředků, které AKS cluster nemá přístup k.

```azurecli
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

V závislosti na objemu dat na disku může trvat několik minut pro vytvoření snímku.

## <a name="restore-and-use-a-snapshot"></a>Obnovit a použít snímek

Obnovení disku a jeho použití s podu Kubernetes, použijte snímek jako zdroj a při vytváření disku s [az disk vytvořit][az-disk-create]. Tato operace zachová původní prostředek, pokud je pak potřeba přístup k původní data snímku. Následující příklad vytvoří disk s názvem *pvcRestored* ze snímku s názvem *pvcSnapshot*:

```azurecli
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Používat obnoveného disku pod, zadejte ID disku v manifestu. Získání ID disku se [az disk show] [ az-disk-show] příkazu. Následující příklad získá ID disku pro *pvcRestored* vytvořili v předchozím kroku:

```azurecli
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Vytvoření manifestu pod názvem `azure-restored.yaml` a zadejte disk identifikátor URI, kterou jste získali v předchozím kroku. Následující příklad vytvoří základní webový server NGINX, se obnovený disk připojený jako svazek v */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
    - name: myfrontendrestored
      image: nginx
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

Vytvořte pod s [kubectl vytvořit] [ kubectl-create] příkaz, jak je znázorněno v následujícím příkladu:

```
$ kubectl create -f azure-restored.yaml

pod/mypodrestored created
```

Můžete použít `kubectl describe pod mypodrestored` k zobrazení podrobností pod, jako je například následujícímu zhuštěnému příkladu, který zobrazuje informace o svazku:

```
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

## <a name="next-steps"></a>Další postup

Další informace o Kubernetes trvalé svazky s využitím disků v Azure.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro disky Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
