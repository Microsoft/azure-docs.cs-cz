---
title: Integrace souborů Azure NetApp se službou Azure Kubernetes
description: Přečtěte si, jak integrovat soubory Azure NetApp se službou Azure Kubernetes Service
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273755"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrace souborů Azure NetApp se službou Azure Kubernetes

[Azure NetApp Files][anf] je služba podnikového vysoce výkonného úložiště souborů s měřením dat spuštěná v Azure. Tento článek ukazuje, jak integrovat soubory Azure NetApp se službou Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Než začnete
Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Váš cluster AKS musí být také [v oblasti, která podporuje soubory Azure NetApp][anf-regions].

Potřebujete také nainstalované a nakonfigurované verze Azure CLI verze 2.0.59 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

### <a name="limitations"></a>Omezení

Při použití souborů Azure NetApp platí následující omezení:

* Soubory Azure NetApp jsou dostupné [jenom ve vybraných oblastech Azure][anf-regions].
* Než budete moci používat soubory Azure NetApp, musíte získat přístup ke službě Azure NetApp Files. Chcete-li požádat o přístup, můžete použít [formulář pro odeslání seznamu čekácích na soubory Azure NetApp][anf-waitlist]. Ke službě Azure NetApp Files nemáte přístup, dokud neobdržíte oficiální potvrzovací e-mail od týmu Azure NetApp Files.
* Vaše služba Azure NetApp Files musí být vytvořena ve stejné virtuální síti jako váš cluster AKS.
* Po počátečním nasazení clusteru AKS je podporováno pouze statické zřizování pro soubory Azure NetApp.
* Pokud chcete používat dynamické zřizování se soubory Azure NetApp, nainstalujte a nakonfigurujte [NetApp Trident](https://netapp-trident.readthedocs.io/) verze 19.07 nebo novější.

## <a name="configure-azure-netapp-files"></a>Konfigurace souborů Azure NetApp

> [!IMPORTANT]
> Než budete moci zaregistrovat poskytovatele prostředků *Microsoft.NetApp,* musíte vyplnit [formulář pro odeslání seznamu souborů Azure NetApp][anf-waitlist] pro vaše předplatné. Nemůžete zaregistrovat prostředek poskytnout, dokud neobdržíte oficiální potvrzovací e-mail z týmu Azure NetApp Soubory.

Zaregistrujte poskytovatele prostředků *Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> To může nějakou dobu trvat.

Když vytvoříte účet Azure NetApp pro použití s AKS, musíte vytvořit účet ve skupině prostředků **uzlu.** Nejprve získejte název skupiny prostředků pomocí příkazu [az aks show][az-aks-show] a přidejte parametr dotazu. `--query nodeResourceGroup` Následující příklad získá skupinu prostředků uzlu pro cluster AKS s názvem *myAKSCluster* v názvu skupiny prostředků *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Vytvořte účet Azure NetApp Files ve skupině prostředků **uzlu** a ve stejné oblasti jako cluster AKS pomocí [vytvoření účtu az netappfiles][az-netappfiles-account-create]. Následující příklad vytvoří účet s názvem *myaccount1* ve skupině *prostředků MC_myResourceGroup_myAKSCluster_eastus* a *oblasti eastus:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Vytvořte nový fond kapacit pomocí [az netappfiles fondu vytvořit][az-netappfiles-pool-create]. Následující příklad vytvoří nový fond kapacit s názvem *mypool1* s velikostí 4 TB a úrovní služeb *Premium:*

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Vytvořte podsíť, která [bude delegována na soubory Azure NetApp][anf-delegate-subnet] pomocí [vytvoření podsítě sítě AZ][az-network-vnet-subnet-create]. *Tato podsíť musí být ve stejné virtuální síti jako cluster AKS.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Vytvořte svazek pomocí [az netappfiles vytvoření svazku][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Vytvořit trvalý svazek

Seznam podrobností o svazku pomocí [az netappfiles volume show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Vytvořte `pv-nfs.yaml` definování PersistentVolume. Nahraďte `path` *creationToken* a `server` *ipAddress* z předchozího příkazu. Například:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Aktualizujte *server* a *cestu* k hodnotám svazku systému souborů NFS (Network File System), který jste vytvořili v předchozím kroku. Vytvořte příkaz PersistentVolume pomocí příkazu [kubectl apply:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Ověřte, zda je *stav* trvalého svazku *k dispozici* pomocí příkazu [kubectl describe:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Vytvořit deklaraci PersistentVolumeClaim

Vytvořte `pvc-nfs.yaml` definování PersistentVolume. Například:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Vytvořte příkaz PersistentVolumeClaim pomocí příkazu [kubectl apply:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

Ověřte, zda je *stav* deklarace persistentVolumeClaim *vázán* pomocí příkazu [kubectl describe:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Montáž s luskem

Vytvořte `nginx-nfs.yaml` definující pod, který používá PersistentVolumeClaim. Například:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Vytvořit pod s [příkazem kubectl apply:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Ověřte, zda je pod *spuštěn* pomocí příkazu [popsat kubectl:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Ověřte, zda byl svazek připojen do modulu pomocí [kubectl exec][kubectl-exec] pro připojení k podu a pak `df -h` zkontrolujte, zda je svazek připojen.

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Další kroky

Další informace o souborech Azure NetApp najdete v tématu [Co je soubory Azure NetApp][anf]. Další informace o používání systému souborů NFS se službou AKS naleznete [v tématu Ruční vytvoření a použití svazku serveru Linux serveru systému Souborů NFS (Network File System) se službou Azure Kubernetes Service (AKS).][aks-nfs]


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
