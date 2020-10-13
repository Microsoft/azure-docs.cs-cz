---
title: Použití fondů systémových uzlů ve službě Azure Kubernetes Service (AKS)
description: Naučte se vytvářet a spravovat fondy systémových uzlů ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit
ms.openlocfilehash: 2cb6ed265d3e94c2c162381dfb80ba0c5427a71f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888957"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Správa fondů systémových uzlů ve službě Azure Kubernetes Service (AKS)

Ve službě Azure Kubernetes Service (AKS) jsou uzly stejné konfigurace seskupeny dohromady do *fondů uzlů*. Fondy uzlů obsahují základní virtuální počítače, na kterých běží vaše aplikace. Fondy systémových uzlů a fondy uživatelských uzlů jsou dva různé režimy fondu uzlů pro clustery AKS. Fondy systémových uzlů slouží jako primární účel hostování důležitých systémových lusků, jako jsou `CoreDNS` a `metrics-server` . Fondy uživatelských uzlů slouží jako primární účel hostování aplikace. V případě, že chcete mít v clusteru AKS jenom jeden fond, je ale možné naplánovat použití lusků na uzlech systému. Každý cluster AKS musí obsahovat alespoň jeden fond uzlů systému s alespoň jedním uzlem.

> [!Important]
> Pokud pro cluster AKS spustíte jeden fond uzlů systému v produkčním prostředí, doporučujeme pro fond uzlů použít aspoň tři uzly.

## <a name="before-you-begin"></a>Než začnete

* Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.3.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které podporují fondy systémových uzlů, platí následující omezení.

