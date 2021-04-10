---
title: Přidání a odebrání typů uzlů Service Fabric spravovaného clusteru (Preview)
description: V tomto kurzu se dozvíte, jak přidat a odebrat typy uzlů Service Fabric spravovaného clusteru.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: bb33512652677fc4e46d8ba3668dca985bbcfe01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791236"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>Kurz: Přidání a odebrání typů uzlů z Service Fabric spravovaného clusteru (Preview)

V této sérii kurzů si probereme následující:

> [!div class="checklist"]
> * [Postup nasazení spravovaného clusteru Service Fabric](tutorial-managed-cluster-deploy.md)
> * [Postup horizontálního navýšení kapacity Service Fabric spravovaného clusteru](tutorial-managed-cluster-scale.md)
> * Postup přidání a odebrání uzlů ve spravovaném clusteru s Service Fabric
> * [Postup nasazení aplikace na spravovaný Cluster Service Fabric](tutorial-managed-cluster-deploy-app.md)

Tato část řady se zabývá těmito postupy:

> [!div class="checklist"]
> * Přidat typ uzlu do spravovaného clusteru Service Fabric
> * Odstranění typu uzlu z Service Fabric spravovaného clusteru

## <a name="prerequisites"></a>Požadavky

* Service Fabric spravovaný cluster (viz [*nasazení spravovaného clusteru*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) nebo novějším (viz [*instalace Azure PowerShell*](/powershell/azure/install-az-ps)).

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>Přidat typ uzlu do spravovaného clusteru Service Fabric

Typ uzlu můžete přidat do spravovaného clusteru Service Fabric pomocí Azure Resource Manager šablony, PowerShellu nebo rozhraní příkazového řádku. V tomto kurzu budeme přidávat typ uzlu pomocí Azure PowerShell.

Chcete-li vytvořit nový typ uzlu, bude nutné definovat tři vlastnosti:

* **Název typu uzlu**: název, který je jedinečný ze všech existujících typů uzlů v clusteru.
* **Počet instancí**: počáteční počet uzlů nového typu uzlu.
* **Velikost virtuálního počítače**: SKU virtuálního počítače pro uzly. Pokud není zadaný, použije se výchozí hodnota *Standard_D2* .

> [!NOTE]
> Pokud je typ uzlu přidaný jako první nebo pouze typ uzlu v clusteru, je nutné použít primární vlastnost.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>Odebrání typu uzlu z Service Fabric spravovaného clusteru

Chcete-li odebrat typ uzlu z Service Fabric spravovaného clusteru, je nutné použít prostředí PowerShell nebo rozhraní příkazového řádku (CLI). V tomto kurzu odstraníme typ uzlu pomocí Azure PowerShell.

> [!NOTE]
> Není možné odebrat primární typ uzlu, pokud se jedná o jediný typ primárního uzlu v clusteru.  

Postup odebrání typu uzlu:

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>Další kroky

 V této části jsme přidali a odstranili typy uzlů. Informace o tom, jak nasadit aplikaci do spravovaného clusteru Service Fabric, najdete v tématu:

> [!div class="nextstepaction"]
> [Nasazení aplikace do spravovaného clusteru Service Fabric](tutorial-managed-cluster-deploy-app.md)
