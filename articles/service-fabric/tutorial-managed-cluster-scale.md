---
title: Horizontální navýšení kapacity Service Fabric spravovaného clusteru (Preview)
description: V tomto kurzu se naučíte škálovat typ uzlu Service Fabric spravovaného clusteru.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 01b299744d462496296884211eff08b7a9c64687
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316141"
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

## <a name="prerequisites"></a>Předpoklady

* Service Fabric spravovaný cluster (viz [*nasazení spravovaného clusteru*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps?preserve-view=true&view=azps-4.7.0#azservicefabric) nebo novějším (viz [*instalace Azure PowerShell*](/powershell/azure/install-az-ps?preserve-view=true&view=azps-4.7.0)).

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