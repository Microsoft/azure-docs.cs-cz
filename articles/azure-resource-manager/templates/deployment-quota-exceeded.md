---
title: Překročena kvóta nasazení
description: Popisuje, jak vyřešit chybu, která má více než 800 nasazení v historii skupiny prostředků.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 919cd9a3482401cd47516e2677b0bf58387488b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245085"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Vyřešit chybu, pokud počet nasazení překročí 800

Každá skupina prostředků je omezena na 800 nasazení v historii nasazení. Tento článek popisuje chybu, která se zobrazí při selhání nasazení, protože by překročilpovolené nasazení 800. Chcete-li tuto chybu vyřešit, odstraňte nasazení z historie skupiny prostředků. Odstranění nasazení z historie nemá vliv na žádné prostředky, které byly nasazeny.

## <a name="symptom"></a>Příznak

Během nasazení se zobrazí chyba oznamující, že aktuální nasazení překročí kvótu 800 nasazení.

## <a name="solution"></a>Řešení

### <a name="azure-cli"></a>Azure CLI

Pomocí příkazu [delete skupiny nasazení az](/cli/azure/group/deployment) odstraňte nasazení z historie.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Chcete-li odstranit všechna nasazení starší než pět dní, použijte:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

Aktuální počet v historii nasazení můžete získat pomocí následujícího příkazu:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí příkazu [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) odstraňte nasazení z historie.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Chcete-li odstranit všechna nasazení starší než pět dní, použijte:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Aktuální počet v historii nasazení můžete získat pomocí následujícího příkazu:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Řešení třetích stran

Konkrétní scénáře řeší následující externí řešení:

* [Aplikace Logiky Azure a řešení PowerShellu](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Rozšíření AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
