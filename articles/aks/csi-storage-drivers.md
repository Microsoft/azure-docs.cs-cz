---
title: Povolit ovladače rozhraní pro kontejnerové úložiště (CSI) ve službě Azure Kubernetes (AKS)
description: Naučte se, jak povolit ovladače rozhraní pro kontejnerové úložiště (CSI) pro soubory Azure disk a Azure v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: bd5706d20496e1ff00843f761443d183cf7fcae3
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422025"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Povolení ovladačů rozhraní úložiště kontejnerů (CSI) pro disky Azure a soubory Azure ve službě Azure Kubernetes Service (AKS) (Preview)

Rozhraní pro úložiště kontejnerů (CSI) představuje standard pro vystavení libovolných systémů blokování a souborů úložiště pro zabalení úloh na Kubernetes. Díky přijetí a používání rozhraní CSI může služba Azure Kubernetes Service (AKS) zapisovat, nasazovat a iterovat moduly plug-in, které vystavují nové nebo zlepšují stávající systémy úložiště v Kubernetes, aniž by museli se dotknout základního kódu Kubernetes a čekat na jeho cykly vydávání.

Podpora ovladače úložiště CSI v AKS umožňuje nativně využívat:
- [*Disky Azure*](azure-disk-csi.md) – lze použít k vytvoření prostředku Kubernetes *datadisk* . Disky můžou využívat službu Azure Premium Storage, která je zajištěná vysokým výkonem SSD nebo Azure Storage úrovně Standard, zajištěná běžným HDD nebo standardem SSD. Pro většinu produkčních a vývojových úloh použijte Premium Storage. Disky Azure jsou připojené jako *ReadWriteOnce*, takže jsou dostupné jenom pro jeden pod. Pro svazky úložiště, ke kterým se dá současně přistupovat více lusků, použijte soubory Azure.
- [*Soubory Azure*](azure-files-csi.md) můžete použít k připojení sdílené složky SMB 3,0 s účtem Azure Storage do lusků. Soubory umožňují sdílet data napříč více uzly a lusky. Soubory můžou používat úložiště Azure Standarded založené na běžných HDD nebo Azure Premium Storage založené na vysoce výkonném SSD.

> [!IMPORTANT]
> V Kubernetes verze 1,21 bude Kubernetes používat pouze ovladače CSI a ve výchozím nastavení. Toto jsou budoucí podpora úložiště v Kubernetes.
>
> *Ovladače v rámci stromu* odkazují na aktuální ovladače úložiště, které jsou součástí základního Kubernetes kódu, a nové ovladače CSI, které jsou moduly plug-in.

## <a name="limitations"></a>Omezení

- Tato funkce se dá nastavit jenom v době vytváření clusteru.
- Minimální podverze Kubernetes, která podporuje ovladače CSI, je v 1.17.
- Ve verzi Preview bude výchozí třída úložiště stále [stejná ve třídě úložiště stromu](concepts-storage.md#storage-classes). Jakmile je tato funkce všeobecně dostupná, bude výchozí třída úložiště `managed-csi` třída úložiště a třídy úložiště ve stromové struktuře budou odebrány.
- Během první fáze Preview se podporuje jenom Azure CLI.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Registrace `EnableAzureDiskFileCSIDriver` funkce Preview

Pokud chcete vytvořit cluster AKS, který může využívat ovladače CSI pro disky Azure a soubory Azure, musíte `EnableAzureDiskFileCSIDriver` u svého předplatného povolit příznak funkce.

Zaregistrujte `EnableAzureDiskFileCSIDriver` příznak funkce pomocí příkazu [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

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

Pokud chcete vytvořit cluster AKS nebo fond uzlů, který může používat ovladače úložiště CLI, budete potřebovat nejnovější rozšíření rozhraní *příkazového řádku AKS-Preview* . Pomocí příkazu [AZ Extension Add][az-extension-add] nainstalujte rozšíření Azure CLI *AKS-Preview* , nebo pomocí příkazu [AZ Extension Update][az-extension-update] nainstalujte všechny dostupné aktualizace:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Vytvoření nového clusteru, který může používat ovladače úložiště CSI

Vytvořte nový cluster, který může využít ovladače úložiště CLI pro disky Azure a soubory Azure pomocí následujících příkazů rozhraní příkazového řádku. `--aks-custom-headers`K nastavení funkce použijte příznak `EnableAzureDiskFileCSIDriver` .

Vytvořte skupinu prostředků Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Vytvořte cluster AKS s podporou pro ovladače úložiště CSI.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Pokud chcete místo ovladačů úložiště CSI vytvořit clustery v rámci stromových ovladačů úložiště, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` parametr.


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

- Použití jednotky CSI pro disky Azure najdete v tématu [použití disku Azure s ovladači CSI](azure-disk-csi.md).
- Použití jednotky CSI pro soubory Azure najdete v tématu [použití souborů Azure s ovladači CSI](azure-files-csi.md).
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
[premium-storage]: ../virtual-machines/windows/disks-types.md
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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register