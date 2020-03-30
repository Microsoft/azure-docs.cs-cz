---
title: Správa skupin prostředků – Azure PowerShell
description: Pomocí Azure PowerShellu můžete spravovat skupiny prostředků prostřednictvím Azure Resource Manageru. Ukazuje, jak vytvořit, vypsat a odstranit skupiny prostředků.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 25e001927d5c06b4a7e5639faaa054ae18b12bb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248356"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Správa skupin prostředků Azure Resource Manager pomocí Azure PowerShellu

Přečtěte si, jak používat Azure PowerShell ve [Správci prostředků Azure](overview.md) ke správě skupin prostředků Azure. Správa prostředků Azure najdete v [tématu Správa prostředků Azure pomocí Azure PowerShellu](manage-resources-powershell.md).

Další články o správě skupin zdrojů:

- [Správa skupin prostředků Azure pomocí portálu Azure](manage-resources-portal.md)
- [Správa skupin prostředků Azure pomocí azure cli](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Co je skupina prostředků

Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků. Obecně přidejte prostředky, které sdílejí stejný životní cyklus, do stejné skupiny prostředků, abyste je mohli snadno nasadit, aktualizovat a odstranit jako skupinu.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků tedy určujete, kde se tato metadata ukládají. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

Skupina prostředků ukládá metadata o prostředcích. Když zadáte umístění pro skupinu prostředků, určujete, kde jsou tato metadata uložena.

## <a name="create-resource-groups"></a>Vytvořit skupiny prostředků

Následující skript prostředí PowerShell vytvoří skupinu prostředků a zobrazí skupinu prostředků.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Seznam skupin prostředků

Následující skript prostředí PowerShell uvádí skupiny prostředků v rámci vašeho předplatného.

```azurepowershell-interactive
Get-AzResourceGroup
```

Chcete-li získat jednu skupinu prostředků:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Odstranění skupin prostředků

Následující skript prostředí PowerShell odstraní skupinu prostředků:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Další informace o tom, jak Azure Resource Manager objednávky odstranění prostředků, najdete v [tématu Odstranění skupiny prostředků Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Nasazení prostředků do existující skupiny prostředků

Viz [Nasazení prostředků do existující skupiny prostředků](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Informace o ověření nasazení skupiny prostředků naleznete v tématu [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Nasazení skupiny prostředků a prostředků

Skupinu prostředků můžete vytvořit a nasadit prostředky do skupiny pomocí šablony Správce prostředků. Další informace naleznete v [tématu Vytvoření skupiny prostředků a nasazení prostředků](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Opětovné nasazení při selhání nasazení

Tato funkce je také známá jako *vrácení zpět při chybě*. Další informace naleznete [v tématu Znovu nasadit při selhání nasazení](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Přechod na jinou skupinu prostředků nebo předplatné

Prostředky ve skupině můžete přesunout do jiné skupiny prostředků. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Uzamčení skupin prostředků

Uzamčení zabrání ostatním uživatelům ve vaší organizaci v náhodném odstranění nebo úpravě kritických prostředků, jako je předplatné Azure, skupina prostředků nebo prostředek. 

Následující skript uzamkne skupinu prostředků, takže skupinu prostředků nelze odstranit.

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

## <a name="tag-resource-groups"></a>Označit skupiny prostředků

Značky můžete použít na skupiny prostředků a prostředky, které logicky uspořádají prostředky. Další informace najdete [v tématu Použití značek k uspořádání prostředků Azure](tag-resources.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Export skupin prostředků do šablon

Po nastavení skupiny prostředků můžete zobrazit šablonu Správce prostředků pro skupinu prostředků. Export šablony nabízí dvě výhody:

- Automatizujte budoucí nasazení řešení, protože šablona obsahuje úplnou infrastrukturu.
- Naučte se syntaxi šablony tak, že se podíváte na JavaScript Object Notation (JSON), který představuje vaše řešení.

Chcete-li exportovat všechny prostředky ve skupině prostředků, použijte rutinu [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) a zadejte název skupiny prostředků.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Uloží šablonu jako místní soubor.

Namísto exportu všech prostředků ve skupině prostředků můžete vybrat prostředky, které chcete exportovat.

Chcete-li exportovat jeden prostředek, předavažte toto ID prostředku.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Chcete-li exportovat více než jeden prostředek, předavěte ID prostředků v poli.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Při exportu šablony můžete určit, zda budou v šabloně použity parametry. Ve výchozím nastavení jsou zahrnuty parametry pro názvy prostředků, ale nemají výchozí hodnotu. Tuto hodnotu parametru musíte předat během nasazení.

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

V prostředku se parametr používá pro název.

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

Pokud parametr `-IncludeParameterDefaultValue` použijete při exportu šablony, parametr šablony obsahuje výchozí hodnotu, která je nastavena na aktuální hodnotu. Můžete použít tuto výchozí hodnotu nebo přepsat výchozí hodnotu předáním v jiné hodnotě.

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

Pokud použijete `-SkipResourceNameParameterization` parametr při exportu šablony, parametry pro názvy prostředků nejsou zahrnuty v šabloně. Místo toho je název prostředku nastaven přímo na prostředek na jeho aktuální hodnotu. Název nelze přizpůsobit během nasazení.

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

Funkce šablony exportu nepodporuje export prostředků Azure Data Factory. Informace o tom, jak exportovat prostředky Data Factory, najdete v [tématu Kopírování nebo klonování datové továrny v Azure Data Factory](https://aka.ms/exportTemplateViaAdf).

Chcete-li exportovat prostředky vytvořené prostřednictvím klasického modelu nasazení, musíte [je migrovat do modelu nasazení Správce prostředků](https://aka.ms/migrateclassicresourcetoarm).

Další informace najdete [v tématu Export jednoho a více prostředků do šablony na webu Azure Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Správa přístupu ke skupinám prostředků

[Řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/overview.md) je způsob, jakým spravujete přístup k prostředkům v Azure. Další informace najdete [v tématu Správa přístupu pomocí RBAC a Azure PowerShell .](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Další kroky

- Pokud se chcete naučit Správce prostředků Azure, přečtěte si [přehled správce prostředků Azure](overview.md).
- Syntaxe syntaxe šablony Správce prostředků najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](../templates/template-syntax.md).
- Chcete-li se dozvědět, jak vyvíjet šablony, podívejte se na [podrobné výukové programy](/azure/azure-resource-manager/).
- Pokud chcete zobrazit schémata šablon Azure Resource Manager, přečtěte si [odkaz na šablonu](/azure/templates/).