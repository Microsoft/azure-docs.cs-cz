---
title: Povolení podpory Ultra disk ve službě Azure Kubernetes Service (AKS)
description: Přečtěte si, jak povolit a nakonfigurovat disky Ultra v clusteru Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: c743162ed3f75386287e050443e82069e797ced9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502565"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Použití disků Azure Ultra ve službě Azure Kubernetes (Preview)

[Azure Ultra disks](../virtual-machines/disks-enable-ultra-ssd.md) nabízí vysokou propustnost, vysoké IOPS a konzistentní diskové úložiště s nízkou latencí pro stavové aplikace. Jednou z hlavních výhod Ultra disks je schopnost dynamicky měnit výkon jednotky SSD společně s vašimi úlohami, aniž by bylo nutné restartovat uzly agenta. Disky Ultra jsou vhodné pro úlohy náročné na data.

## <a name="before-you-begin"></a>Než začnete

Tato funkce se dá nastavit jenom při vytváření clusteru nebo při vytváření fondu uzlů.

> [!IMPORTANT]
> Disky Azure Ultra vyžadují nodepools nasazené v zónách dostupnosti a oblastech, které tyto disky podporují, a také pouze konkrétní řady virtuálních počítačů. Podívejte se na téma [**omezení a rozsahy Ultra discích GA**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations).

### <a name="register-the-enableultrassd-preview-feature"></a>Registrace `EnableUltraSSD` funkce Preview

Pokud chcete vytvořit cluster AKS nebo fond uzlů, který může využít Ultra disků, musíte `EnableUltraSSD` u svého předplatného povolit příznak funkce.

Zaregistrujte `EnableUltraSSD` příznak funkce pomocí příkazu [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Zobrazení stavu v *registraci* trvá několik minut. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření rozhraní příkazového řádku aks-preview

Pokud chcete vytvořit cluster AKS nebo fond uzlů, který může používat disky Ultra, budete potřebovat nejnovější rozšíření rozhraní *příkazového řádku AKS-Preview* . Pomocí příkazu [AZ Extension Add][az-extension-add] nainstalujte rozšíření Azure CLI *AKS-Preview* , nebo pomocí příkazu [AZ Extension Update][az-extension-update] nainstalujte všechny dostupné aktualizace:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Omezení
- Viz [ **omezení a obory Ultra disky GA** .](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Velikost podporovaného rozsahu pro disky Ultra je mezi 100 a 1500.

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Vytvoření nového clusteru, který může používat Ultra disks

Vytvořte cluster AKS, který dokáže využívat disky Ultra pomocí následujících příkazů CLI. `--aks-custom-headers`K nastavení funkce použijte příznak `EnableUltraSSD` .

Vytvořte skupinu prostředků Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Vytvořte cluster AKS s podporou pro disky Ultra.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Pokud chcete vytvořit clustery bez podpory Ultra disk, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` parametr.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Povolení disků Ultra v existujícím clusteru

Můžete povolit disky Ultra na existujících clusterech přidáním nového fondu uzlů do clusteru, který podporuje extrémně disky. Nakonfigurujte nový fond uzlů tak, aby používal disky Ultra pomocí `--aks-custom-headers` příznaku.

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Pokud chcete vytvořit nové fondy uzlů bez podpory pro disky Ultra, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` parametr.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Použití disků Ultra dynamicky s třídou úložiště

Pokud chcete použít Ultra disks v našich nasazeních nebo stavových sadách, můžete [pro dynamické zřizování použít třídu úložiště](azure-disks-dynamic-pv.md).

### <a name="create-the-storage-class"></a>Vytvoření třídy úložiště

Třída úložiště se používá k definování způsobu, jakým se jednotka úložiště dynamicky vytvoří s trvalým svazkem. Další informace o třídách úložiště Kubernetes naleznete v tématu [třídy úložiště Kubernetes][kubernetes-storage-classes].

V tomto případě vytvoříme třídu úložiště, která odkazuje na disky Ultra. Vytvořte soubor s názvem `azure-ultra-disk-sc.yaml` a zkopírujte ho do následujícího manifestu.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

Vytvořte třídu úložiště pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte soubor *Azure-Ultra-disk-SC. yaml* :

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Vytvoření deklarace identity trvalého svazku

Deklarace identity trvalého svazku (PVC) se používá k automatickému zřízení úložiště na základě třídy úložiště. V takovém případě může virtuální okruh použít dříve vytvořenou třídu úložiště k vytvoření Ultra disku.

Vytvořte soubor s názvem `azure-ultra-disk-pvc.yaml` a zkopírujte ho do následujícího manifestu. Deklarace identity vyžaduje disk s názvem `ultra-disk` *1000 GB* s přístupem *ReadWriteOnce* . Třída úložiště *Ultra-disk-SC* je určena jako třída úložiště.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

Pomocí příkazu [kubectl Apply][kubectl-apply] vytvořte deklaraci trvalého svazku a zadejte svůj soubor *Azure-Ultra-disk-PVC. yaml* :

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Použití trvalého svazku

Po vytvoření deklarace identity trvalého svazku a úspěšném zřízení disku je možné vytvořit pod s přístupem k disku. Následující manifest vytvoří základní NGINX pod, který používá deklaraci trvalého svazku s názvem *Ultra-disk* k připojení disku Azure v cestě `/mnt/azure` .

Vytvořte soubor s názvem `nginx-ultra.yaml` a zkopírujte ho do následujícího manifestu.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
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
        claimName: ultra-disk
```

Vytvořte pod příkazem [kubectl Apply][kubectl-apply] , jak je znázorněno v následujícím příkladu:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

Teď máte spuštěný pod diskem Azure připojeným k `/mnt/azure` adresáři. Tato konfigurace se může zobrazit při kontrole pod tím `kubectl describe pod nginx-ultra` , jak je znázorněno v následujícím zhuštěném příkladu:

```console
$ kubectl describe pod nginx-ultra

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```


## <a name="next-steps"></a>Další kroky

- Další informace o discích Ultra najdete v tématu [použití disků Azure Ultra](../virtual-machines/disks-enable-ultra-ssd.md).
- Další informace o osvědčených postupech pro úložiště najdete v tématu [osvědčené postupy pro úložiště a zálohy ve službě Azure Kubernetes Service (AKS)][operator-best-practices-storage] .

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
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
