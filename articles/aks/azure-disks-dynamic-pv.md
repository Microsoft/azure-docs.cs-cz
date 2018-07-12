---
title: Vytvářet trvalé svazky pomocí služby Azure Kubernetes Service
description: Další informace o použití disků v Azure k vytvoření trvalého svazků pro podů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/10/2018
ms.author: iainfou
ms.openlocfilehash: 14617b57f59c068aa015c9bfea9b4d18520b4152
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38473678"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Vytvářet trvalé svazky s disky Azure pro Azure Kubernetes Service (AKS)

Trvalý svazek představuje část úložiště, která byla zřízena pro použití s podů Kubernetes. Trvalý svazek může využívat jeden nebo více podů a je možné dynamicky nebo staticky zřídit. Další informace o Kubernetes trvalé svazky, naleznete v tématu [trvalé svazky Kubernetes][kubernetes-volumes]. Tento článek ukazuje, jak použít trvalé svazky s Azure disky v clusteru služby Azure Kubernetes Service (AKS).

> [!NOTE]
> Disk s Azure je možné připojit pouze s *režim přístupu* typ *ReadWriteOnce*, takže je k dispozici pouze jeden uzel AKS. Pokud museli trvalý svazek sdílet mezi více uzlů, zvažte použití [Azure Files][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Součástí třídy úložiště

Třída úložiště se používá k definování, jak se jednotka úložiště dynamicky vytvoří s trvalý svazek. Další informace o Kubernetes třídy úložiště najdete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes].

Každý cluster AKS obsahuje dvě třídy předem vytvořené úložiště, nakonfigurovány pro práci s disků v Azure. *Výchozí* třídu úložiště mu standardní disk Azure. *Spravované premium* třídu úložiště zřídí disku Azure na úrovni premium. Pokud uzlů AKS ve vašem clusteru používat premium storage, vyberte *spravované premium* třídy.

Použití [kubectl get sc] [ kubectl-get] příkazu naleznete v tématu třídy předem vytvořené úložiště. Následující příklad ukazuje předem vytvořit třídy úložiště k dispozici v rámci clusteru AKS:

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Trvalý svazek deklarace identity jsou uvedeny v GiB, ale Azure managed disks se účtují po SKU pro určité velikosti. Tyto skladové položky sahají od 32GiB disků S4 nebo P4 4TiB S50 nebo P50 disků. Kromě toho spravovat propustnost a výkon vstupně-výstupních operací na úrovni Premium disku závisí na obou SKU a velikosti instance uzlů v clusteru AKS. Další informace najdete v tématu [ceny a výkonu Managed Disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Vytvořit deklaraci identity trvalý svazek

Trvalý svazek deklarace identity (PVC) se používá k automatickému zřízení úložiště založené na třídě úložiště. V takovém případě PVC můžete použít jednu z tříd předem vytvořené úložiště k vytvoření Azure úrovně standard nebo premium spravovaného disku.

Vytvořte soubor s názvem `azure-premium.yaml`a zkopírujte do následující manifestu.

Všimněte si, že *spravované premium* třídu úložiště je zadaná v poznámce a deklarace identity vyžaduje disk *5GB* velikosti s *ReadWriteOnce* přístup.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Vytvořit deklaraci trvalý svazek s [použití kubectl] [ kubectl-apply] příkaz a zadejte vaše *azure premium.yaml* souboru:

```console
kubectl apply -f azure-premium.yaml
```

## <a name="use-the-persistent-volume"></a>Použít trvalý svazek

Po vytvoření deklarace identity trvalý svazek a disku úspěšně zřízený, pod je možné vytvořit s přístupem k disku. Následující manifest vytvoří pod, která používá deklarace identity trvalý svazek *azure managed Disks* připojit disk v Azure `/mnt/azure` cestu.

Vytvořte soubor s názvem `azure-pvc-disk.yaml`a zkopírujte do následující manifestu.

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

Vytvořte pod s [použití kubectl] [ kubectl-apply] příkazu.

```console
kubectl apply -f azure-pvc-disk.yaml
```

Teď máte spuštěné pod s Azure disku připojené `/mnt/azure` adresáře. Tato konfigurace se zobrazí při kontrole podu prostřednictvím `kubectl describe pod mypod`.

## <a name="next-steps"></a>Další postup

Další informace o Kubernetes trvalé svazky s využitím disků v Azure.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro disky Azure][azure-disk-volume]

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
