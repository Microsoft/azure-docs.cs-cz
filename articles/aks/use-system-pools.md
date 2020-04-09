---
title: Použití fondů systémových uzlů ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvářet a spravovat fondy systémových uzlů ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/06/2020
ms.openlocfilehash: ef5400f19f68fd2da45776d220e17777f58e46e6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986314"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Správa fondů systémových uzlů ve službě Azure Kubernetes Service (AKS)

Ve službě Azure Kubernetes Service (AKS) jsou uzly stejné konfigurace seskupeny do *fondů uzlů*. Fondy uzlů obsahují základní virtuální počítače, které spouštějí vaše aplikace. Fondy systémových uzlů a fondy uživatelských uzlů jsou dva různé režimy fondu uzlů pro clustery AKS. Fondy systémových uzlů jsou hostiteli základních systémových služeb, jako je například CoreDNS. Fondy uzlů uživatelů jsou místo, kam umístíte pody specifické pro aplikaci. Každý cluster AKS musí obsahovat alespoň jeden fond systémových uzlů s alespoň jedním uzlem. Pokud spustíte fond jednoho systémového uzlu pro cluster AKS, doporučujeme použít alespoň tři uzly pro fond uzlů. 

## <a name="before-you-begin"></a>Než začnete

* Potřebujete nainstalované a nakonfigurované azure CLI verze 2.3.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="limitations"></a>Omezení

Následující omezení platí při vytváření a správě clusterů AKS, které podporují fondy systémových uzlů.

* Viz [Kvóty, omezení velikosti virtuálního počítače a dostupnost oblasti ve službě Azure Kubernetes Service (AKS).][quotas-skus-regions]
* Cluster AKS musí být sestaven s škálovacími sadami virtuálních strojů jako typ virtuálního počítače.
* Název fondu uzlů může obsahovat pouze malá alfanumerická písmena a musí začínat s malou písmena. Pro fondy uzlů Linux délka musí být mezi 1 a 12 znaků. U fondů uzlů systému Windows musí být délka mezi 1 a 6 znaky.

## <a name="system-and-user-node-pools"></a>Fondy systémových a uživatelských uzlů

Uzly fondu systémových uzlů mají každý popisek **kubernetes.azure.com/mode: system**. Každý cluster AKS obsahuje alespoň jeden fond systémových uzlů. Fondy systémových uzlů mají následující omezení:

* Systémové fondy osType musí být Linux.
* Uživatelské uzly fondy osType může být Linux nebo Windows.
* Systémové fondy musí obsahovat alespoň jeden uzel a fondy uživatelských uzlů mohou obsahovat nula nebo více uzlů.
* Fondy systémových uzlů vyžadují skladovou položku virtuálního zařízení s alespoň 2 virtuálními procesory a 4 GB paměti.
* Fondy systémových uzlů musí podporovat alespoň 30 podů, jak je popsáno [ve vzorci minimální a maximální hodnoty pro pody][maximum-pods].
* Fondy přímých uzlů vyžadují fondy uzlů uživatelů.

S fondy uzlů můžete provést následující operace:

* Změňte fond systémových uzlů jako fond uživatelských uzlů za předpokladu, že máte jiný fond systémových uzlů, který zaujme jeho místo v clusteru AKS.
* Změňte fond uživatelských uzlů jako fond systémových uzlů.
* Odstraňte fondy uživatelských uzlů.
* Fondy systémových uzlů můžete odstranit za předpokladu, že máte jiný fond systémových uzlů, který zaujme své místo v clusteru AKS.
* Cluster AKS může mít více fondů systémových uzlů a vyžaduje alespoň jeden fond systémových uzlů.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Vytvoření nového clusteru AKS s fondem systémových uzlů

Při vytváření nového clusteru AKS automaticky vytvoříte fond systémových uzlů s jedním uzlem. Počáteční fond uzlů je ve výchozím nastavení nastaven na typový systém. Když vytvoříte nové fondy uzlů s přidáním uzlu azaks, tyto fondy uzlů jsou fondy uživatelských uzlů, pokud explicitně nezadáte parametr režimu.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *eastus.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Pomocí příkazu [az aks create][az-aks-create] vytvořte cluster AKS. Následující příklad vytvoří cluster s názvem *myAKSCluster* s jedním systémem fondu obsahující jeden uzel. Pro produkční úlohy se ujistěte, že používáte fondy systémových uzlů s alespoň třemi uzly. Tato operace může trvat několik minut.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Přidání fondu systémových uzlů do existujícího clusteru AKS

Do existujících clusterů AKS můžete přidat jeden nebo více fondů systémových uzlů. Následující příkaz přidá fond uzlů systému typu režimu s výchozím počtem tří uzlů.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Zobrazit podrobnosti pro fond uzlů

Můžete zkontrolovat podrobnosti fondu uzlů pomocí následujícího příkazu.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Režim typu **Systém** je definován pro fondy systémových uzlů a režim typu **Uživatel** je definován pro fondy uživatelských uzlů.

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

Můžete změnit režimy pro systémové i uživatelské uzlové fondy. Fond systémových uzlů můžete změnit na fond uživatelů pouze v případě, že v clusteru AKS již existuje jiný fond systémových uzlů.

Tento příkaz změní fond systémových uzlů na fond uživatelských uzlů.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Tento příkaz změní fond uživatelských uzlů na fond systémových uzlů.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Odstranění fondu systémových uzlů

> [!Note]
> Chcete-li použít fondy systémových uzlů v clusterech AKS před verzí rozhraní API 2020-03-02, přidejte nový fond systémových uzlů a odstraňte původní výchozí fond uzlů.

Dříve nebylo možné odstranit fond systémových uzlů, což byl počáteční výchozí fond uzlů v clusteru AKS. Nyní máte možnost odstranit všechny fondy uzlů z clusterů. Vzhledem k tomu, že clustery AKS vyžadují alespoň jeden fond systémových uzlů, musíte mít v clusteru AKS alespoň dva fondy systémových uzlů, než můžete jeden z nich odstranit.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak vytvořit a spravovat fondy systémových uzlů v clusteru AKS. Další informace o použití více fondů uzlů naleznete v tématu [použití více fondů uzlů][use-multiple-node-pools].

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
[maximum-pods]: faq.md#why-cant-i-set-maxpods-below-30