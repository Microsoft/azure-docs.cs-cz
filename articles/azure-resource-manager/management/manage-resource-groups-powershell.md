---
title: Správa skupin prostředků – Azure PowerShell
description: Pomocí Azure PowerShell můžete spravovat skupiny prostředků přes Azure Resource Manager. Ukazuje, jak vytvořit, vypsat a odstranit skupiny prostředků.
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao
ms.openlocfilehash: 5c72dd234cf6d8558fb49882170fe33bac1900dd
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372132"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Správa Azure Resource Manager skupin prostředků pomocí Azure PowerShell

Naučte se používat Azure PowerShell s [Azure Resource Manager](overview.md) ke správě skupin prostředků Azure. Informace o správě prostředků Azure najdete v tématu [Správa prostředků Azure pomocí Azure PowerShell](manage-resources-powershell.md).

Další články o správě skupin prostředků:

- [Správa skupin prostředků Azure pomocí Azure Portal](manage-resources-portal.md)
- [Správa skupin prostředků Azure pomocí Azure CLI](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Co je skupina prostředků

Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků. Obecně můžete přidat prostředky, které sdílejí stejný životní cyklus do stejné skupiny prostředků, abyste je mohli snadno nasadit, aktualizovat a odstranit jako skupinu.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků tedy určujete, kde se tato metadata ukládají. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

Skupina prostředků ukládá metadata o prostředcích. Když zadáte umístění pro skupinu prostředků, určíte, kde jsou tato metadata uložená.

## <a name="create-resource-groups"></a>Vytvoření skupin prostředků

Následující skript prostředí PowerShell vytvoří skupinu prostředků.

```azurepowershell-interactive
New-AzResourceGroup -Name demoResourceGroup -Location westus
```

## <a name="list-resource-groups"></a>Seznam skupin prostředků

Následující skript prostředí PowerShell vypíše skupiny prostředků v rámci vašeho předplatného.

```azurepowershell-interactive
Get-AzResourceGroup
```

Jak získat jednu skupinu prostředků:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Odstranění skupin prostředků

Následující skript PowerShellu odstraní skupinu prostředků:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Další informace o tom, jak Azure Resource Manager objednává odstranění prostředků, najdete v tématu [Azure Resource Manager odstranění skupiny prostředků](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Nasazení prostředků do existující skupiny prostředků

Viz [nasazení prostředků do existující skupiny prostředků](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Pokud chcete ověřit nasazení skupiny prostředků, přečtěte si téma [test-AzResourceGroupDeployment](/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Nasazení skupiny prostředků a prostředků

Můžete vytvořit skupinu prostředků a nasadit prostředky do skupiny pomocí šablony Správce prostředků. Další informace najdete v tématu [Vytvoření skupiny prostředků a nasazení prostředků](../templates/deploy-to-subscription.md#resource-groups).

## <a name="redeploy-when-deployment-fails"></a>Znovu nasadit v případě neúspěchu nasazení

Tato funkce se také označuje jako *vrácení zpět s chybou*. Další informace najdete v tématu [opětovné nasazení v případě neúspěchu nasazení](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Přesunout do jiné skupiny prostředků nebo předplatného

Prostředky ve skupině můžete přesunout do jiné skupiny prostředků. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Uzamčení skupin prostředků

Uzamykání brání jiným uživatelům ve vaší organizaci v neúmyslném odstranění nebo úpravě důležitých prostředků, jako je například předplatné Azure, skupina prostředků nebo prostředek. 

Následující skript zamkne skupinu prostředků, takže skupinu prostředků nejde odstranit.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Následující skript získá všechny zámky pro skupinu prostředků:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](lock-resources.md).

## <a name="tag-resource-groups"></a>Označení skupin prostředků

Můžete použít značky pro skupiny prostředků a prostředky k logické organizaci vašich assetů. Informace najdete v tématu [použití značek k uspořádání prostředků Azure](tag-resources.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Export skupin prostředků do šablon

Po nastavení skupiny prostředků můžete zobrazit šablonu Správce prostředků pro skupinu prostředků. Export šablony nabízí dvě výhody:

- Automatizujte budoucí nasazení řešení, protože šablona obsahuje úplnou infrastrukturu.
- Podívejte se na JavaScript Object Notation (JSON), která představuje vaše řešení, a Naučte se syntaxí šablony.

Pokud chcete exportovat všechny prostředky ve skupině prostředků, použijte rutinu [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) a zadejte název skupiny prostředků.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Uloží šablonu jako místní soubor.

Místo exportování všech prostředků ve skupině prostředků můžete vybrat, které prostředky se mají exportovat.

Pokud chcete exportovat jeden prostředek, předejte toto ID prostředku.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Chcete-li exportovat více než jeden prostředek, předejte ID prostředků v poli.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Při exportování šablony můžete určit, zda jsou parametry použity v šabloně. Ve výchozím nastavení jsou k dispozici parametry pro názvy prostředků, ale nemají výchozí hodnotu. Tuto hodnotu parametru musíte předat během nasazování.

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

V prostředku je parametr použit pro název.

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

Použijete-li `-IncludeParameterDefaultValue` parametr při exportování šablony, parametr šablony obsahuje výchozí hodnotu, která je nastavena na aktuální hodnotu. Můžete buď použít tuto výchozí hodnotu, nebo přepsat výchozí hodnotu tak, že předáte jinou hodnotu.

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

Použijete-li `-SkipResourceNameParameterization` parametr při exportování šablony, parametry pro názvy prostředků nejsou zahrnuty do šablony. Místo toho je název prostředku nastaven přímo na prostředek na jeho aktuální hodnotu. Během nasazování nemůžete přizpůsobit název.

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

Funkce Exportovat šablonu nepodporuje export Azure Data Factorych prostředků. Další informace o tom, jak můžete exportovat Data Factory prostředky, najdete [v tématu kopírování nebo klonování datové továrny v Azure Data Factory](https://aka.ms/exportTemplateViaAdf).

Chcete-li exportovat prostředky vytvořené prostřednictvím modelu nasazení Classic, je nutné [je migrovat do modelu nasazení Správce prostředků](https://aka.ms/migrateclassicresourcetoarm).

Další informace najdete v tématu [Export jednoho a více prostředků do šablony v Azure Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Správa přístupu ke skupinám prostředků

[Řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md) je způsob, jakým můžete spravovat přístup k prostředkům v Azure. Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Další kroky

- Informace o Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](overview.md).
- Další informace o syntaxi šablon Správce prostředků naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](../templates/template-syntax.md).
- Další informace o vývoji šablon najdete v [podrobných kurzech](../index.yml).
- Chcete-li zobrazit schémata šablon Azure Resource Manager, přečtěte si téma [reference šablony](/azure/templates/).
