---
title: Povolit ovladače rozhraní pro kontejnerové úložiště (CSI) ve službě Azure Kubernetes (AKS)
description: Naučte se, jak povolit ovladače rozhraní pro kontejnerové úložiště (CSI) pro disky Azure a soubory Azure v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 2af4f9e2ea1dc0fcb8e5f40e0024297124292b49
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074817"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Povolení ovladačů rozhraní úložiště kontejnerů (CSI) pro disky Azure a soubory Azure ve službě Azure Kubernetes Service (AKS) (Preview)

Rozhraní pro úložiště kontejnerů (CSI) představuje standard pro vystavení libovolných systémů blokování a souborů úložiště pro zabalení úloh na Kubernetes. Díky přijetí a používání rozhraní CSI může služba Azure Kubernetes Service (AKS) zapisovat, nasazovat a iterovat moduly plug-in, které budou vystavovat nové nebo vylepšit stávající úložné systémy v Kubernetes, aniž byste museli se dotknout základního kódu Kubernetes a počkat na jeho cykly vydávání.

Podpora ovladače úložiště CSI na AKS umožňuje nativně používat:
- [*Disky Azure*](azure-disk-csi.md), které se dají použít k vytvoření prostředku Kubernetes *datadisk* . Disky můžou využívat Azure Premium Storage, které jsou založené na vysoce výkonném SSD nebo Azure Storage úrovně Standard, a to s využitím regulárních HDD nebo Standard SSD. Pro většinu produkčních a vývojových úloh použijte Premium Storage. Disky Azure jsou připojené jako *ReadWriteOnce*, takže jsou dostupné jenom pro jeden pod. Pro svazky úložiště, ke kterým se dá současně přistupovat více lusků, použijte soubory Azure.
- [*Soubory Azure*](azure-files-csi.md), které se dají použít k připojení sdílené složky SMB 3,0 s účtem Azure Storage k luskům. Pomocí služby soubory Azure můžete sdílet data napříč několika uzly a lusky. Služba soubory Azure může používat úložiště Azure Standarded s využitím pravidelných HDD nebo Azure Premium Storage zajištěné vysokým výkonem SSD.

> [!IMPORTANT]
> V Kubernetes verze 1,21 bude Kubernetes používat pouze ovladače CSI a ve výchozím nastavení. Tyto ovladače jsou budoucí podporou úložiště v Kubernetes.
>
> *Ovladače stromové struktury* odkazují na aktuální ovladače úložiště, které jsou součástí základního kódu Kubernetes, oproti novým ovladačům CSI, které jsou moduly plug-in.

## <a name="limitations"></a>Omezení

- Tato funkce se dá nastavit jenom v době vytváření clusteru.
- Minimální podverze Kubernetes, která podporuje ovladače CSI, je v 1.17.
- Ve verzi Preview bude výchozí třída úložiště stále [stejná ve třídě úložiště stromu](concepts-storage.md#storage-classes). Jakmile je tato funkce všeobecně dostupná, bude výchozí třída úložiště `managed-csi` třída úložiště a třídy úložiště ve stromové struktuře budou odebrány.
- Během první fáze Preview se podporuje jenom Azure CLI.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Registrace `EnableAzureDiskFileCSIDriver` funkce Preview

Pokud chcete vytvořit cluster AKS, který může používat ovladače CSI pro disky Azure a soubory Azure, musíte `EnableAzureDiskFileCSIDriver` u svého předplatného povolit příznak funkce.

Příznak funkce Zaregistrujte `EnableAzureDiskFileCSIDriver` pomocí příkazu [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Zobrazení stavu v *registraci*trvá několik minut. Pomocí příkazu [AZ Feature list][az-feature-list] ověřte stav registrace:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření rozhraní příkazového řádku aks-preview

Pokud chcete vytvořit cluster AKS nebo fond uzlů, který může používat ovladače úložiště CLI, budete potřebovat nejnovější rozšíření rozhraní *příkazového řádku Azure AKS-Preview* . Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] . Nebo nainstalujte jakékoli dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Vytvoření nového clusteru, který může používat ovladače úložiště CSI

Pomocí následujících příkazů rozhraní příkazového řádku vytvořte nový cluster, který může používat ovladače úložiště CLI pro disky Azure a soubory Azure. `--aks-custom-headers`K nastavení funkce použijte příznak `EnableAzureDiskFileCSIDriver` .

Vytvořte skupinu prostředků Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Vytvoření clusteru AKS s podporou pro ovladače úložiště pro rozhraní CSI:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Pokud chcete vytvořit clustery v ovladačích úložiště stromu místo ovladačů úložiště CSI, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` parametr.


Podívejte se, kolik svazků založených na discích Azure můžete připojit k tomuto uzlu spuštěním:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Další kroky

- Pokud chcete použít jednotku CSI pro disky Azure, přečtěte si téma [použití disků Azure s ovladači CSI](azure-disk-csi.md).
- Pokud chcete použít jednotku CSI pro soubory Azure, přečtěte si téma [použití souborů Azure s ovladači CSI](azure-files-csi.md).
- Další informace o osvědčených postupech pro úložiště najdete v tématu [osvědčené postupy pro úložiště a zálohování ve službě Azure Kubernetes][operator-best-practices-storage].

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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true