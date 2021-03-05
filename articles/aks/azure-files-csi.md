---
title: Použití ovladačů rozhraní úložiště kontejnerů (CSI) pro Azure Files ve službě Azure Kubernetes (AKS)
description: Naučte se používat ovladače rozhraní pro kontejnerové úložiště (CSI) pro soubory Azure v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 93f7f7a3c59beca362145ac16f7cf727df773f81
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174057"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Použití ovladačů rozhraní pro kontejnerové úložiště Azure Files ve službě Azure Kubernetes (AKS) (Preview)

Ovladač rozhraní Container Storage (CSI) pro Azure Files je ovladač kompatibilní se [specifikací CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md), který používá služba Azure Kubernetes Service (AKS) ke správě životního cyklu sdílených složek Azure Files.

Soubory CSI jsou standardem pro vystavení libovolných systémů blokování a souborové úložiště pro vytvoření kontejnerů úloh v Kubernetes. Po přijetí a použití rozhraní CSI může AKS nyní zapisovat, nasazovat a iterovat moduly plug-in, aby vystavoval nové nebo vylepšit stávající systémy úložiště v Kubernetes, aniž by se museli podotknout základního kódu Kubernetes a počkat na jeho cykly vydávání.

Pokud chcete vytvořit cluster AKS s podporou ovladače CSI, přečtěte si téma [Povolení ovladačů rozhraní CSI pro disky Azure a soubory Azure v AKS](csi-storage-drivers.md).

>[!NOTE]
> *Ovladače stromové struktury* odkazují na aktuální ovladače úložiště, které jsou součástí základního kódu Kubernetes, oproti novým ovladačům CSI, které jsou moduly plug-in.

## <a name="use-a-persistent-volume-with-azure-files"></a>Použití trvalého svazku se soubory Azure

