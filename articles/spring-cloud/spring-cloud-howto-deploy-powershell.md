---
title: Nasazení jarního cloudu Azure pomocí Azure PowerShell
description: Nasazení jarního cloudu Azure pomocí Azure PowerShell
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549934"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Nasazení jarního cloudu Azure pomocí Azure PowerShell

Tento článek popisuje, jak můžete vytvořit instanci Azure jarního cloudu pomocí modulu [AZ. SpringCloud](/powershell/module/Az.SpringCloud) PowerShellu.

## <a name="requirements"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > I když je modul PowerShell **AZ. SpringCloud** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny. Až bude tento modul PowerShellu všeobecně dostupný, bude součástí budoucna ve výchozím nastavení AZ PowerShell Module releases a Available v rámci Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Vyberte konkrétní předplatné pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

Následující příklad vytvoří skupinu prostředků se zadaným názvem a v zadaném umístění.

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Zřízení nové instance služby Azure Pramenitého cloudu

K vytvoření nové instance Azure jaře cloudu použijete rutinu [New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) . Následující příklad vytvoří ve dříve vytvořené skupině prostředků službu jarní cloudovou službu Azure se zadaným názvem.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>Vytvoření nové aplikace pro cloudovou službu Azure jaře

Pokud chcete vytvořit novou aplikaci, použijte rutinu [New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) . Následující příklad vytvoří cloudovou aplikaci Azure s názvem `gateway` .

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>Vytvořit nové nasazení v Azure jaře Cloud

Chcete-li vytvořit nové nasazení, použijte rutinu [New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) . Následující příklad vytvoří nasazení cloudu Azure ve jarním prostředí s názvem `default` pro `gateway` aplikaci.

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Získání služby jarního cloudu Azure

K získání služby jarního cloudu Azure a jejích vlastností použijte rutinu [Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) . Následující příklad načte informace o zadané službě jarní cloudové služby Azure.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Získání cloudové aplikace Azure pro jaře

K získání cloudové aplikace a jejích vlastností Azure jaře použijte rutinu [Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) . Následující příklad načte informace o `gateway` jarní cloudové aplikaci.

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Získání nasazení pro jarní Cloud v Azure

K získání nasazení a vlastností cloudového cloudu Azure použijte rutinu [Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) . Následující příklad načte informace o `default` nasazení jarního cloudu.

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud prostředky vytvořené v tomto článku nepotřebujete, můžete je odstranit spuštěním následujících příkladů.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Odstranění nasazení v cloudu Azure jaře

Pokud chcete odebrat nasazení v cloudu Azure pružiny, použijte rutinu [Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) . Následující příklad odstraní nasazení cloudové aplikace Azure s názvem `default` pro zadanou službu a aplikaci.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Odstranění cloudové aplikace Azure jaře

Pokud chcete odebrat jarní cloudovou aplikaci, použijte rutinu [Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) . Následující příklad odstraní `gateway` aplikaci v zadané službě a skupině prostředků.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Odstranění jarní cloudové služby Azure

K odebrání jarní cloudové služby Azure použijte rutinu [Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) . Následující příklad odstraní zadanou cloudovou službu Azure jaře.

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

> [!CAUTION]
> Následující příklad odstraní zadanou skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
> Pokud v zadané skupině prostředků existují prostředky mimo rozsah tohoto článku, budou také odstraněny.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>Další kroky

[Prostředky pro vývojáře pro cloudové cloudy Azure](spring-cloud-resources.md)
