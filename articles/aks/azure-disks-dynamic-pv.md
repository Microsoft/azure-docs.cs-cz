---
title: Azure Disk pomocí AKS
description: Disky systému Azure pomocí AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: iainfou
ms.openlocfilehash: ddac68b2a47fc830055b9dd5bd705802cc29c52f
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095922"
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

> [!NOTE]
> Trvalé svazku deklarace identity jsou určené v GiB ale spravovat Azure disky se účtují podle SKU pro konkrétní velikost. Tyto identifikátory SKU rozsahu od 32GiB S4 nebo P4 disků 4TiB S50 nebo P50 disků. Kromě toho propustnost a IOPS výkon prémiový spravované disku závisí na obou SKU a velikost instance uzly v clusteru AKS. V tématu [ceny a výkonu spravovaných disků][managed-disk-pricing-performance].

## <a name="create-persistent-volume-claim"></a>Vytvoření svazku trvalé deklarace identity

Trvalé svazku deklarace identity (PVC) se používá k automatickému zajištění úložiště založené na třídě úložiště. V takovém případě PVC můžete použít jednu z tříd předem vytvořené úložiště k vytvoření Azure standard nebo premium spravovaného disku.

Vytvořte soubor s názvem `azure-premium.yaml`a zkopírujte následující manifestu.

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
kubectl apply -f azure-premium.yaml
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
> [Modul plug-in Kubernetes pro disky systému Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
