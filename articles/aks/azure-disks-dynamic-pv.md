---
title: Dynamické vytváření a používání trvalého svazku s disky Azure ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak dynamicky vytvořit trvalý svazek s disky Azure ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: f16a6134b1d2065668952ea11c0cab7398a3559a
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617475"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dynamické vytváření a používání trvalého svazku s disky Azure ve službě Azure Kubernetes Service (AKS)

Trvalý svazek představuje část úložiště, která byla zřízena pro použití s pody Kubernetes. Trvalý svazek může používat jeden nebo mnoho podů a může být dynamicky nebo staticky zřízena. Tento článek ukazuje, jak dynamicky vytvářet trvalé svazky s disky Azure pro použití jedním podem v clusteru Služby Azure Kubernetes (AKS).

> [!NOTE]
> Disk Azure lze připojit pouze s typem *přístupu* typu *ReadWriteOnce*, který je k dispozici pouze jeden pod v AKS. Pokud potřebujete sdílet trvalý svazek mezi více pody, použijte [Soubory Azure][azure-files-pvc].

Další informace o svazcích Kubernetes najdete [v tématu Možnosti úložiště pro aplikace v AKS][concepts-storage].

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované verze Azure CLI verze 2.0.59 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="built-in-storage-classes"></a>Vestavěné třídy úložišť

Třída úložiště se používá k definování způsobu dynamického vytváření jednotky úložiště s trvalým svazkem. Další informace o třídách úložiště Kubernetes naleznete [v tématu Kubernetes Storage Classes][kubernetes-storage-classes].

Každý cluster AKS obsahuje dvě předem vytvořené třídy úložiště, obě nakonfigurované pro práci s disky Azure:

* *Výchozí* třída úložiště zřstorage s standardnídisk Azure.
    * Standardní úložiště je podporováno pevnými disky a poskytuje nákladově efektivní úložiště, zatímco je stále výkonné. Standardní disky jsou ideální pro nákladově efektivní úlohy pro práci s dev a test.
* Třída úložiště *spravovaného premium* zřídí prémiový disk Azure.
    * Disky Premium jsou založené na vysoce výkonných discích SSD s nízkou latencí. Jsou ideální pro virtuální počítače s produkčními úlohami. Pokud uzly AKS ve vašem clusteru používají úložiště premium, vyberte třídu *spravovaného premium.*
    
Tyto výchozí třídy úložiště neumožňují aktualizovat velikost svazku po vytvoření. Chcete-li tuto možnost povolit, přidejte řádek *allowVolumeExpansion: true* do jedné z výchozích tříd úložiště nebo vytvořte vlastní třídu úložiště. Pomocí příkazu můžete upravit `kubectl edit sc` existující třídu úložiště. Další informace o třídách úložiště a vytváření vlastních, naleznete v [tématu Možnosti úložiště pro aplikace v AKS][storage-class-concepts].