[Trvalý svazek (PV)](concepts-storage.md#persistent-volumes) představuje úložiště, které je zřízené pro použití s Kubernetes lusky. Souč_hod lze použít v jednom nebo mnoha luskech a lze ji dynamicky nebo staticky zřídit. Pokud více lusků potřebuje souběžný přístup ke stejnému svazku úložiště, můžete použít soubory Azure pro připojení pomocí [protokolu SMB (Server Message Block)][smb-overview]. V tomto článku se dozvíte, jak dynamicky vytvořit sdílenou složku souborů Azure pro použití v několika luskech v clusteru AKS. Informace o statickém zřizování najdete v tématu [Ruční vytvoření a použití svazku se sdílenou složkou souborů Azure](azure-files-volume.md).

Další informace o Kubernetes svazcích najdete v tématu [Možnosti úložiště pro aplikace v AKS][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Dynamické vytváření PVs souborů Azure pomocí integrovaných tříd úložiště

Třída úložiště se používá k definování, jak se vytvoří sdílená složka souborů Azure. Účet úložiště se automaticky vytvoří ve [skupině prostředků uzlu][node-resource-group] pro použití s třídou úložiště pro ukládání sdílených složek Azure Files. Pro *skuName* vyberte jednu z následujících [SKU Azure Storage pro redundanci][storage-skus] :

* **Standard_LRS**: standardní místně redundantní úložiště
* **Standard_GRS**: standardní geograficky redundantní úložiště
* **Standard_ZRS**: standardní zóna – redundantní úložiště
* **Standard_RAGRS**: standardní geograficky redundantní úložiště s přístupem pro čtení
* **Premium_LRS**: místně redundantní úložiště úrovně Premium

> [!NOTE]
> Azure Files podporuje Azure Premium Storage. Minimální sdílená složka Premium je 100 GB.

Když použijete ovladače úložiště CSI na AKS, jsou k dispozici dva další integrované `StorageClasses` , které využívají ovladače úložiště Azure Files CSI. Další třídy úložiště CSI se vytvoří s clusterem společně s výchozími třídami úložiště ve stromové struktuře.

- `azurefile-csi`: Používá službu Azure Storage úrovně Standard k vytvoření sdílené složky Azure Files.
- `azurefile-csi-premium`: Používá Azure Premium Storage k vytvoření sdílené složky služby soubory Azure.

Zásady opětovné deklarace v obou třídách úložiště zajistí, že se při odstranění příslušné PV odstraní i podkladová sdílená složka souborů Azure. Třídy úložiště také nakonfigurují sdílené složky tak, aby se rozšířily, stačí upravit deklaraci trvalého svazku (PVC) s novou velikostí.

Chcete-li použít tyto třídy úložiště, vytvořte [trvalý virtuální okruh](concepts-storage.md#persistent-volume-claims) a příslušné na něm, které se na něj odkazují a používají je. K automatickému zřízení úložiště založeného na třídě úložiště slouží virtuální okruh (PVC). Virtuální okruh (PVC) může použít jednu z předem vytvořených tříd úložiště nebo uživatelsky definované třídy úložiště k vytvoření sdílené složky Azure Files pro požadovanou SKU a velikost. Když vytváříte definici pod, je pro ni určen virtuální okruh, který požaduje požadované úložiště.

Vytvořte [ukázkový okruh PVC a potom, který zobrazí aktuální datum do objektu `outfile` ](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Po je-li v běžícím stavu, můžete ověřit, zda je sdílená složka správně připojena, spuštěním následujícího příkazu a ověřením výstupního souboru obsahuje `outfile` :

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

Vytvoří [snímek svazku](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) z virtuálního okruhu, který [jsme dynamicky vytvořili na začátku tohoto kurzu](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) `pvc-azurefile` .


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

## <a name="resize-a-persistent-volume"></a>Změna velikosti trvalého svazku

Pro virtuální okruh (PVC) si můžete vyžádat větší objem. Upravte objekt PVC a zadejte větší velikost. Tato změna aktivuje rozšíření základního svazku, který vrací souč_hod.

> [!NOTE]
> Nová souč_hod se nikdy nevytvořila, aby vyhověla deklaraci identity. Místo toho se změnila velikost stávajícího svazku.

Integrovaná `azurefile-csi` třída úložiště v AKS již podporuje rozšíření, takže použijte [virtuální okruh (PVC), který byl vytvořen dříve s touto třídou úložiště](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). Virtuální okruh (PVC) požadoval sdílenou složku 100Gi. Můžeme potvrdit, že je možné spustit:

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


## <a name="nfs-file-shares"></a>Sdílené složky NFS
[Soubory Azure teď podporují protokol NFS verze 4.1](../storage/files/storage-files-how-to-create-nfs-shares.md). Podpora NFS 4,1 pro soubory Azure poskytuje plně spravovaný systém souborů NFS jako službu postavenou na vysoce dostupné a vysoce odolné platformě odolného úložiště.

 Tato možnost je optimalizovaná pro úlohy s náhodným přístupem s aktualizacemi místních dat a poskytuje úplnou podporu systému souborů POSIX. V této části se dozvíte, jak používat sdílené složky NFS s ovladačem Azure File CSI v clusteru AKS.

Nezapomeňte zkontrolovat [omezení](../storage/files/storage-files-compare-protocols.md#limitations) a [dostupnost oblasti](../storage/files/storage-files-compare-protocols.md#regional-availability) v rámci fáze Preview.

### <a name="register-the-allownfsfileshares-preview-feature"></a>Registrace `AllowNfsFileShares` funkce Preview

Pokud chcete vytvořit sdílenou složku, která využívá NFS 4,1, musíte `AllowNfsFileShares` u svého předplatného povolit příznak funkce.

Příznak funkce Zaregistrujte `AllowNfsFileShares` pomocí příkazu [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Zobrazení stavu v *registraci* trvá několik minut. Pomocí příkazu [AZ Feature list][az-feature-list] ověřte stav registrace:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. Storage* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Vytvoření účtu úložiště pro sdílenou složku NFS

[Vytvořit `Premium_LRS` Účet služby Azure Storage](../storage/files/storage-how-to-create-file-share.md) s následujícími konfiguracemi pro podporu sdílených složek NFS:
- druh účtu: úložiště
- je vyžadován zabezpečený přenos (povolit pouze provoz HTTPS): false
- Vyberte virtuální síť uzlů agentů v bránách firewall a virtuálních sítích, takže můžete chtít vytvořit účet úložiště ve skupině prostředků MC_.

### <a name="create-nfs-file-share-storage-class"></a>Vytvoření souborové třídy úložiště sdílené složky systému souborů NFS

Uložte `nfs-sc.yaml` soubor s manifestem níže úpravou příslušných zástupných symbolů.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

Po úpravě a uložení souboru vytvořte třídu úložiště pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Vytvoření nasazení se sdílenou složkou zálohovanou systémem souborů NFS
Můžete nasadit ukázkovou [stavovou sadu](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) , která uloží časová razítka do souboru `data.txt` nasazením následujícího příkazu pomocí příkazu [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

Ověřte obsah svazku spuštěním:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Vzhledem k tomu, že se sdílená složka NFS nachází v účtu Premium, minimální velikost sdílené složky je 100 GB. Pokud vytvoříte virtuální okruh (PVC) s malým množstvím úložiště, může dojít k chybě při vytváření sdílené složky... velikost (5)... ".


## <a name="windows-containers"></a>Kontejnery Windows

Ovladač Azure Files CSI podporuje i uzly a kontejnery Windows. Pokud chcete používat kontejnery Windows, přidejte fond uzlů Windows podle [kurzu Windows Containers](windows-container-cli.md) .

Po použití fondu uzlů Windows použijte předdefinované třídy úložiště, například `azurefile-csi` nebo vytvořte vlastní. Můžete nasadit ukázkovou [stavovou sadu založenou na Windows](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) , která uloží časová razítka do souboru `data.txt` nasazením následujícího příkazu pomocí příkazu [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
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

- Informace o tom, jak používat ovladače CSI pro disky Azure, najdete v tématu [použití disků Azure s ovladači CSI](azure-disk-csi.md).
- Další informace o osvědčených postupech pro úložiště najdete v tématu [osvědčené postupy pro úložiště a zálohování ve službě Azure Kubernetes][operator-best-practices-storage].


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md