---
title: Horizontální navýšení kapacity Service Fabric spravovaného clusteru (Preview)
description: V tomto kurzu se naučíte škálovat typ uzlu Service Fabric spravovaného clusteru.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 769bcb339b2cc1419c7a3d92d0f08130029a9f95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785427"
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
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) nebo novějším (viz [*instalace Azure PowerShell*](/powershell/azure/install-az-ps)).

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
