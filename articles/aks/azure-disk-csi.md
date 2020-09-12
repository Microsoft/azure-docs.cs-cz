---
title: Použití ovladačů rozhraní úložiště kontejnerů (CSI) pro disky Azure ve službě Azure Kubernetes (AKS)
description: Naučte se používat ovladače rozhraní pro kontejnerové úložiště (CSI) pro disky Azure v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 4b5ccd2712a95f5f020daa0161f1b5908a38a62e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422030"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Použití ovladačů rozhraní Azure disk Container Storage (CSI) ve službě Azure Kubernetes Service (AKS) (Preview)
Ovladač Azure disk CSI je ovladač, který je kompatibilní se [specifikací CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md) , který používá AKS ke správě životního cyklu disků Azure. 

Rozhraní pro úložiště kontejnerů (CSI) představuje standard pro vystavení libovolných systémů blokování a souborů úložiště pro zabalení úloh na Kubernetes. Díky přijetí a používání rozhraní CSI může služba Azure Kubernetes Service (AKS) zapisovat, nasazovat a iterovat moduly plug-in, které vystavují nové nebo zlepšují stávající systémy úložiště v Kubernetes, aniž by museli se dotknout základního kódu Kubernetes a čekat na jeho cykly vydávání.

Pokud chcete vytvořit cluster AKS s podporou ovladače CSI, přečtěte si téma [Povolení ovladačů rozhraní CSI pro disky Azure a soubory Azure v AKS](csi-storage-drivers.md).

>[!NOTE]
> *Ovladače v rámci stromu* odkazují na aktuální ovladače úložiště, které jsou součástí základního Kubernetes kódu, a nové ovladače CSI, které jsou moduly plug-in.

## <a name="use-csi-persistent-volumes-pv-with-azure-disks"></a>Použití trvalých svazků platformy CSI (PV) s disky Azure 

[Trvalý svazek](concepts-storage.md#persistent-volumes) představuje úložiště, které je zřízené pro použití s Kubernetes lusky. Trvalý svazek lze použít v jednom nebo mnoha luskech a lze jej dynamicky nebo staticky zřídit. V tomto článku se dozvíte, jak dynamicky vytvářet trvalé svazky s disky Azure, které používá jeden z nich v clusteru Azure Kubernetes Service (AKS). Informace o statickém zřizování najdete v tématu [Ruční vytvoření a použití svazku s disky Azure](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Další informace o Kubernetes svazcích najdete v tématu [Možnosti úložiště pro aplikace v AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes"></a>Dynamické vytváření Azure disk PVs pomocí integrovaných tříd úložiště

Třída úložiště se používá k definování způsobu, jakým se jednotka úložiště dynamicky vytvoří s trvalým svazkem. Další informace o třídách úložiště Kubernetes naleznete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes]. Při používání ovladačů úložiště CSI v AKS je k dispozici 2 další integrovaná `StorageClasses` , která využívá **ovladače úložiště Azure disk CSI**. Další třídy úložiště CSI se vytvoří s clusterem společně s výchozími třídami úložiště ve stromové struktuře.

>[!NOTE]
> *Ovladače v rámci stromu* odkazují na aktuální ovladače úložiště, které jsou součástí základního Kubernetes kódu a ovladačů. CSI, které jsou moduly plug-in.

- `managed-csi` – Používá k vytvoření spravovaného disku místně redundantní úložiště (LRS) Azure StandardSSD.
- `managed-csi-premium` – Používá k vytvoření spravovaného disku místně redundantní úložiště (LRS) Azure Premium. 

Zásady opětovné deklarace v obou třídách úložiště zajistí, že se při odstranění příslušného trvalého svazku odstraní základní disk Azure. Třídy úložiště také nakonfigurují trvalé svazky tak, aby se rozšířily, stačí upravit deklaraci trvalého svazku s novou velikostí.

