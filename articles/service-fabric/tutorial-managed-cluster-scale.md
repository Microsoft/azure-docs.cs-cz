---
title: Horizontální navýšení kapacity Service Fabric spravovaného clusteru (Preview)
description: V tomto kurzu se naučíte škálovat typ uzlu Service Fabric spravovaného clusteru.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: a6777743932f3cc73db973c4d9935d4b5827700b
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410434"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>Kurz: horizontální navýšení kapacity Service Fabric spravovaného clusteru (Preview)

V této sérii kurzů si probereme následující:

> [!div class="checklist"]
> * [Jak nasadit spravovaný Cluster Service Fabric.](tutorial-managed-cluster-deploy.md)
> * Postup horizontálního navýšení kapacity Service Fabric spravovaného clusteru
> * [Postup přidání a odebrání typů uzlů ve spravovaném clusteru s Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * [Postup nasazení aplikace na spravovaný Cluster Service Fabric](tutorial-managed-cluster-deploy-app.md)

Tato část řady se zabývá těmito postupy:

> [!div class="checklist"]
> * Škálování Service Fabric spravovaného uzlu clusteru

## <a name="prerequisites"></a>Požadavky

* Service Fabric spravovaný cluster (viz [*nasazení spravovaného clusteru*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](https://docs.microsoft.com/powershell/azure/release-notes-azureps?view=azps-4.7.0&preserve-view=true#azservicefabric) nebo novějším (viz [*instalace Azure PowerShell*](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.7.0&preserve-view=true)).

## <a name="scale-a-service-fabric-managed-cluster"></a>Škálování Service Fabric spravovaného clusteru
Změňte počet instancí pro zvýšení nebo snížení počtu uzlů v typu uzlu, který chcete škálovat. Názvy typů uzlů můžete najít v šabloně Azure Resource Manager (šablona ARM) z nasazení clusteru nebo v Service Fabric Explorer.  

> [!NOTE]
> Pokud je typ uzlu primární, nebudete moct přejít pod 3 uzly pro základní cluster SKU a 5 uzlů pro cluster Standard SKU.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

Cluster se začne automaticky upgradovat a po několika minutách se zobrazí další uzly.

## <a name="next-steps"></a>Další kroky

V tomto kroku jsme na spravovaném clusteru Service Fabric škálovat typ uzlu. Další informace o přidávání a odebírání typů uzlů najdete v tématech:

> [!div class="nextstepaction"]
> [Přidání a odebrání typů uzlů do spravovaného clusteru Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
