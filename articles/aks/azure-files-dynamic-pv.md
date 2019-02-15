---
title: Dynamicky se vytvářejí soubory svazku pro několik podů se ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak dynamicky se vytvářejí trvalý svazek se soubory Azure pro použití s více souběžných podů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 2cf9a98a2f27c9088266a976118acdb56f8a65d7
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300818"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Dynamicky vytvořit a použít trvalý svazek se soubory Azure ve službě Azure Kubernetes Service (AKS)

Trvalý svazek představuje část úložiště, která byla zřízena pro použití s podů Kubernetes. Trvalý svazek může využívat jeden nebo více podů a je možné dynamicky nebo staticky zřídit. Pokud potřebujete více podů souběžný přístup na stejný svazek úložiště, můžete používat soubory Azure a připojte se pomocí [zprávy bloku SMB (Server) protokol][smb-overview]. Tento článek popisuje, jak dynamicky se vytvářejí sdílenou složku služby soubory Azure používají několik podů se v clusteru služby Azure Kubernetes Service (AKS).

Další informace o Kubernetes trvalé svazky, naleznete v tématu [trvalé svazky Kubernetes][kubernetes-volumes].

## <a name="before-you-begin"></a>Před zahájením

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Také musíte mít nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.46 nebo novější. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="create-a-storage-class"></a>Vytvořte třídu úložiště

Třída úložiště se používá k definování, jak se vytvoří sdílené složky Azure. Účet úložiště se automaticky vytvoří v *_MC* skupiny prostředků pro použití s třídou úložiště pro uložení sdílené složky Azure. Zvolit z následujících [redundance služby Azure storage] [ storage-skus] pro *skuName*:

* *Standard_LRS* – standardní místně redundantní úložiště (LRS)
* *Standard_GRS* – standardní geograficky redundantní úložiště (GRS)
* *Standard_RAGRS* – standardní geograficky redundantní úložiště jen pro čtení (RA-GRS)

> [!NOTE]
> Azure Files aktuálně pouze práce s úložiště úrovně Standard. Pokud používáte službu Premium storage, nepodaří svazek zřídit.

Další informace o Kubernetes třídy úložiště pro soubory Azure najdete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes].

Vytvořte soubor s názvem `azure-file-sc.yaml` a kopie v následujícím příkladu manifestu. Další informace o *mountOptions*, najdete v článku [možnosti připojení] [ mount-options] oddílu.

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

Vytvoření třídy úložiště se [použití kubectl] [ kubectl-apply] příkaz:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Vytvoření role clusteru a vazby

AKS clustery pomocí Kubernetes řízení přístupu na základě rolí (RBAC) k omezení akcí, které lze provést. *Role* definovat oprávnění udělit, a *vazby* platí pro požadovaného uživatele. Tato přiřazení můžete použít k daném oboru názvů nebo přes celý cluster. Další informace najdete v tématu [pomocí RBAC se podařilo Autorizovat][kubernetes-rbac].

Povolit platformu Azure k vytvoření prostředků úložiště, vytvořte *ClusterRole* a *ClusterRoleBinding*. Vytvořte soubor s názvem `azure-pvc-roles.yaml` a zkopírujte do následující kód YAML:

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

Přiřazení oprávnění s [použití kubectl] [ kubectl-apply] příkaz:

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Vytvořit deklaraci identity trvalý svazek

Trvalý svazek deklarace identity (PVC) používá objekt třídy úložiště dynamicky zřizovat sdílené složky Azure. Následující kód YAML, je možné vytvořit deklaraci identity trvalý svazek *5GB* velikost s *ReadWriteMany* přístup. Další informace o režimy přístupu najdete v článku [trvalý svazek Kubernetes] [ access-modes] dokumentaci.

Teď vytvořte soubor s názvem `azure-file-pvc.yaml` a zkopírujte do následující kód YAML. Ujistěte se, že *storageClassName* odpovídá třídy úložiště vytvořený v předchozím kroku:

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

Vytvořit deklaraci trvalý svazek s [použití kubectl] [ kubectl-apply] příkaz:

```console
kubectl apply -f azure-file-pvc.yaml
```

Po dokončení se vytvoří sdílené složky. Tajného kódu Kubernetes se také vytvoří, který obsahuje informace o připojení a přihlašovací údaje. Můžete použít [kubectl get] [ kubectl-get] příkazu zobrazte stav PVC:

```
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Použít trvalý svazek

Následující kód YAML vytvoří pod, která používá deklarace identity trvalý svazek *azurefile* pro připojení sdílené složky Azure v */mnt/azure* cestu.

Vytvořte soubor s názvem `azure-pvc-files.yaml`a zkopírujte do následující kód YAML. Ujistěte se, že *claimName* odpovídá PVC vytvořili v předchozím kroku.

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

Vytvořte pod s [použití kubectl] [ kubectl-apply] příkazu.

```console
kubectl apply -f azure-pvc-files.yaml
```

Teď máte spuštěné pod s vaší sdílenou složku služby soubory Azure připojené */mnt/azure* adresáře. Tato konfigurace se zobrazí při kontrole podu prostřednictvím `kubectl describe pod mypod`. Následující výstup zhuštěnému příkladu ukazuje svazek připojený v kontejneru:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Wed, 15 Aug 2018 22:22:27 +0000
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

Výchozí *režimu souboru fileMode* a *dirMode* hodnoty se liší mezi verzemi Kubernetes, jak je popsáno v následující tabulce.

| version | hodnota |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 nebo novější | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 nebo novější | 0755 |

Pokud používáte cluster verze 1.8.5 nebo vyšší a dynamicky se vytvoří trvalý svazek s třídou úložiště, možnosti připojení se dá nastavit pro objekt třídu úložiště. Následující příklad nastaví *0777*:

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

Pokud používáte cluster verze 1.8.5 nebo vyšší a staticky vytváření objektu trvalý svazek, možnosti připojení musí být zadána na *PersistentVolume* objektu. Další informace o vytvoření staticky trvalý svazek najdete v tématu [statické trvalé svazky][pv-static].

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
```

Pokud používáte cluster verze 1.8.0 - 1.8.4, kontext zabezpečení je možné zadat při *Spustit_jako_uživatel* nastavena na hodnotu *0*. Další informace o kontextu zabezpečení Pod najdete v tématu [konfigurace kontext zabezpečení][kubernetes-security-context].

## <a name="next-steps"></a>Další postup

Další informace o trvalé svazky Kubernetes pomocí služby soubory Azure.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro Azure Files][kubernetes-files]

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
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
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
