---
title: Správa skupin v Azure Resource Manageru pomocí prostředí Azure PowerShell | Dokumentace Microsoftu
description: Pomocí Azure Powershellu ke správě skupin Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 3d6a102b794ca9c43e1dd18f923f6ce224596499
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296265"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Správa skupin prostředků Azure Resource Manageru pomocí prostředí Azure PowerShell

Zjistěte, jak pomocí Azure Powershellu s [Azure Resource Manageru](resource-group-overview.md) ke správě skupin prostředků Azure. Správa prostředků Azure, najdete v části [Správa prostředků Azure pomocí Azure Powershellu](./manage-resources-powershell.md).

Další články o správě skupin prostředků:

- [Správa skupin prostředků Azure pomocí webu Azure portal](./manage-resources-portal.md)
- [Správa skupin prostředků Azure pomocí Azure CLI](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Co je skupina prostředků

Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků. Obecně platí přidejte prostředky, které sdílejí stejný životní cyklus do stejné skupiny prostředků, takže můžete snadno nasadit, aktualizovat a odstranit jako skupina.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků tedy určujete, kde se tato metadata ukládají. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků, určujete, kde se tato metadata ukládají.

## <a name="create-resource-groups"></a>Vytvoření skupiny prostředků

Následující skript Powershellu vytvoří skupinu prostředků a poté zobrazí skupina prostředků.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Seznam skupin prostředků

Následující skript Powershellu zobrazuje seznam skupin prostředků v rámci vašeho předplatného.

```azurepowershell-interactive
Get-AzResourceGroup
```

Pokud chcete získat jednu skupinu prostředků:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Odstranění skupiny prostředků

Následující skript prostředí PowerShell odstraní skupinu prostředků:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Další informace o tom, jak Azure Resource Manageru orders odstranění prostředků najdete v tématu [odstranění skupiny prostředků Azure Resource Manageru](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Nasazení prostředků do existující skupiny prostředků

Zobrazit [nasadit prostředky do existující skupiny prostředků](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Pokud chcete ověřit nasazení skupiny prostředků, najdete v článku [testovací AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Nasazení skupiny prostředků a prostředků

Můžete vytvořit skupinu prostředků a nasazení prostředků do skupiny s použitím šablony Resource Manageru. Další informace najdete v tématu [vytvořte skupinu prostředků a nasazení prostředků](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Opětovné nasazení při nasazení se nezdaří

Tato funkce se také označuje jako *vrácení zpět při chybě*. Další informace najdete v tématu [opětovné nasazení při selhání nasazení](./resource-group-template-deploy.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Přesunout do jiné skupiny prostředků nebo předplatného

Prostředky ve skupině můžete přesunout do jiné skupiny prostředků. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](./resource-group-move-resources.md#move-resources).

## <a name="lock-resource-groups"></a>Skupiny prostředků zámku

Zamknutí zabrání ostatním uživatelům ve vaší organizaci omylem odstranit nebo upravit důležité prostředky, jako je předplatné Azure, skupinu prostředků nebo prostředek. 

Následující skript Zamkne skupinu prostředků, takže nelze odstranit skupinu prostředků.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Následující skript načte všech zámků pro skupinu prostředků:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Značka skupiny prostředků

Můžete provést značky u prostředků a skupin prostředků logicky tak uspořádat vaše prostředky. Informace najdete v tématu [použití značek k uspořádání prostředků Azure](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Export skupiny prostředků do šablon

Po nastavení skupiny prostředků, můžete zobrazit šablonu Resource Manageru pro skupinu prostředků. Export šablony nabízí dvě výhody:

- Budoucí nasazení řešení automatizace, protože šablona obsahuje kompletní infrastrukturu.
- Přečtěte si syntaxi šablony pohledem na zápisu JSON (JavaScript Object), který představuje vaše řešení.

Chcete-li exportovat všechny prostředky ve skupině prostředků, použijte [Export AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) rutinu a zadejte název skupiny prostředků.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Šablona uloží do místního souboru.

Místo vyexportování všechny prostředky ve skupině prostředků, můžete vybrat prostředky pro export.

Chcete-li exportovat jeden prostředek, předejte ID tohoto zdroje.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Pokud chcete exportovat více než jeden prostředek, předejte ID prostředků v poli.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Při exportu šablony, můžete určit, jestli jsou parametry v šabloně použít. Ve výchozím nastavení parametry pro názvy prostředků, které jsou zahrnuty, ale nemají výchozí hodnotu. Hodnota tohoto parametru musí projít během nasazení.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

V prostředku tento parametr se používá pro název.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Pokud používáte `-IncludeParameterDefaultValue` parametr při exportu šablony parametr šablony obsahuje výchozí hodnotu, která je nastavena na aktuální hodnotu. Můžete buď používat tato výchozí hodnota nebo výchozí hodnotu přepsat předáním jinou hodnotu.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Pokud používáte `-SkipResourceNameParameterization` parametr při exportu šablony parametry pro názvy prostředků nejsou zahrnuty v šabloně. Název prostředku je místo toho nastavit přímo na prostředek, který chcete jeho aktuální hodnota. Nelze upravit název během nasazení.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Další informace najdete v tématu [export jednoho a víc prostředků šablony na webu Azure portal](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Správa přístupu ke skupinám prostředků

[Řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) je způsob správy přístupu k prostředkům v Azure. Další informace najdete v tématu [správě přístupu pomocí RBAC a prostředí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Další postup

- Další Azure Resource Manageru najdete v tématu [přehled Azure Resource Manageru](./resource-group-overview.md).
- Seznamte se se syntaxí šablony Resource Manageru, najdete v článku [Princip struktury a syntaxe šablon Azure Resource Manageru](./resource-group-authoring-templates.md).
- Zjistěte, jak vyvíjet šablony, najdete v článku [podrobné kurzy](/azure/azure-resource-manager/).
- Schémata šablon Azure Resource Manageru najdete v tématu [referenčními informacemi k šablonám](/azure/templates/).