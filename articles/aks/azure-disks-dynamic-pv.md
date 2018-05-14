---
title: Azure Disk pomocí AKS
description: Disky systému Azure pomocí AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 858961db439b28a71d3475d2608073287e02f2fd
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Trvalé svazky s disky systému Azure

Trvalé svazku představuje část úložiště, které se zřizují pro použití s pracovními stanicemi soustředěnými kolem Kubernetes. Trvalé svazku může používat jednoho nebo mnoha pracovními stanicemi soustředěnými kolem a můžete dynamicky nebo staticky zřídit. Další informace o Kubernetes trvalé svazky, najdete v části [trvalé svazky Kubernetes][kubernetes-volumes].

Tento dokument údaje použití trvalé svazků s Azure disky v clusteru služby Azure Kubernetes služby (AKS).

> [!NOTE]
> Azure disk může být připojen pouze s typem režim přístupu ReadWriteOnce, že bude k dispozici pouze do jednoho uzlu AKS. Pokud museli sdílet mezi několika uzly trvalé svazku, zvažte použití [Azure Files][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Součástí třídy úložiště

Třídy úložiště se používá k definování, jak se dynamicky vytvoří jednotku úložiště s trvalé svazku. Další informace o Kubernetes třídy úložiště najdete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes].

Každý cluster AKS zahrnuje dvě třídy předem vytvořené úložiště, jak nakonfigurováno pro práci s disky systému Azure. `default` Třídy úložiště zřídí standardní Azure disk. `managed-premium` Třídy úložiště zřídí premium Azure disku. Pokud AKS uzlů v clusteru používat úložiště úrovně premium, vyberte `managed-premium` třídy.

Použití [kubectl získat sc] [ kubectl-get] příkaz, který najdete v tématu třídy předem vytvořené úložiště.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>Vytvoření svazku trvalé deklarace identity

Trvalé svazku deklarace identity (PVC) se používá k automatickému zajištění úložiště založené na třídě úložiště. V takovém případě PVC můžete použít jednu z tříd předem vytvořené úložiště k vytvoření Azure standard nebo premium spravovaného disku.

Vytvořte soubor s názvem `azure-premimum.yaml`a zkopírujte následující manifestu.

Všimněte si, která `managed-premium` třídy úložiště je uveden v anotace a deklarace identity požaduje disk `5GB` velikost `ReadWriteOnce` přístup.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Vytvoření svazku trvalé deklarace s [kubectl použít] [ kubectl-apply] příkaz.

```azurecli-interactive
kubectl apply -f azure-premimum.yaml
```

## <a name="using-the-persistent-volume"></a>Použití trvalé svazku

Jakmile vytvořila deklaraci identity trvalé svazku a disku úspěšném zřízení, pod lze vytvořit s přístupem na disk. Následující manifest vytvoří pod, která používá deklarace trvalé svazku `azure-managed-disk` připojit disk v Azure `/mnt/azure` cesta.

Vytvořte soubor s názvem `azure-pvc-disk.yaml`a zkopírujte následující manifestu.

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
        claimName: azure-managed-disk
```

Vytvoření pod s [kubectl použít] [ kubectl-apply] příkaz.

```azurecli-interactive
kubectl apply -f azure-pvc-disk.yaml
```

Nyní máte spuštěný pod s Azure diskem připojené `/mnt/azure` adresáře. Tato konfigurace si můžete prohlédnout při kontrole vaší pod prostřednictvím `kubectl describe pod mypod`.

## <a name="next-steps"></a>Další postup

Další informace o Kubernetes trvalé svazky s využitím disky systému Azure.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro disky systému Azure][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md