* Podívejte se [na téma kvóty, omezení velikosti virtuálních počítačů a dostupnost oblasti ve službě Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Cluster AKS musí být sestaven s virtuálním počítačem Scale Sets jako typ virtuálního počítače a *standardní* SKU pro vyrovnávání zatížení.
* Název fondu uzlů může obsahovat jenom malé alfanumerické znaky a musí začínat malým písmenem. U fondů uzlů se systémem Linux musí být délka v rozmezí od 1 do 12 znaků. U fondů uzlů Windows musí mít délka 1 až 6 znaků.
* Pro nastavení režimu fondu uzlů musí být použita verze rozhraní API 2020-03-01 nebo vyšší. Clustery vytvořené ve verzích rozhraní API starších než 2020-03-01 obsahují pouze fondy uživatelských uzlů, ale mohou být migrovány do skupiny systémových uzlů pomocí následujících [kroků aktualizace režimu fondu](#update-existing-cluster-system-and-user-node-pools).
* Režim fondu uzlů je povinná vlastnost, která musí být explicitně nastavena při použití šablon ARM nebo přímých volání rozhraní API.

## <a name="system-and-user-node-pools"></a>Fondy systémových a uživatelských uzlů

Pro fond uzlů systému AKS automaticky přiřadí jmenovku **Kubernetes.Azure.com/Mode: systém** ke svým uzlům. To způsobí, že AKS upřednostňuje plánování systémových lusků u fondů uzlů, které obsahují tento popisek. Tento popisek vám nebrání v plánování aplikačních lusků ve fondech uzlů systému. Doporučujeme však izolovat kritické systémové lusky od vaší aplikace, aby nedocházelo k chybám konfigurovaným nebo neautorizovaným aplikacím v omylu po nechtěném usmrcování systémových lusků. Toto chování můžete vyhovět vytvořením vyhrazeného fondu uzlů systému. Použijte `CriticalAddonsOnly=true:NoSchedule` k tomu, aby nedocházelo k naplánování aplikací lusků ve fondech uzlů systému, pomocí chuti.

Fondy systémových uzlů mají následující omezení:

* Systémové fondy osType musí být Linux.
* Fondy uživatelských uzlů osType můžou být Linux nebo Windows.
* Systémové fondy musí obsahovat alespoň jeden uzel a fondy uživatelských uzlů můžou obsahovat nula nebo víc uzlů.
* Fondy systémových uzlů vyžadují SKU virtuálního počítače minimálně 2 vCPU a 4 GB paměti.
* Fondy systémových uzlů musí podporovat aspoň 30 lusků, jak je popsáno v části [vzorec minimální a maximální hodnoty pro lusky][maximum-pods].
* Fondy uzlů bodu musí vyžadovat fondy uživatelských uzlů.
* Přidání dalšího fondu uzlů systému nebo změna fondu uzlů, který je fondem uzlů systému, nebude *automaticky přesouvat* systémové lusky. Systémové lusky mohou běžet na stejném fondu uzlů i v případě, že je změníte na fond uživatelských uzlů. Pokud odstraníte nebo zmenšíte rozsah uzlů, na kterých je spuštěný systém lusky, který byl dřív ve fondu uzlů systému, znovu nasadí tyto systémové lusky s preferovaným plánováním do nového fondu uzlů systému.

S fondy uzlů můžete provádět následující operace:

* Vytvoření vyhrazeného fondu uzlů systému (preferovat plánování systémových lusků do fondů uzlů `mode:system` )
* Změňte fond uzlů systému tak, aby byl fondem uživatelských uzlů za předpokladu, že máte jiný fond uzlů systému, aby bylo možné provést místo v clusteru AKS.
* Změňte fond uzlů uživatele tak, aby byl fondem uzlů systému.
* Odstraňte fondy uživatelských uzlů.
* Můžete odstranit fondy systémových uzlů, pokud máte jiný fond uzlů systému, aby bylo možné provést místo v clusteru AKS.
* Cluster AKS může mít několik fondů systémových uzlů a vyžaduje alespoň jeden fond uzlů systému.
* Pokud chcete změnit různá neproměnlivá nastavení u existujících fondů uzlů, můžete vytvořit nové fondy uzlů, které je nahradí. Jedním z příkladů je přidat nový fond uzlů s novým nastavením maxPods a odstranit starý fond uzlů.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Vytvoření nového clusteru AKS s fondem uzlů systému

Když vytváříte nový cluster AKS, automaticky se vytvoří fond systémových uzlů s jedním uzlem. Výchozí fond uzlů je výchozím režimem typu System. Když vytváříte nové fondy uzlů s `az aks nodepool add` , tyto fondy uzlů jsou fondy uzlů uživatele, pokud explicitně neurčíte parametr Mode.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *eastus* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Pomocí příkazu [az aks create][az-aks-create] vytvořte cluster AKS. Následující příklad vytvoří cluster s názvem *myAKSCluster* s jedním vyhrazeným fondem systému, který obsahuje jeden uzel. V případě produkčních úloh se ujistěte, že používáte fondy systémových uzlů s alespoň třemi uzly. Dokončení této operace může trvat několik minut.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Přidání fondu vyhrazených systémových uzlů do stávajícího clusteru AKS

> [!Important]
> Po vytvoření fondu uzlů nemůžete měnit příchuti uzlů prostřednictvím rozhraní příkazového řádku.

Do existujících clusterů AKS můžete přidat jeden nebo více fondů systémových uzlů. Doporučuje se naplánovat své aplikace na fondy uživatelských uzlů a vyhradit fondy systémových uzlů jenom k důležitým systémovým luskům. To brání neúmyslným aplikacím v neúmyslném usmrcování systémových lusků. Vyjistěte toto chování pomocí `CriticalAddonsOnly=true:NoSchedule` [chuti][aks-taints] pro fondy systémových uzlů. 

Následující příkaz přidá fond vyhrazených uzlů systému typů režimu s výchozím počtem tří uzlů.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>Zobrazit podrobnosti o fondu uzlů

Podrobnosti o fondu uzlů můžete zjistit pomocí následujícího příkazu.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

Režim **systému** typů je definován pro fondy systémových uzlů a režim typu **uživatel** je definován pro fondy uživatelských uzlů. V případě fondu systému ověřte, zda je nastavení vlastnosti chuti nastaveno na hodnotu `CriticalAddonsOnly=true:NoSchedule` , což zabrání v tom, aby se aplikace v tomto fondu uzlů naplánovala.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Aktualizace existujícího systému clusterů a fondů uživatelských uzlů

> [!NOTE]
> Pro nastavení režimu fondu systémových uzlů musí být použita verze rozhraní API 2020-03-01 nebo vyšší. Clustery vytvořené ve verzích rozhraní API starších než 2020-03-01 obsahují v důsledku toho pouze fondy uživatelských uzlů. Chcete-li získat funkce fondu uzlů systému a výhody ve starších clusterech, aktualizujte režim stávajících fondů uzlů pomocí následujících příkazů v nejnovější verzi Azure CLI.

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

Než budete moct odstranit jeden z nich, musíte mít ve svém clusteru AKS aspoň dva fondy systémových uzlů.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete cluster odstranit, odstraňte skupinu prostředků AKS pomocí příkazu [AZ Group Delete][az-group-delete] :

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
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
[aks-taints]: use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
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
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
