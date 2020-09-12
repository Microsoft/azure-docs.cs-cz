---
title: Použití ovladačů rozhraní úložiště kontejnerů (CSI) pro Azure Files ve službě Azure Kubernetes (AKS)
description: Naučte se používat ovladače rozhraní pro kontejnerové úložiště (CSI) pro soubory Azure v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 018275b6db4c2d2d1059f35077f74a6f45ec3ba9
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422029"
---
# <a name="use-the-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Použití ovladačů rozhraní kontejnerů úložiště Azure Files ve službě Azure Kubernetes (AKS) (Preview)
Ovladač Azure Files CSI je ovladač, který je kompatibilní s [specifikací CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md) , který používá AKS ke správě životního cyklu sdílených složek Azure Files. 

Rozhraní pro úložiště kontejnerů (CSI) představuje standard pro vystavení libovolných systémů blokování a souborů úložiště pro zabalení úloh na Kubernetes. Díky přijetí a používání rozhraní CSI může služba Azure Kubernetes Service (AKS) nyní zapisovat, nasazovat a iterovat moduly plug-in, které odhalují nové nebo zlepšují stávající úložné systémy v Kubernetes, aniž by bylo nutné se dotknout základního kódu Kubernetes a čekat na jejich cykly vydávání.

Pokud chcete vytvořit cluster AKS s podporou ovladače CSI, přečtěte si téma [Povolení ovladačů rozhraní CSI pro disky Azure a soubory Azure v AKS](csi-storage-drivers.md).

>[!NOTE]
> *Ovladače v rámci stromu* odkazují na aktuální ovladače úložiště, které jsou součástí základního Kubernetes kódu, a nové ovladače CSI, které jsou moduly plug-in.

## <a name="use-a-persistent-volume-pv-with-azure-files"></a>Použití trvalého svazku (PV) se soubory Azure