Pomocí příkazu [kubectl get sc][kubectl-get] zobrazíte předem vytvořené třídy úložiště. Následující příklad ukazuje předvytvoření tříd úložiště, které jsou k dispozici v clusteru AKS:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Trvalé deklarace svazku jsou určeny v GiB, ale spravované disky Azure se účtují skladovou položkou pro určitou velikost. Tyto skutážse se pohybují od 32GiB pro Disky S4 nebo P4 až po 32TiB pro disky S80 nebo P80 (ve verzi Preview). Výkon propustnosta a vstupně-sady služby Premium spravovaného disku závisí na skladové jednotce a velikosti instance uzlů v clusteru AKS. Další informace naleznete v [tématu Ceny a výkon spravovaných disků][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Vytvoření trvalé deklarace svazku

Deklarace trvalého svazku (PVC) se používá k automatickému zřizování úložiště na základě třídy úložiště. V takovém případě může trvalý virtuální okruh použít jednu z předem vytvořených tříd úložiště k vytvoření standardního nebo prémiového spravovaného disku Azure.

Vytvořte soubor `azure-premium.yaml`s názvem a zkopírujte jej do následujícího manifestu. Deklarace požaduje disk `azure-managed-disk` s názvem *5 GB* s přístupem *ReadWriteOnce.* Třída úložiště *spravovaného premium* je určena jako třída úložiště.

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
> Chcete-li vytvořit disk, který `storageClassName: default` používá standardní úložiště, použijte místo *spravovaného programu*.

Vytvořte deklaraci trvalého svazku pomocí příkazu [kubectl apply][kubectl-apply] a zadejte soubor *azure-premium.yaml:*

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Použití trvalého svazku

Po vytvoření deklarace trvalého svazku a úspěšném zřízení disku lze vytvořit pod s přístupem k disku. Následující manifest vytvoří základní pod NGINX, který používá deklaraci trvalého svazku s `/mnt/azure`názvem *azure spravovaného disku* k připojení disku Azure na cestě . Pro kontejnery Windows Serveru (aktuálně ve verzi preview v AKS) zadejte *mountPath* pomocí konvence cesty systému Windows, například *"D:"*.

Vytvořte soubor `azure-pvc-disk.yaml`s názvem a zkopírujte jej do následujícího manifestu.

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

Vytvořte pod s příkazem [kubectl apply,][kubectl-apply] jak je znázorněno v následujícím příkladu:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Nyní máte spuštěný pod s diskem `/mnt/azure` Azure připojeným v adresáři. Tato konfigurace může být viděn při `kubectl describe pod mypod`kontrole pod přes , jak je znázorněno v následujícím příkladu kondenzované:

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

## <a name="back-up-a-persistent-volume"></a>Zálohování trvalého svazku

Chcete-li zálohovat data v trvalém svazku, pořizovat snímek spravovaného disku pro svazek. Tento snímek pak můžete použít k vytvoření obnoveného disku a připojení k podům jako prostředek obnovení dat.

Nejprve získejte název `kubectl get pvc` svazku pomocí příkazu, například pro trvalý virtuální okruh s názvem *azure-managed-disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Tento název svazku tvoří základní název disku Azure. Dotaz na ID disku se [seznamem az disků][az-disk-list] a zadejte název svazku trvalého virtuálního okruhu, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Pomocí ID disku vytvořte snímek disku s [az snímek vytvořit][az-snapshot-create]. Následující příklad vytvoří snímek s názvem *PVCSnapshot* ve stejné skupině prostředků jako cluster AKS (*MC_myResourceGroup_myAKSCluster_eastus*). Pokud vytvoříte snímky a obnovíte disky ve skupinách prostředků, ke kterým cluster AKS nemá přístup, může dojít k problémům s oprávněním.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

V závislosti na množství dat na disku může vytvoření snímku trvat několik minut.

## <a name="restore-and-use-a-snapshot"></a>Obnovení a použití snímku

Chcete-li disk obnovit a použít jej s modulem Kubernetes, použijte snímek jako zdroj při vytváření disku s [vytvořením az disku][az-disk-create]. Tato operace zachová původní prostředek, pokud pak potřebujete získat přístup k původní snímek dat. Následující příklad vytvoří disk s názvem *pvcRestored* ze snímku s názvem *PVCSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Chcete-li použít obnovený disk s modulem pod, zadejte ID disku v manifestu. Získejte ID disku pomocí příkazu [az disk show.][az-disk-show] Následující příklad získá ID disku pro *pvcRestored* vytvořené v předchozím kroku:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Vytvořte manifest `azure-restored.yaml` pod s názvem a zadejte identifikátor URI disku získaný v předchozím kroku. Následující příklad vytvoří základní webový server NGINX s obnoveným diskem připojeným jako svazek na */mnt/azure*:

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

Vytvořte pod s příkazem [kubectl apply,][kubectl-apply] jak je znázorněno v následujícím příkladu:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Můžete zobrazit `kubectl describe pod mypodrestored` podrobnosti o podu, například následující zkrácený příklad, který zobrazuje informace o svazku:

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

Doporučené postupy najdete v [tématu Doporučené postupy pro ukládání a zálohování v AKS][operator-best-practices-storage].

Přečtěte si další informace o trvalých svazcích Kubernetes pomocí disků Azure.

> [!div class="nextstepaction"]
> [Kubernetes plugin pro disky Azure][azure-disk-volume]

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
[premium-storage]: ../virtual-machines/windows/disks-types.md
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
