---
title: Integrace Azure NetApp Files se službou Azure Kubernetes
description: Naučte se integrovat Azure NetApp Files se službou Azure Kubernetes.
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 28c5b77f06bc48bf06575e45194adfaed068b30f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776046"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrace Azure NetApp Files se službou Azure Kubernetes

[Azure NetApp Files][anf] je vysoce výkonná služba úložiště monitorovaných souborů na podnikové úrovni, která běží v Azure. V tomto článku se dozvíte, jak integrovat Azure NetApp Files se službou Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Než začnete
V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Cluster AKS musí být také [v oblasti, která podporuje Azure NetApp Files][anf-regions].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

### <a name="limitations"></a>Omezení

Při použití Azure NetApp Files platí následující omezení:

* Azure NetApp Files je k dispozici pouze [ve vybraných oblastech Azure][anf-regions].
* Předtím, než budete moci použít Azure NetApp Files, je nutné udělit přístup ke službě Azure NetApp Files. Pokud chcete požádat o přístup, můžete použít [formulář pro odeslání Azure NetApp Files pořadníku][anf-waitlist] nebo přejít na https://azure.microsoft.com/services/netapp/#getting-started . Ke službě Azure NetApp Files nemůžete získat přístup, dokud nedostanete oficiální potvrzovací e-mail od Azure NetApp Files týmu.
* Po počátečním nasazení clusteru AKS se podporuje pouze statické zřizování pro Azure NetApp Files.
* Pokud chcete používat dynamické zřizování s Azure NetApp Files, nainstalujte a nakonfigurujte [NetApp Trident](https://netapp-trident.readthedocs.io/) verze 19,07 nebo novější.

## <a name="configure-azure-netapp-files"></a>Konfigurace Azure NetApp Files

> [!IMPORTANT]
> Než budete moct zaregistrovat poskytovatele prostředků  *Microsoft. NetApp* , musíte dokončit [formulář pro odeslání služby Azure NetApp Files pořadníku][anf-waitlist] , nebo přejít na https://azure.microsoft.com/services/netapp/#getting-started pro vaše předplatné. Prostředek nejde zaregistrovat, dokud nedostanete oficiální potvrzovací e-mail od Azure NetApp Files týmu.

Zaregistrujte poskytovatele prostředků *Microsoft. NetApp* :

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Dokončení tohoto může nějakou dobu trvat.

Když vytvoříte účet Azure NetApp pro použití s AKS, musíte vytvořit účet ve skupině prostředků **uzlu** . Nejprve Získejte název skupiny prostředků pomocí příkazu [AZ AKS show][az-aks-show] a přidejte `--query nodeResourceGroup` parametr dotazu. Následující příklad načte skupinu prostředků uzlu pro cluster AKS s názvem *myAKSCluster* v názvu skupiny prostředků *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Vytvořte účet Azure NetApp Files v rámci skupiny prostředků **uzlu** a stejnou oblast jako cluster AKS pomocí [AZ netappfiles Account Create][az-netappfiles-account-create]. Následující příklad vytvoří účet s názvem *myaccount1* ve skupině prostředků *MC_myResourceGroup_myAKSCluster_eastus* a v oblasti *eastus* :

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Vytvořte nový fond kapacit pomocí funkce [AZ netappfiles Pool Create][az-netappfiles-pool-create]. Následující příklad vytvoří nový fond kapacit s názvem *mypool1* s velikostí 4 TB na úrovni služby *Premium* :

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Vytvořte podsíť pro [delegování pro Azure NetApp Files][anf-delegate-subnet] pomocí [AZ Network VNet Subnet Create][az-network-vnet-subnet-create]. *Tato podsíť musí být ve stejné virtuální síti jako cluster AKS.*

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

Pomocí [AZ netappfiles Volume Create][az-netappfiles-volume-create]vytvořte svazek.

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
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

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
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Vytvoření PersistentVolume

Seznam podrobností o svazku pomocí [AZ netappfiles Volume show][az-netappfiles-volume-show]

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

Vytvoří `pv-nfs.yaml` definici PersistentVolume. Nahraďte `path` *creationToken* a parametrem `server` *ipAddress* z předchozího příkazu. Například:

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
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Aktualizujte *Server* a *cestu* na hodnoty svazku NFS (Network File System), který jste vytvořili v předchozím kroku. Vytvořte PersistentVolume pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pv-nfs.yaml
```

Ověřte, že je *stav* PersistentVolume *k dispozici* pomocí příkazu [kubectl popis][kubectl-describe] :

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Vytvoření PersistentVolumeClaim

Vytvoří `pvc-nfs.yaml` definici PersistentVolume. Například:

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

Vytvořte PersistentVolumeClaim pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pvc-nfs.yaml
```

Ověřte, že *stav* PersistentVolumeClaim je *vázán* pomocí příkazu [kubectl popis][kubectl-describe] :

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Připojit pomocí pod

Vytvořte `nginx-nfs.yaml` definici pod, která používá PersistentVolumeClaim. Například:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

Vytvořte pod příkazem [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f nginx-nfs.yaml
```

Ověřte, že je pod *spuštěným* příkazem [kubectl popis][kubectl-describe] :

```console
kubectl describe pod nginx-nfs
```

Ověřte, že je svazek připojený k rozhraní pod, pomocí [kubectl exec][kubectl-exec] a `df -h` Ověřte, jestli je svazek připojený.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Další kroky

Další informace o Azure NetApp Files najdete v tématu [co je Azure NetApp Files][anf]. Další informace o použití systému souborů NFS s AKS najdete v tématu [Ruční vytvoření a použití svazku systému souborů NFS (Network File System) pro Linux serveru se službou Azure Kubernetes Service (AKS)][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