Aby bylo možné využít tyto třídy úložiště, stačí vytvořit [trvalou deklaraci identity trvalého svazku (PVC)](concepts-storage.md#persistent-volume-claims) a příslušnému typu, který je na něj odkazuje, a využívat je. Deklarace identity trvalého svazku (PVC) se používá k automatickému zřízení úložiště na základě třídy úložiště. Virtuální okruh (PVC) může použít jednu z předem vytvořených tříd úložiště nebo uživatelsky definované třídy úložiště k vytvoření disku spravovaného službou Azure pro požadovanou SKU a velikost. Při vytváření definice pod je určena deklarace identity trvalého svazku pro vyžádání požadovaného úložiště.

Vytvořte příklad pod a příslušnou deklaraci trvalého svazku pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Poté, co je ve stavu spuštěno, vytvořte nový soubor s názvem `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Disk teď můžete ověřit tak, že spustíte následující příkaz a ověříte, že se `test.txt` ve výstupu zobrazí soubor: 

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Vytvoření vlastní třídy úložiště

Výchozí třídy úložiště vyhovují nejběžnějším scénářům, ale ne všem. V některých případech můžete chtít vlastní třídu úložiště přizpůsobit vlastním parametrům. Pro exemplify máme scénář, kde můžete chtít změnit `volumeBindingMode` . 

Výchozí třídy úložiště používají `volumeBindingMode: Immediate` takové záruky, ke kterým dojde ihned po vytvoření PersistentVolumeClaim. V případech, kdy jsou fondy uzlů omezené, například pomocí Zóny dostupnosti, by trvalé svazky byly vázané nebo zřízené bez znalosti požadavků na plánování (v tomto případě v konkrétní zóně).

Chcete-li tento scénář vyřešit, můžete použít `volumeBindingMode: WaitForFirstConsumer` , což způsobí zpoždění vazby a zřízení PersistentVolume, dokud se nevytvoří pod a pomocí PersistentVolumeClaim. V takovém případě bude PV odpovídat a bude zajištěna v zóně dostupnosti (nebo jiné topologii), která je zadána pomocí omezení plánování na základě. 

Vytvořte soubor s názvem `sc-azuredisk-csi-waitforfirstconsumer.yaml` a vložte níže uvedený manifest.
Třída úložiště je stejná jako naše `managed-csi` třída úložiště, ale s jinou třídou úložiště `volumeBindingMode` . 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Vytvořte třídu úložiště pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte `sc-azuredisk-csi-waitforfirstconsumer.yaml` soubor:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Snímky svazků

Ovladač Azure disk CSI podporuje vytváření [snímků trvalých svazků](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). V rámci této schopnosti může ovladač provádět *úplné* nebo [ *přírůstkové* snímky](../virtual-machines/windows/disks-incremental-snapshots.md) v závislosti na hodnotě nastavené v `incremental` parametru (ve výchozím nastavení je to true). 

Podrobnosti o všech parametrech naleznete v tématu [parametry třídy snímku svazku](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Vytvoření snímku svazku

Pokud chcete tuto funkci exemplify, vytvořte [třídu snímku svazku](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Teď vytvoříme [snímek svazku](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) z virtuálního počítače, který [jsme dynamicky vytvořili na začátku tohoto kurzu](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Ověřte, zda byl snímek správně vytvořen:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Vytvoření nového okruhu PVC na základě snímku svazku

Můžete vytvořit nový trvalý virtuální okruh na základě snímku svazku. Použijte snímek vytvořený v předchozím kroku a vytvořte [Nový virtuální okruh (PVC)](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) a [Nový pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) ním, abyste ho mohli spotřebovat.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Nakonec se ujistěte, že je to stejný virtuální okruh, který jste vytvořili, a to tak, že zkontrolujete jeho obsah.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Jak se očekávalo, pořád pořád vidíte náš dříve vytvořený `test.txt` soubor.

## <a name="clone-volumes"></a>Klonovat svazky

Klonovaný svazek je definován jako duplikát stávajícího svazku Kubernetes. Další informace o klonování svazků v Kubernetes najdete v Koncepční dokumentaci pro [klonování svazků](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning).

Ovladač CSI pro disky Azure podporuje klonování svazků. Chcete-li předvést, vytvořte [Klonovaný svazek](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) [dříve vytvořeného](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `azuredisk-pvc` a [nového pod tím, aby jej bylo možné spotřebovat](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).



```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Obsah naklonovaného svazku teď můžeme zkontrolovat tak, že níže uvedený příklad spustíte a potvrzujeme, že se vám `test.txt` vytvořený soubor pořád zobrazuje.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume-pv"></a>Změna velikosti trvalého svazku (PV)

Místo toho si můžete vyžádat větší objem pro virtuální okruh. Upravte objekt PVC a zadejte větší velikost. Tato změna aktivuje rozšíření základního svazku, který zálohuje PersistentVolume. 

> [!NOTE] 
> Pro uspokojení deklarace identity se nikdy nevytvoří nový PersistentVolume. Místo toho se změnila velikost stávajícího svazku.

Integrovaná `managed-csi` třída úložiště v AKS již umožňuje rozšíření, takže využívá virtuální okruh, který byl [vytvořen dříve s touto třídou úložiště](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes). Virtuální okruh (PVC) požadoval 10Gi trvalý svazek, můžeme potvrdit, že běží na:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```
> [!IMPORTANT]
> V současné době ovladač Azure disk CSI podporuje pouze změnu velikosti virtuálních okruhů bez přidružených lusků (a svazku nepřipojeného k určitému uzlu).

Jak to umožňuje odstranit pole pod vytvořeným dříve:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Pojďme rozbalovat virtuální okruh zvýšením `spec.resources.requests.storage` pole:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Pojďme potvrdit, že svazek je teď větší:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE] 
> Virtuální okruh nebude odrážet novou velikost, dokud znovu nemá přiřazený objekt pod.

Pojďme vytvořit nové pod:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

Nakonec potvrďte velikost okruhu PVC a dovnitř pod ním: 
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

<!--- ## Shared disk

[Azure shared disks](../virtual-machines/windows/disks-shared.md) is an Azure managed disks feature that enables attaching an Azure disk to agent nodes simultaneously. Attaching a managed disk to multiple agent nodes allows you, for example, to deploy new or migrate existing clustered applications to Azure.

> [!IMPORTANT] Currently, only raw block device (`volumeMode: Block`) is supported by the Azure disk CSI driver. Applications should manage the coordination and control of writes, reads, locks, caches, mounts and fencing on the shared disk which is exposed as raw block device.

Let's create file called `shared-disk.yaml` by copying the below that contains the shared disk storage class and PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Create the storage class with the [kubectl apply][kubectl-apply] command and specify your `shared-disk.yaml` file:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Now let's create a file called `deployment-shared.yml` by copying the below:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: nginx
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Create the deployment with the [kubectl apply][kubectl-apply] command and specify your `deployment-shared.yml` file:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Finally, let's check the block device inside the pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp bash
root@deployment-sharedisk-7454978bc6-xh7jp:/# dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.0502999 s, 2.1 GB/s
```
-->

## <a name="windows-containers"></a>Kontejnery Windows

Ovladač Azure disk CSI podporuje i uzly a kontejnery Windows. Pokud chcete používat kontejnery Windows, postupujte podle [kurzu Windows Containers](windows-container-cli.md) (Přidat fond uzlů Windows).

Jakmile budete mít fond uzlů Windows, můžete teď využít předdefinované třídy úložiště, jako je `managed-csi` . Můžete nasadit ukázkovou [stavovou sadu založenou na systému Windows](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) , která uloží časová razítka do souboru `data.txt` nasazením níže pomocí příkazu [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Teď můžete ověřit obsah svazku spuštěním:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Další kroky

- Informace o použití ovladače CSI pro soubory Azure najdete v tématu [použití souborů Azure s ovladači CSI](azure-files-csi.md).
- Další informace o osvědčených postupech pro úložiště najdete v tématu [osvědčené postupy pro úložiště a zálohy ve službě Azure Kubernetes Service (AKS)][operator-best-practices-storage] .


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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register