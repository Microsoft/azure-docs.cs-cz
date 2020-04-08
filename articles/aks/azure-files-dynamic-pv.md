---
title: Dynamicky vytvářet sdílené složky Azure Files
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak dynamicky vytvořit trvalý svazek se soubory Azure pro použití s více souběžnými pody ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/12/2019
ms.openlocfilehash: 59b773cd4608187fedb24358eac57715e1c271ea
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803530"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Dynamické vytváření a používání trvalého svazku se soubory Azure ve službě Azure Kubernetes Service (AKS)

Trvalý svazek představuje část úložiště, která byla zřízena pro použití s pody Kubernetes. Trvalý svazek může používat jeden nebo mnoho podů a může být dynamicky nebo staticky zřízena. Pokud více podů potřebují souběžný přístup ke stejnému svazku úložiště, můžete použít soubory Azure pro připojení pomocí [protokolu Server Message Block (SMB).][smb-overview] Tento článek ukazuje, jak dynamicky vytvořit sdílené složky Azure Files pro použití více pody v clusteru služby Azure Kubernetes Service (AKS).

Další informace o svazcích Kubernetes najdete [v tématu Možnosti úložiště pro aplikace v AKS][concepts-storage].

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované verze Azure CLI verze 2.0.59 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="create-a-storage-class"></a>Vytvoření třídy úložiště

Třída úložiště se používá k definování, jak se vytvoří sdílená složka Azure. Účet úložiště se automaticky vytvoří ve [skupině prostředků uzlu][node-resource-group] pro použití s třídou úložiště pro uložení sdílených složek souborů Azure. Vyberte si z následující [redundance úložiště Azure][storage-skus] pro *skuName*:

* *Standard_LRS* - standardní místně redundantní úložiště (LRS)
* *Standard_GRS* - standardní geograficky redundantní úložiště (GRS)
* *Standard_ZRS* - standardní zónové redundantní úložiště (ZRS)
* *Standard_RAGRS* - standardní geograficky redundantní úložiště pro čtení (RA-GRS)
* *Premium_LRS* - prémiové místně redundantní úložiště (LRS)
* *Premium_ZRS* - redundantní úložiště prémiové zóny (GRS)

> [!NOTE]
> Azure Files podporují úložiště premium v clusterech AKS, které běží Kubernetes 1.13 nebo vyšší, minimální sdílení souborů premium je 100 GB

Další informace o třídách úložiště Kubernetes pro soubory Azure najdete v [tématu Kubernetes Storage Classes][kubernetes-storage-classes].

Vytvořte soubor `azure-file-sc.yaml` s názvem a zkopírujte v následujícím příkladu manifestu. Další informace o *možnostech připojení*naleznete v části [Možnosti připojení.][mount-options]

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

Vytvořte třídu úložiště pomocí příkazu [použít kubectl:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Vytvoření trvalé deklarace svazku

Trvalá deklarace svazku (PVC) používá objekt třídy úložiště k dynamickému zřizování sdílené složky Azure. Následující YAML lze použít k vytvoření trvalé deklarace svazku *5 GB* velikosti s *přístupem ReadWriteMany.* Další informace o režimech přístupu naleznete v dokumentaci [k trvalému svazku Kubernetes.][access-modes]

Nyní vytvořte `azure-file-pvc.yaml` soubor s názvem a zkopírujte v následujícím YAML. Ujistěte se, že *storageClassName* odpovídá třídě úložiště vytvořené v posledním kroku:

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

> [!NOTE]
> Pokud používáte *Premium_LRS* skladovou položku pro vaši třídu úložiště, musí být minimální hodnota *úložiště* *100Gi*.

Vytvořte deklaraci trvalého svazku pomocí příkazu [kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-pvc.yaml
```

Po dokončení bude vytvořena sdílená složka. Kubernetes tajný klíč je také vytvořen, který obsahuje informace o připojení a pověření. Příkaz [kubectl get][kubectl-get] můžete použít k zobrazení stavu PVC:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Použití trvalého svazku

Následující YAML vytvoří pod, který používá trvalý svazek deklarace *azurefile* k připojení sdílené složky Azure na */mnt/azure* cestu. Pro kontejnery Windows Serveru (aktuálně ve verzi preview v AKS) zadejte *mountPath* pomocí konvence cesty systému Windows, například *"D:"*.

Vytvořte soubor `azure-pvc-files.yaml`s názvem a zkopírujte jej v následujícím dokumentu YAML. Ujistěte se, že *claimName* odpovídá PVC vytvořené v posledním kroku.

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

Vytvořte pod pomocí příkazu [kubectl apply.][kubectl-apply]

```console
kubectl apply -f azure-pvc-files.yaml
```

Teď máte spuštěný pod se sdílenou spodou souborů Azure připojený v adresáři */mnt/azure.* Tato konfigurace může být viděn při `kubectl describe pod mypod`kontrole pod přes . Následující kondenzovaný příklad výstupu ukazuje objem namontovaný v kontejneru:

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

Výchozí hodnota pro *fileMode* a *dirMode* je *0777* pro Kubernetes verze 1.13.0 a vyšší. Pokud dynamicky vytváříte trvalý svazek s třídou úložiště, lze na objektu třídy úložiště zadat možnosti připojení. Následující příklad nastaví *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Další kroky

Doporučené postupy najdete v [tématu Doporučené postupy pro ukládání a zálohování v AKS][operator-best-practices-storage].

Přečtěte si další informace o trvalých svazcích Kubernetes pomocí souborů Azure.

> [!div class="nextstepaction"]
> [Kubernetes plugin pro soubory Azure][kubernetes-files]

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
