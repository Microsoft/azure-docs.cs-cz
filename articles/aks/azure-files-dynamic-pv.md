---
title: Použijte Azure soubor s AKS
description: Použití disků v Azure s AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bac80e354a4d629bfbfc8207b9fed7c69c765839
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429263"
---
# <a name="persistent-volumes-with-azure-files"></a>Trvalé svazků se soubory Azure

Trvalý svazek je část úložiště, které byly vytvořeny pro použití v clusteru Kubernetes. Trvalý svazek může využívat jeden nebo více podů a je možné dynamicky nebo staticky vytvořit. Tento dokument podrobně popisuje **dynamické vytváření** ze sdílené složky Azure jako trvalý svazek.

Další informace o Kubernetes najdete v článku trvalé svazky, včetně statických vytvoření [trvalé svazky Kubernetes][kubernetes-volumes].

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

Při vytváření dynamicky sdílené složky Azure jako svazek Kubernetes, libovolný účet úložiště je možné, dokud se AKS **uzel** skupinu prostředků. To je ten, který se `MC_` předponu, která vytvořil zřizování prostředků pro AKS clusteru. Získání názvu skupiny prostředků s [az resource show] [ az-resource-show] příkazu.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Použití [vytvořit účet úložiště az] [ az-storage-account-create] příkaz k vytvoření účtu úložiště.

Aktualizace `--resource-group` s názvem skupiny prostředků získané v předchozím kroku, a `--name` název podle vašeho výběru.

```azurecli-interactive
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

> Služba soubory Azure pouze aktuálně pracovat úložiště úrovně standard. Pokud používáte službu premium storage, se zřizování nezdaří svazku.

## <a name="create-storage-class"></a>Vytvořte třídu úložiště

Třída úložiště se používá k definování, jak se vytvoří sdílené složky Azure. Konkrétnímu účtu úložiště se dá nastavit ve třídě. Pokud není zadaný účet úložiště, `skuName` a `location` musí být zadána, a shody se vyhodnocují všechny účty úložiště ve skupině prostředků přidružené.

Další informace o Kubernetes třídy úložiště pro soubory Azure najdete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes].

Vytvořte soubor s názvem `azure-file-sc.yaml` a zkopírujte do následující manifestu. Aktualizace `storageAccount` s názvem cílový účet úložiště. V části [možnosti připojení] Další informace na `mountOptions`.

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

Vytvoření třídy úložiště se [použití kubectl] [ kubectl-apply] příkazu.

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Vytvoření deklarace identity trvalý svazek

Trvalý svazek deklarace identity (PVC) používá objekt třídy úložiště dynamicky zřizovat sdílené složky Azure.

Následující kód YAML, je možné vytvořit deklaraci identity trvalý svazek `5GB` velikost s `ReadWriteMany` přístup. Další informace o režimy přístupu najdete v článku [trvalý svazek Kubernetes] [ access-modes] dokumentaci.

Vytvořte soubor s názvem `azure-file-pvc.yaml` a zkopírujte do následující kód YAML. Ujistěte se, že `storageClassName` odpovídá třídy úložiště vytvořený v předchozím kroku.

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

Vytvořit deklaraci trvalý svazek s [použití kubectl] [ kubectl-apply] příkazu.

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

Po dokončení se vytvoří sdílené složky. Tajného kódu Kubernetes se také vytvoří, který obsahuje informace o připojení a přihlašovací údaje.

## <a name="using-the-persistent-volume"></a>Pomocí trvalý svazek

Následující kód YAML vytvoří pod, která používá deklarace identity trvalý svazek `azurefile` pro připojení sdílené složky Azure v `/mnt/azure` cestu.

Vytvořte soubor s názvem `azure-pvc-files.yaml`a zkopírujte do následující kód YAML. Ujistěte se, že `claimName` odpovídá PVC vytvořili v předchozím kroku.

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

Vytvořte pod s [použití kubectl] [ kubectl-apply] příkazu.

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
```

Teď máte spuštěné pod s Azure disku připojené `/mnt/azure` adresáře. Tato konfigurace se zobrazí při kontrole podu prostřednictvím `kubectl describe pod mypod`.

## <a name="mount-options"></a>Možnosti připojení

Výchozí hodnoty režimu souboru fileMode a dirMode lišit mezi verzemi Kubernetes, jak je popsáno v následující tabulce.

| verze | hodnota |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 nebo novější | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 nebo novější | 0755 |

Pokud používáte cluster verze 1.8.5 nebo vyšší a dynamicky vytvořit svazek trvalá s třídou úložiště, možnosti připojení se dá nastavit pro objekt třídu úložiště. Následující příklad nastaví `0777`.

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

Pokud používáte cluster verze 1.8.5 nebo vyšší a staticky vytváření objektu svazku trvalá, možnosti připojení musí být zadána na `PersistentVolume` objektu. Další informace o staticky při vytváření svazku trvalá, naleznete v tématu [statické trvalé svazky][pv-static].

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

Pokud používáte cluster verze 1.8.0 - 1.8.4, kontext zabezpečení je možné zadat při `runAsUser` nastavena na hodnotu `0`. Další informace o kontextu zabezpečení Pod najdete v tématu [konfigurace kontext zabezpečení][kubernetes-security-context].

## <a name="next-steps"></a>Další postup

Další informace o trvalé svazky Kubernetes pomocí služby soubory Azure.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro Azure Files][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
