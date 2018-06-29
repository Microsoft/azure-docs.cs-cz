---
title: Použít Azure soubor s AKS
description: Disky systému Azure pomocí AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 84500791887194884e1ec7d15ddfbc169ba22517
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098341"
---
# <a name="persistent-volumes-with-azure-files"></a>Trvalé svazky s soubory Azure

Trvalé svazku je úložiště, který byl vytvořen pro použití v clusteru s podporou Kubernetes. Trvalé svazek může používat jednoho nebo mnoha pracovními stanicemi soustředěnými kolem a lze dynamicky nebo staticky vytvořit. Tento dokument podrobnosti **dynamického vytváření** sdílené složky Azure file jako trvalé svazek.

Další informace o Kubernetes trvalé svazky, včetně statické vytváření, najdete v části [trvalé svazky Kubernetes][kubernetes-volumes].

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

Při vytváření dynamicky sdílenou složku Azure jako Kubernetes svazek, můžete použít libovolný účet úložiště, dokud se AKS **uzlu** skupinu prostředků. Toto je to se `MC_` předponu, která byla vytvořená zřizování prostředků pro AKS clusteru. Získat název skupiny prostředků s [az prostředků zobrazit] [ az-resource-show] příkaz.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Použití [vytvořit účet úložiště az] [ az-storage-account-create] příkaz pro vytvoření účtu úložiště.

Aktualizace `--resource-group` s názvem skupiny prostředků získané v posledním kroku, a `--name` název svého výběru.

```azurecli-interactive
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

> Soubory Azure aktuálně pouze pracovat standardní úložiště. Pokud používáte storage úrovně premium, se zřizování nezdaří svazku.

## <a name="create-storage-class"></a>Vytvoření třídy úložiště

Třídy úložiště se používá k definování, jak vytvořit sdílenou složku Azure. Účet konkrétní úložiště lze zadat v třídě. Pokud není zadán účet úložiště, `skuName` a `location` musí být zadán, a všechny účty úložiště ve skupině prostředků přidružené vyhodnocují shody.

Další informace o Kubernetes třídy úložiště pro soubory Azure najdete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes].

Vytvořte soubor s názvem `azure-file-sc.yaml` a zkopírujte následující manifestu. Aktualizace `storageAccount` s názvem cílového účtu úložiště. Najdete v části [možnosti připojení] Další informace na `mountOptions`.

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

Vytvoření třídy úložiště s [kubectl použít] [ kubectl-apply] příkaz.

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Vytvoření svazku trvalé deklarace identity

Trvalé svazku deklarace identity (PVC) používá objektu třídy úložiště pro dynamicky zajišťují sdílenou složku Azure.

Následující YAML lze použít k vytvoření svazku trvalé deklarace `5GB` velikost `ReadWriteMany` přístup. Další informace o režimy přístupu najdete v tématu [svazku trvalé Kubernetes] [ access-modes] dokumentaci.

Vytvořte soubor s názvem `azure-file-pvc.yaml` a zkopírujte následující YAML. Ujistěte se, že `storageClassName` odpovídá třídy úložiště vytvořili v předchozím kroku.

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

Vytvoření svazku trvalé deklarace s [kubectl použít] [ kubectl-apply] příkaz.

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

Po dokončení, vytvoří se sdílené složky. Kubernetes tajný klíč je vytvořen také obsahuje informace o připojení a přihlašovací údaje.

## <a name="using-the-persistent-volume"></a>Použití trvalé svazku

Následující YAML vytvoří pod, která používá deklarace trvalé svazku `azurefile` připojit sdílenou složkou Azure v `/mnt/azure` cesta.

Vytvořte soubor s názvem `azure-pvc-files.yaml`a zkopírujte následující YAML. Ujistěte se, že `claimName` odpovídá PVC vytvořili v předchozím kroku.

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

Vytvoření pod s [kubectl použít] [ kubectl-apply] příkaz.

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
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

Pokud používáte cluster verze 1.8.5 nebo vyšší a dynamicky vytvoření svazku trvalá pomocí třídy úložiště, možnosti připojení lze zadat v objektu třídy úložiště. Následující příklad sady `0777`.

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

Pokud používáte cluster verze 1.8.5 nebo vyšší a staticky vytváření objektu svazku trvalá, možnosti připojení je potřeba zadat na `PersistentVolume` objektu. Další informace o vytváření staticky svazek trvalá najdete v tématu [statické trvalé svazky][pv-static].

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

Pokud používáte cluster verze 1.8.0 - 1.8.4, lze určit kontext zabezpečení `runAsUser` nastavena na hodnotu `0`. Další informace o kontextu zabezpečení Pod najdete v tématu [konfigurace kontextu zabezpečení][kubernetes-security-context].

## <a name="next-steps"></a>Další postup

Další informace o Kubernetes trvalé svazky s využitím Azure Files.

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
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[mount-options]: #mount-options
