---
title: Použití ovladačů rozhraní úložiště kontejnerů (CSI) pro disky Azure ve službě Azure Kubernetes (AKS)
description: Naučte se používat ovladače rozhraní pro kontejnerové úložiště (CSI) pro disky Azure v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 5f9e28ac568f70801b2bd955c201712cfcb80084
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963322"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Použití ovladačů rozhraní Azure disk Container Storage (CSI) ve službě Azure Kubernetes Service (AKS) (Preview)
Ovladač rozhraní Azure disk Container Storage (CSI) je ovladač kompatibilní se [specifikací CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md), který používá služba Azure Kubernetes Service (AKS) ke správě životního cyklu disků Azure.

Soubory CSI jsou standardem pro vystavení libovolných systémů blokování a souborové úložiště pro vytvoření kontejnerů úloh v Kubernetes. Po přijetí a použití rozhraní CSI může AKS zapisovat, nasazovat a iterovat moduly plug-in, aby vystavoval nové nebo vylepšit stávající systémy úložiště v Kubernetes, aniž by se museli podotknout základního kódu Kubernetes a počkat na jeho cykly vydaných verzí.

Pokud chcete vytvořit cluster AKS s podporou ovladače CSI, přečtěte si téma [Povolení ovladačů rozhraní CSI pro disky Azure a soubory Azure v AKS](csi-storage-drivers.md).

>[!NOTE]
> *Ovladače stromové struktury* odkazují na aktuální ovladače úložiště, které jsou součástí základního kódu Kubernetes, oproti novým ovladačům CSI, které jsou moduly plug-in.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Použití trvalých svazků CSI s disky Azure

[Trvalý svazek](concepts-storage.md#persistent-volumes) (PV) představuje úložiště, které je zřízené pro použití s Kubernetes lusky. Souč_hod lze použít v jednom nebo mnoha luskech a lze ji dynamicky nebo staticky zřídit. V tomto článku se dozvíte, jak dynamicky vytvářet PVs s disky Azure pro použití jediným pod v clusteru AKS. Informace o statickém zřizování najdete v tématu [Ruční vytvoření a použití svazku s disky Azure](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Další informace o Kubernetes svazcích najdete v tématu [Možnosti úložiště pro aplikace v AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Dynamické vytvoření Azure disk PVs pomocí integrovaných tříd úložiště

Třída úložiště se používá k definování způsobu, jakým se jednotka úložiště dynamicky vytvoří s trvalým svazkem. Další informace o třídách úložiště Kubernetes naleznete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes]. Když použijete ovladače úložiště CSI na AKS, jsou k dispozici dva další integrované `StorageClasses` , které využívají ovladače úložiště Azure disk CSI. Další třídy úložiště CSI se vytvoří s clusterem společně s výchozími třídami úložiště ve stromové struktuře.

- `managed-csi`: K vytvoření spravovaného disku používá Azure SSD úrovně Standard místně redundantní úložiště (LRS).
- `managed-csi-premium`: Používá Azure Premium LRS k vytvoření spravovaného disku.

Zásady opětovné deklarace v obou třídách úložiště zaručují, že se po odstranění příslušné PV disk Azure odstraní. Třídy úložiště také nakonfigurují PVs tak, aby se rozšířily. Potřebujete jenom Upravit deklaraci identity trvalého svazku (PVC) s novou velikostí.