[Trvalý svazek](concepts-storage.md#persistent-volumes) představuje úložiště, které je zřízené pro použití s Kubernetes lusky. Trvalý svazek lze použít v jednom nebo mnoha luskech a lze jej dynamicky nebo staticky zřídit. Pokud více lusků potřebuje souběžný přístup ke stejnému svazku úložiště, můžete použít soubory Azure pro připojení pomocí [protokolu SMB (Server Message Block)][smb-overview]. V tomto článku se dozvíte, jak dynamicky vytvořit sdílenou složku Azure Files pro použití v několika luskech v clusteru Azure Kubernetes Service (AKS). Informace o statickém zřizování najdete v tématu [Ruční vytvoření a použití svazku se sdílenou složkou Azure Files](azure-files-volume.md).

Další informace o Kubernetes svazcích najdete v tématu [Možnosti úložiště pro aplikace v AKS][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-using-the-built-in-storage-classes"></a>Dynamické vytváření PVs souborů Azure pomocí integrovaných tříd úložiště
Třída úložiště se používá k definování způsobu vytvoření sdílené složky Azure. Účet úložiště se automaticky vytvoří ve [skupině prostředků uzlu][node-resource-group] pro použití s třídou úložiště pro ukládání sdílených složek Azure. Vyberte následující [redundanci úložiště Azure][storage-skus] pro *skuName*:

* *Standard_LRS* – standardní místně redundantní úložiště
* *Standard_GRS* – standardní geograficky redundantní úložiště
* *Standard_ZRS* – standardní zóna redundantního úložiště
* *Standard_RAGRS* – standardní geograficky redundantní úložiště s přístupem pro čtení
* *Premium_LRS* – místní redundantní úložiště úrovně Premium

> [!NOTE]
> Soubory Azure podporují službu Premium Storage. minimální sdílená složka Premium je 100 GB.

Při používání ovladačů úložiště CSI v AKS je k dispozici 2 další integrovaná `StorageClasses` , která využívá **ovladače úložiště rozhraní Azure Files CSI**. Další třídy úložiště CSI se vytvoří s clusterem společně s výchozími třídami úložiště ve stromové struktuře.

- `azurefile-csi` – Používá službu Azure Storage Standard k vytvoření sdílené složky Azure. 
- `azurefile-csi-premium` – Využívá Azure Premium Storage k vytvoření sdílené složky Azure. 

Zásady opětovné deklarace v obou třídách úložiště zajistí, že se při odstranění příslušného trvalého svazku odstraní základní sdílená složka Azure. Třídy úložiště také nakonfigurují sdílené složky tak, aby se rozšířily, stačí upravit deklaraci trvalého svazku s novou velikostí.

Pokud chcete využít tyto třídy úložiště, vytvořte [deklaraci trvalého svazku (PVC)](concepts-storage.md#persistent-volume-claims) a příslušnému typu, který se na něj odkazuje, a využijte je. Deklarace identity trvalého svazku (PVC) se používá k automatickému zřízení úložiště na základě třídy úložiště. Virtuální okruh (PVC) může použít jednu z předem vytvořených tříd úložiště nebo uživatelsky definované třídy úložiště k vytvoření sdílené složky Azure Files pro požadovanou SKU a velikost. Při vytváření definice pod je určena deklarace identity trvalého svazku pro vyžádání požadovaného úložiště.

Vytvořte [příklad deklarace identity trvalého svazku a pod tím, který vytiskne aktuální datum `outfile` do objektu](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Po je-li je ve stavu spuštěno, můžete ověřit, zda je sdílená složka správně připojena, spuštěním následujícího příkazu a ověřením výstupu obsahuje `outfile` : 

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Vytvoření vlastní třídy úložiště

Výchozí třídy úložiště vyhovují nejběžnějším scénářům, ale ne všem. V některých případech můžete chtít vlastní třídu úložiště přizpůsobit vlastním parametrům. Použijte například následující manifest ke konfiguraci `mountOptions` sdílené složky.

Výchozí hodnota pro *FileMode* a *dirMode* je *0777* pro sdílené složky Kubernetes připojených souborů. V objektu třídy úložiště můžete zadat různé možnosti připojení.

Vytvořte soubor s názvem `azure-file-sc.yaml` a vložte následující vzorový manifest: 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Vytvořte třídu úložiště pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Ovladač Azure Files CSI podporuje vytváření [snímků trvalých svazků](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) a podkladových sdílených složek. 

Vytvořte [třídu snímku svazku](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Vytvoří [snímek svazku](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) z virtuálního okruhu, který [jsme dynamicky vytvořili na začátku tohoto kurzu](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes) `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Ověřte, že se snímek správně vytvořil:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume-pv"></a>Změna velikosti trvalého svazku (PV)

Pro virtuální okruh (PVC) si můžete vyžádat větší objem. Upravte objekt PVC a zadejte větší velikost. Tato změna aktivuje rozšíření základního svazku, který zálohuje PersistentVolume. 

> [!NOTE] 
> Pro uspokojení deklarace identity se nikdy nevytvoří nový PersistentVolume. Místo toho se změnila velikost stávajícího svazku.

Integrovaná `azurefile-csi` třída úložiště v AKS již podporuje rozšíření, takže využívá [virtuální okruhy vytvořené dříve s touto třídou úložiště](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes). Virtuální okruh (PVC) požadoval sdílenou složku 100Gi, můžeme potvrdit, že běží na:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Rozšiřte okruh PVC zvýšením `spec.resources.requests.storage` pole:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Ověřte, že se v rámci okruhu PVC i systému souborů zobrazuje nová velikost:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```

## <a name="windows-containers"></a>Kontejnery Windows

Ovladač souborů Azure Files podporuje i uzly a kontejnery Windows. Pokud chcete používat kontejnery Windows, přidejte fond uzlů Windows podle [kurzu Windows Containers](windows-container-cli.md) .

Jakmile budete mít fond uzlů Windows, využijte integrované třídy úložiště jako `azurefile-csi` nebo Vytvářejte vlastní. Můžete nasadit ukázkovou [stavovou sadu založenou na systému Windows](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) , která uloží časová razítka do souboru `data.txt` nasazením níže pomocí příkazu [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Ověřte obsah svazku spuštěním:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Další kroky

- Informace o použití ovladače CSI pro disky Azure najdete v tématu [použití disků Azure s ovladači CSI](azure-disk-csi.md).
- Další informace o osvědčených postupech pro úložiště najdete v tématu [osvědčené postupy pro úložiště a zálohy ve službě Azure Kubernetes Service (AKS)][operator-best-practices-storage] .


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md