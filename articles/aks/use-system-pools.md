---
title: Použití fondů systémových uzlů ve službě Azure Kubernetes Service (AKS)
description: Naučte se vytvářet a spravovat fondy systémových uzlů ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 04/28/2020
ms.openlocfilehash: 04322bdaa2e0e72c5fbdbadb07f2608ee360e1e3
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790554"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Správa fondů systémových uzlů ve službě Azure Kubernetes Service (AKS)

Ve službě Azure Kubernetes Service (AKS) jsou uzly stejné konfigurace seskupeny dohromady do *fondů uzlů*. Fondy uzlů obsahují základní virtuální počítače, na kterých běží vaše aplikace. Fondy systémových uzlů a fondy uživatelských uzlů jsou dva různé režimy fondu uzlů pro clustery AKS. Fondy systémových uzlů slouží jako primární účel hostování důležitých systémových lusků, jako jsou například CoreDNS a tunnelfront. Fondy uživatelských uzlů slouží jako primární účel hostování aplikace. V případě, že chcete mít v clusteru AKS jenom jeden fond, je ale možné naplánovat použití lusků na uzlech systému. Každý cluster AKS musí obsahovat alespoň jeden fond uzlů systému s alespoň jedním uzlem. 

> [!Important]
> Pokud pro cluster AKS spustíte jeden fond uzlů systému v produkčním prostředí, doporučujeme pro fond uzlů použít aspoň tři uzly.

## <a name="before-you-begin"></a>Před zahájením

* Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.3.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které podporují fondy systémových uzlů, platí následující omezení.

* Podívejte se [na téma kvóty, omezení velikosti virtuálních počítačů a dostupnost oblasti ve službě Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Cluster AKS musí být sestavený s virtuálním počítačem Scale Sets jako typ virtuálního počítače.
* Název fondu uzlů může obsahovat jenom malé alfanumerické znaky a musí začínat malým písmenem. U fondů uzlů se systémem Linux musí být délka v rozmezí od 1 do 12 znaků. U fondů uzlů Windows musí mít délka 1 až 6 znaků.
* Pro nastavení režimu fondu uzlů musí být použita verze rozhraní API 2020-03-01 nebo vyšší.
* Režim fondu uzlů je povinná vlastnost, která musí být explicitně nastavena při použití šablon ARM nebo přímých volání rozhraní API.

## <a name="system-and-user-node-pools"></a>Fondy systémových a uživatelských uzlů

Uzly fondu systémových uzlů mají jmenovku **Kubernetes.Azure.com/Mode: System**. Každý cluster AKS obsahuje alespoň jeden fond uzlů systému. Fondy systémových uzlů mají následující omezení:

* Systémové fondy osType musí být Linux.
* Fondy uživatelských uzlů osType můžou být Linux nebo Windows.
* Systémové fondy musí obsahovat alespoň jeden uzel a fondy uživatelských uzlů můžou obsahovat nula nebo víc uzlů.
* Fondy systémových uzlů vyžadují SKU virtuálního počítače minimálně 2 vCPU a 4 GB paměti.
* Fondy systémových uzlů musí podporovat aspoň 30 lusků, jak je popsáno v části [vzorec minimální a maximální hodnoty pro lusky][maximum-pods].
* Fondy uzlů bodu musí vyžadovat fondy uživatelských uzlů.

S fondy uzlů můžete provádět následující operace:

* Změňte fond uzlů systému tak, aby byl fondem uživatelských uzlů za předpokladu, že máte jiný fond uzlů systému, aby bylo možné provést místo v clusteru AKS.
* Změňte fond uzlů uživatele tak, aby byl fondem uzlů systému.
* Odstraňte fondy uživatelských uzlů.
* Můžete odstranit fondy systémových uzlů, pokud máte jiný fond uzlů systému, aby bylo možné provést místo v clusteru AKS.
* Cluster AKS může mít několik fondů systémových uzlů a vyžaduje alespoň jeden fond uzlů systému.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Vytvoření nového clusteru AKS s fondem uzlů systému

Když vytváříte nový cluster AKS, automaticky se vytvoří fond systémových uzlů s jedním uzlem. Výchozí fond uzlů je výchozím režimem typu System. Když vytvoříte nové fondy uzlů pomocí AZ AKS nodepool Add, budou tyto fondy uzlů fondy uživatelských uzlů, pokud explicitně neurčíte parametr Mode.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *eastus* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Pomocí příkazu [az aks create][az-aks-create] vytvořte cluster AKS. Následující příklad vytvoří cluster s názvem *myAKSCluster* s jedním systémovým fondem, který obsahuje jeden uzel. V případě produkčních úloh se ujistěte, že používáte fondy systémových uzlů s alespoň třemi uzly. Dokončení této operace může trvat několik minut.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Přidání fondu uzlů systému do existujícího clusteru AKS

Do existujících clusterů AKS můžete přidat jeden nebo více fondů systémových uzlů. Následující příkaz přidá fond uzlů typu režim s výchozím počtem tří uzlů.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Zobrazit podrobnosti o fondu uzlů

Podrobnosti o fondu uzlů můžete zjistit pomocí následujícího příkazu.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Režim **systému** typů je definován pro fondy systémových uzlů a režim typu **uživatel** je definován pro fondy uživatelských uzlů.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>Aktualizace fondů systémových a uživatelských uzlů

Můžete změnit režimy fondů systémových i uživatelských uzlů. Fond uzlů systému můžete změnit na fond uživatelů pouze v případě, že v clusteru AKS již existuje jiný fond uzlů systému.

Tento příkaz změní fond uzlů systému na fond uzlů uživatele.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Tento příkaz změní fond uzlů uživatele na fond uzlů systému.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Odstranění fondu uzlů systému

> [!Note]
> Pokud chcete použít fondy systémových uzlů v clusterech AKS před rozhraním API verze 2020-03-02, přidejte nový fond uzlů systému a pak odstraňte původní výchozí fond uzlů.

Dřív jste nedokázali odstranit fond uzlů systému, který byl počátečním výchozím fondem uzlů v clusteru AKS. Teď máte flexibilitu při odstraňování všech fondů uzlů z vašich clusterů. Vzhledem k tomu, že clustery AKS vyžadují aspoň jeden fond uzlů systému, musíte mít ve svém clusteru AKS aspoň dva fondy systémových uzlů, než budete moct odstranit jeden z nich.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvářet a spravovat fondy systémových uzlů v clusteru AKS. Další informace o použití více fondů uzlů najdete v tématu [použití více fondů uzlů][use-multiple-node-pools].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node