---
title: Použít Azure soubor s AKS
description: Disky systému Azure pomocí AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: dbd1b171185bf49ed5e633153938d3e7ba81387d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Trvalé svazky s soubory Azure

Trvalé svazku představuje část úložiště, které se zřizují pro použití v clusteru s podporou Kubernetes. Trvalé svazku může používat jednoho nebo mnoha pracovními stanicemi soustředěnými kolem a můžete dynamicky nebo staticky zřídit. Tento dokument podrobně popisuje dynamické zřizování sdílenou složku Azure jako trvalé svazek Kubernetes v clusteru služby AKS.

Další informace o Kubernetes trvalé svazky, najdete v části [trvalé svazky Kubernetes][kubernetes-volumes].

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

Při zřizování dynamicky sdílenou složku Azure jako Kubernetes svazek, můžete tak dlouho, dokud je obsažena ve stejné skupině prostředků jako AKS cluster použít libovolný účet úložiště. V případě potřeby vytvořte účet úložiště ve stejné skupině prostředků jako AKS cluster.

Chcete-li identifikovat odpovídající prostředek skupiny, použijte [seznam skupiny az] [ az-group-list] příkaz.

```azurecli-interactive
az group list --output table
```

Hledáte skupinu prostředků s názvem podobná `MC_clustername_clustername_locaton`, kde clustername představuje název clusteru AKS a umístění je oblast Azure, kde byla nasazena do clusteru.

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Použití [vytvořit účet úložiště az] [ az-storage-account-create] příkaz pro vytvoření účtu úložiště.

V tomto příkladu, aktualizovat `--resource-group` s názvem skupiny prostředků a `--name` název svého výběru.

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Vytvoření třídy úložiště

Třídy úložiště se používá k definování, jak vytvořit sdílenou složku Azure. Účet konkrétní úložiště lze zadat v třídě. Pokud není zadán účet úložiště, `skuName` a `location` musí být zadán, a všechny účty úložiště ve skupině prostředků přidružené vyhodnocují shody.

Další informace o Kubernetes třídy úložiště pro soubory Azure najdete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes].

Vytvořte soubor s názvem `azure-file-sc.yaml` a zkopírujte následující manifestu. Aktualizace `storageAccount` s názvem cílového účtu úložiště.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

Vytvoření třídy úložiště s [kubectl vytvořit] [ kubectl-create] příkaz.

```azurecli-interactive
kubectl create -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Vytvoření svazku trvalé deklarace identity

Trvalé svazku deklarace identity (PVC) používá objektu třídy úložiště pro dynamicky zajišťují sdílenou složku Azure.

Následující manifest je použít k vytvoření svazku trvalé deklarace `5GB` velikost `ReadWriteOnce` přístup.

Vytvořte soubor s názvem `azure-file-pvc.yaml` a zkopírujte následující manifestu. Ujistěte se, že `storageClassName` odpovídá třídy úložiště vytvořili v předchozím kroku.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Vytvoření svazku trvalé deklarace s [kubectl vytvořit] [ kubectl-create] příkaz.

```azurecli-interactive
kubectl create -f azure-file-pvc.yaml
```

Po dokončení, vytvoří se sdílené složky. Kubernetes tajný klíč je vytvořen také obsahující informace o připojení a přihlašovací údaje.

## <a name="using-the-persistent-volume"></a>Použití trvalé svazku

Následující manifest vytvoří pod, která používá deklarace trvalé svazku `azurefile` připojit sdílenou složkou Azure v `/mnt/azure` cesta.

Vytvořte soubor s názvem `azure-pvc-files.yaml`a zkopírujte následující manifestu. Ujistěte se, že `claimName` odpovídá PVC vytvořili v předchozím kroku.

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
        claimName: azurefile
```

Vytvoření pod s [kubectl vytvořit] [ kubectl-create] příkaz.

```azurecli-interactive
kubectl create -f azure-pvc-files.yaml
```

Nyní máte spuštěný pod s Azure diskem připojené `/mnt/azure` adresáře. Tato konfigurace si můžete prohlédnout při kontrole vaší pod prostřednictvím `kubectl describe pod mypod`.

## <a name="mount-options"></a>Možnosti připojení

Výchozí hodnoty fileMode a dirMode liší mezi verzemi Kubernetes, jak je popsáno v následující tabulce.

| verze | hodnota |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 nebo novější | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 nebo novější | 0755 |

Pokud používáte cluster verze 1.8.5 nebo větší, přípojných možnosti lze zadat v objektu třídy úložiště. Následující příklad sady `0777`.

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

Pokud používáte cluster verze 1.8.0 - 1.8.4, lze určit kontext zabezpečení `runAsUser` nastavena na hodnotu `0`. Další informace o kontextu zabezpečení Pod najdete v tématu [konfigurace kontextu zabezpečení][kubernetes-security-context].

## <a name="next-steps"></a>Další postup

Další informace o Kubernetes trvalé svazky s využitím Azure Files.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro Azure Files][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