Chcete-li využít tyto třídy úložiště, vytvořte [trvalý virtuální okruh](concepts-storage.md#persistent-volume-claims) a příslušné na něm, které se na něj odkazují a používají je. K automatickému zřízení úložiště založeného na třídě úložiště slouží virtuální okruh (PVC). Virtuální okruh (PVC) může použít jednu z předem vytvořených tříd úložiště nebo uživatelsky definované třídy úložiště k vytvoření disku spravovaného službou Azure pro požadovanou SKU a velikost. Když vytváříte definici pod, je pro ni určen virtuální okruh, který požaduje požadované úložiště.

Vytvořte příklad pod a příslušný trvalý virtuální okruh pomocí příkazu [kubectl Apply][kubectl-apply] :

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

Nyní můžete ověřit, zda je disk správně připojen, spuštěním následujícího příkazu a ověřením, že je `test.txt` soubor zobrazen ve výstupu:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Vytvoření vlastní třídy úložiště

Výchozí třídy úložiště vyhovují nejběžnějším scénářům, ale ne všem. V některých případech můžete chtít vlastní třídu úložiště přizpůsobit vlastním parametrům. Například máme situaci, kdy byste mohli chtít změnit `volumeBindingMode` třídu.

Můžete použít `volumeBindingMode: Immediate` třídu, která zaručuje, že dojde ihned po vytvoření trvalého virtuálního okruhu. V případech, kdy jsou vaše fondy uzlů omezené, například používání zón dostupnosti, by PVs bylo vázané nebo zřízené bez znalosti požadavků na plánování (v tomto případě v konkrétní zóně).

Pro vyřešení tohoto scénáře můžete použít `volumeBindingMode: WaitForFirstConsumer` , čímž se odloží vazba a zřízení PV, dokud se nevytvoří pod, který používá trvalý virtuální okruh. V takovém případě bude PV odpovídat a bude zajištěna v zóně dostupnosti (nebo jiné topologii), která je zadána pomocí omezení plánování na základě. Výchozí třídy úložiště používají `volumeBindingMode: WaitForFirstConsumer` třídu.

Vytvořte soubor s názvem `sc-azuredisk-csi-waitforfirstconsumer.yaml` a vložte následující manifest.
Třída úložiště je stejná jako naše `managed-csi` třída úložiště, ale s jinou `volumeBindingMode` třídou.

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

Ovladač Azure disk CSI podporuje vytváření [snímků trvalých svazků](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). V rámci této schopnosti může ovladač provádět *úplné* nebo [ *přírůstkové* snímky](../virtual-machines/disks-incremental-snapshots.md) v závislosti na hodnotě nastavené v `incremental` parametru (ve výchozím nastavení je to true).

Podrobnosti o všech parametrech naleznete v tématu [parametry třídy snímku svazku](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Vytvoření snímku svazku

Příklad této funkce získáte tak, že vytvoříte [třídu snímků svazku](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Teď vytvoříme [snímek svazku](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) z okruhu PVC, který [jsme dynamicky vytvořili na začátku tohoto kurzu](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `pvc-azuredisk` .


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

Můžete vytvořit nový trvalý virtuální okruh na základě snímku svazku. Použijte snímek vytvořený v předchozím kroku a vytvořte [nový okruh PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) a [Nový pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) ním, aby se mohl využívat.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Nakonec se ujistěte, že se jedná o stejný virtuální okruh, který jste vytvořili, a to tak, že zkontrolujete jeho obsah.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Jak bylo očekáváno, můžeme stále vidět náš dříve vytvořený `test.txt` soubor.

## <a name="clone-volumes"></a>Klonovat svazky

Klonovaný svazek je definován jako duplikát stávajícího svazku Kubernetes. Další informace o klonování svazků v Kubernetes najdete v Koncepční dokumentaci pro [klonování svazků](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning).

Ovladač CSI pro disky Azure podporuje klonování svazků. Chcete-li předvést, vytvořte [Klonovaný svazek](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) [dříve vytvořeného](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc` a [nového pod tím, aby jej bylo možné spotřebovat](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Obsah klonovaného svazku teď můžeme zkontrolovat spuštěním následujícího příkazu a potvrzením, že se pořád zobrazuje náš `test.txt` vytvořený soubor.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Změna velikosti trvalého svazku

Místo toho si můžete vyžádat větší objem pro virtuální okruh. Upravte objekt PVC a zadejte větší velikost. Tato změna aktivuje rozšíření základního svazku, který vrací souč_hod.

> [!NOTE]
> Nová souč_hod se nikdy nevytvořila, aby vyhověla deklaraci identity. Místo toho se změnila velikost stávajícího svazku.

Integrovaná `managed-csi` třída úložiště v AKS již umožňuje rozšíření, takže použijte virtuální okruh, který byl [vytvořen dříve s touto třídou úložiště](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). Okruh PVC požadoval trvalý svazek 10-GI. Můžeme potvrdit, že je možné spustit:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> V současné době ovladač Azure disk CSI podporuje pouze změnu velikosti virtuálních okruhů bez přidružených lusků (a svazku nepřipojeného k určitému uzlu).

Tímto způsobem odstraníme na začátku, které jsme vytvořili dříve:

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

A nakonec potvrďte velikost okruhu PVC a dovnitř pod ním:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Sdílený disk

[Sdílené disky Azure](../virtual-machines/disks-shared.md) jsou funkce služby Azure Managed disks, která umožňuje současně připojit uzly disku Azure k uzlům agenta. Připojení spravovaného disku k několika uzlům agentů vám umožní například nasadit nové nebo migrovat existující clusterované aplikace do Azure.

> [!IMPORTANT] 
> Ovladač Azure disk CSI v současné době podporuje jenom nezpracované blokové zařízení ( `volumeMode: Block` ). Aplikace by měly spravovat koordinaci a kontrolu nad zápisy, čtením, zámky, mezipamětí, připojeními a ukládáním do vyrovnávací paměti na sdíleném disku, který je vystavený jako nezpracované blokové zařízení.

Vytvoříme soubor s názvem `shared-disk.yaml` zkopírováním následujícího příkazu, který obsahuje třídu úložiště sdíleného disku a okruhu PVC:

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

Vytvořte třídu úložiště pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte `shared-disk.yaml` soubor:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Teď vytvoříme soubor s názvem `deployment-shared.yml` zkopírováním následujícího příkazu:

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
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Pomocí příkazu [kubectl Apply][kubectl-apply] vytvořte nasazení a zadejte `deployment-shared.yml` soubor:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Nakonec zkontrolujeme blokování zařízení uvnitř pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Kontejnery Windows

Ovladač Azure disk CSI podporuje i uzly a kontejnery Windows. Pokud chcete používat kontejnery Windows, přidejte fond uzlů Windows podle [kurzu Windows Containers](windows-container-cli.md) .

Až budete mít fond uzlů Windows, můžete teď použít předdefinované třídy úložiště, jako je `managed-csi` . Můžete nasadit ukázkovou [stavovou sadu založenou na Windows](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) , která uloží časová razítka do souboru `data.txt` nasazením následujícího příkazu pomocí příkazu [kubectl Apply][kubectl-apply] :

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

- Informace o tom, jak používat ovladače CSI pro soubory Azure, najdete v tématu [použití souborů Azure s ovladači CSI](azure-files-csi.md).
- Další informace o osvědčených postupech pro úložiště najdete v tématu [osvědčené postupy pro úložiště a zálohování ve službě Azure Kubernetes][operator-best-practices-storage].


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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
