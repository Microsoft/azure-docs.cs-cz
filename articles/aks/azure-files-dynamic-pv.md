---
title: Dynamické vytvoření svazku souborů pro více lusků ve službě Azure Kubernetes (AKS)
description: Zjistěte, jak dynamicky vytvořit trvalý svazek se soubory Azure pro použití s několika souběžnými lusky ve službě Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 580363973afd918351931edfb187a1a8d38d6985
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "67665973"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Dynamické vytvoření a použití trvalého svazku se soubory Azure ve službě Azure Kubernetes Service (AKS)

Trvalý svazek představuje část úložiště, která byla zřízena pro použití s Kubernetes lusky. Trvalý svazek lze použít v jednom nebo mnoha luskech a lze jej dynamicky nebo staticky zřídit. Pokud více lusků potřebuje souběžný přístup ke stejnému svazku úložiště, můžete použít soubory Azure pro připojení pomocí [protokolu SMB (Server Message Block)][smb-overview]. V tomto článku se dozvíte, jak dynamicky vytvořit sdílenou složku Azure Files pro použití v několika luskech v clusteru Azure Kubernetes Service (AKS).

Další informace o Kubernetes svazcích najdete v tématu [Možnosti úložiště pro aplikace v AKS][concepts-storage].

## <a name="before-you-begin"></a>Před zahájením

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější. Verzi `az --version` zjistíte spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="create-a-storage-class"></a>Vytvoření třídy úložiště

Třída úložiště se používá k definování způsobu vytvoření sdílené složky Azure. Účet úložiště se automaticky vytvoří ve [skupině prostředků uzlu][node-resource-group] pro použití s třídou úložiště pro ukládání sdílených složek Azure. Vyberte následující redundanci [úložiště Azure][storage-skus] pro *skuName*:

* *Standard_LRS* – standardní místně redundantní úložiště (LRS)
* *Standard_GRS* – standardní geograficky redundantní úložiště (GRS)
* *Standard_RAGRS* – standardní geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)

> [!NOTE]
> Služba soubory Azure podporuje Premium Storage v clusterech AKS se systémem Kubernetes 1,13 nebo vyšším.

Další informace o třídách úložiště Kubernetes pro soubory Azure najdete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes].

Vytvořte soubor s názvem `azure-file-sc.yaml` a zkopírujte ho v následujícím ukázkovém manifestu. Další informace o *mountOptions*najdete v části [Možnosti připojení][mount-options] .

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Vytvořte třídu úložiště pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Vytvoření role clusteru a vazby

Clustery AKS používají řízení přístupu na základě role (RBAC) Kubernetes k omezení akcí, které je možné provést. *Role* definují oprávnění pro udělení a *vazby* je použijí pro požadované uživatele. Tato přiřazení lze použít na daný obor názvů nebo v celém clusteru. Další informace najdete v tématu [použití autorizace RBAC][kubernetes-rbac].

Pokud chcete platformě Azure dovolit, aby vytvořila požadované prostředky úložiště, vytvořte *ClusterRole* a *ClusterRoleBinding*. Vytvořte soubor s názvem `azure-pvc-roles.yaml` a zkopírujte ho do následujícího YAML:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

Přiřaďte oprávnění pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Vytvoření deklarace identity trvalého svazku

Deklarace trvalého svazku (PVC) používá objekt třídy úložiště k dynamickému zřízení sdílené složky Azure. Následující YAML se dají použít k vytvoření deklarace identity trvalého svazku *5 GB* ve velikosti s přístupem *ReadWriteMany* . Další informace o režimech přístupu najdete v dokumentaci k [trvalému svazku Kubernetes][access-modes] .

Teď vytvořte soubor s názvem `azure-file-pvc.yaml` a zkopírujte ho na následující YAML. Ujistěte se, že *storageClassName* odpovídá třídě úložiště vytvořené v posledním kroku:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Pomocí příkazu [kubectl Apply][kubectl-apply] vytvořte deklaraci trvalého svazku:

```console
kubectl apply -f azure-file-pvc.yaml
```

Po dokončení se sdílená složka vytvoří. Vytvoří se také tajný kód Kubernetes, který obsahuje informace o připojení a přihlašovací údaje. K zobrazení stavu virtuálního okruhu (PVC) můžete použít příkaz [kubectl Get][kubectl-get] :

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Použití trvalého svazku

Následující YAML vytvoří pod, který pomocí *azurefile* trvalého objemu deklarací identity připojí sdílenou složku Azure na cestě */mnt/Azure* . Pro kontejnery Windows serveru (aktuálně ve verzi Preview v AKS) zadejte *mountPath* pomocí konvence cesty Windows, třeba *:* .

Vytvořte soubor s názvem `azure-pvc-files.yaml`a zkopírujte následující YAML. Ujistěte se, že tento *argument* se shoduje s virtuálním okruhem vytvořeným v posledním kroku.

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
        claimName: azurefile
```

Vytvořte pod příkazem [kubectl Apply][kubectl-apply] .

```console
kubectl apply -f azure-pvc-files.yaml
```

Teď máte spuštěný pod složkou sdílené složky Azure, která je připojená v adresáři */mnt/Azure* . Tato konfigurace se dá zobrazit při kontrole přes `kubectl describe pod mypod`. Následující zhuštěný příklad výstupu ukazuje svazek připojený do kontejneru:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
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
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Možnosti připojení

Výchozí hodnoty *FileMode* a *dirMode* se mezi verzemi Kubernetes liší, jak je popsáno v následující tabulce.

| version | value |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v 1.8.6 nebo vyšší | 0755 |
| v1.9.0 | 0700 |
| v 1.9.1 nebo vyšší | 0755 |

Pokud používáte cluster verze 1.8.5 nebo vyšší a dynamicky vytváříte trvalý svazek s třídou úložiště, můžete zadat možnosti připojení u objektu třídy úložiště. Následující příklad nastaví *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Pokud používáte cluster verze 1.8.0-1.8.4, je možné určit kontext zabezpečení s hodnotou *runAsUser* nastavenou na *0*. Další informace o kontextu zabezpečení najdete v tématu [Konfigurace kontextu zabezpečení][kubernetes-security-context].

## <a name="next-steps"></a>Další postup

Související osvědčené postupy najdete [v tématu osvědčené postupy pro úložiště a zálohování v AKS][operator-best-practices-storage].

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