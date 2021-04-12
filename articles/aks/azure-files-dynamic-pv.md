---
title: Dynamické vytvoření sdílené složky Azure Files
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak dynamicky vytvořit trvalý svazek se soubory Azure pro použití s několika souběžnými lusky ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 2ad2affee34348e8c2fc7b734c8b49d0aec8db40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744905"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Dynamické vytvoření a použití trvalého svazku se soubory Azure ve službě Azure Kubernetes Service (AKS)

Trvalý svazek představuje část úložiště, která byla zřízena pro použití s Kubernetes lusky. Trvalý svazek lze použít v jednom nebo mnoha luskech a lze jej dynamicky nebo staticky zřídit. Pokud více lusků potřebuje souběžný přístup ke stejnému svazku úložiště, můžete použít soubory Azure pro připojení pomocí [protokolu SMB (Server Message Block)][smb-overview]. V tomto článku se dozvíte, jak dynamicky vytvořit sdílenou složku Azure Files pro použití v několika luskech v clusteru Azure Kubernetes Service (AKS).

Další informace o Kubernetes svazcích najdete v tématu [Možnosti úložiště pro aplikace v AKS][concepts-storage].

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="create-a-storage-class"></a>Vytvoření třídy úložiště

Třída úložiště se používá k definování způsobu vytvoření sdílené složky Azure. Účet úložiště se automaticky vytvoří ve [skupině prostředků uzlu][node-resource-group] pro použití s třídou úložiště pro ukládání sdílených složek Azure. Vyberte následující [redundanci úložiště Azure][storage-skus] pro *skuName*:

* *Standard_LRS* – standardní místně redundantní úložiště (LRS)
* *Standard_GRS* – standardní geograficky redundantní úložiště (GRS)
* *Standard_ZRS* – standardní zóna redundantního úložiště (ZRS)
* *Standard_RAGRS* – standardní geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)
* *Premium_LRS* – Premium místně redundantní úložiště (LRS)
* *Premium_ZRS* – redundantní úložiště zóny Premium (ZRS)

> [!NOTE]
> Služba soubory Azure podporuje Prémiové úložiště v clusterech AKS, které používají Kubernetes 1,13 nebo vyšší, je minimální úroveň Premium pro sdílení souborů 100 GB

Další informace o třídách úložiště Kubernetes pro soubory Azure najdete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes].

Vytvořte soubor s názvem `azure-file-sc.yaml` a zkopírujte ho v následujícím ukázkovém manifestu. Další informace o *mountOptions* najdete v části [Možnosti připojení][mount-options] .

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

Vytvořte třídu úložiště pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Vytvoření deklarace identity trvalého svazku

Deklarace trvalého svazku (PVC) používá objekt třídy úložiště k dynamickému zřízení sdílené složky Azure. Následující YAML se dají použít k vytvoření deklarace identity trvalého objemu *5 GB* s přístupem *ReadWriteMany* . Další informace o režimech přístupu najdete v dokumentaci k [trvalému svazku Kubernetes][access-modes] .

Teď vytvořte soubor s názvem `azure-file-pvc.yaml` a zkopírujte ho na následující YAML. Ujistěte se, že *storageClassName* odpovídá třídě úložiště vytvořené v posledním kroku:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: my-azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> Pokud používáte skladové položky *Premium_LRS* pro třídu úložiště, musí být minimální hodnota *úložiště* *100Gi*.

Pomocí příkazu [kubectl Apply][kubectl-apply] vytvořte deklaraci trvalého svazku:

```console
kubectl apply -f azure-file-pvc.yaml
```

Po dokončení se sdílená složka vytvoří. Vytvoří se také tajný kód Kubernetes, který obsahuje informace o připojení a přihlašovací údaje. K zobrazení stavu virtuálního okruhu (PVC) můžete použít příkaz [kubectl Get][kubectl-get] :

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Použití trvalého svazku

Následující YAML vytvoří položku, která používá trvalý objem deklarací identity *My-azurefile* pro připojení sdílené složky Azure v cestě */mnt/Azure* . V případě kontejnerů Windows serveru určete *mountPath* pomocí konvence cesty Windows, třeba *:*.

Vytvořte soubor s názvem `azure-pvc-files.yaml` a zkopírujte následující YAML. Ujistěte se, že tento *argument* se shoduje s virtuálním okruhem vytvořeným v posledním kroku.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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
        claimName: my-azurefile
```

Vytvořte pod příkazem [kubectl Apply][kubectl-apply] .

```console
kubectl apply -f azure-pvc-files.yaml
```

Teď máte spuštěný pod složkou sdílené složky Azure, která je připojená v adresáři */mnt/Azure* . Tato konfigurace se dá zobrazit při kontrole přes `kubectl describe pod mypod` . Následující zhuštěný příklad výstupu ukazuje svazek připojený do kontejneru:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  my-azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Možnosti připojení

Výchozí hodnota pro *FileMode* a *dirMode* je *0777* pro Kubernetes verze 1.13.0 a vyšší. Při dynamickém vytváření trvalého svazku s třídou úložiště lze zadat možnosti připojení v objektu třídy úložiště. Následující příklad nastaví *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Další kroky

Související osvědčené postupy najdete [v tématu osvědčené postupy pro úložiště a zálohování v AKS][operator-best-practices-storage].

Parametry třídy úložiště najdete v tématu [dynamické zřizování](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision).

Přečtěte si další informace o Kubernetes trvalých svazcích pomocí souborů Azure.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro soubory Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
