---
title: 'Quickstart: New policy assignment with Azure CLI'
description: In this quickstart, you use Azure CLI to create an Azure Policy assignment to identify non-compliant resources.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 80dbccdb728da94d9f9fdd0aeb506ade40fd7394
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482628"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Quickstart: Create a policy assignment to identify non-compliant resources with Azure CLI

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. _Neodpovídají_ přiřazení zásad.

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V této příručce se Azure CLI používá k vytvoření přiřazení zásady a identifikaci prostředků, které nedodržují předpisy, v prostředí Azure.

## <a name="prerequisites"></a>Předpoklady

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

- This quickstart requires that you run Azure CLI version 2.0.76 or later to install and use the CLI locally. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

- Register the Azure Policy Insights resource provider using Azure CLI. Registrace poskytovatele prostředků zajistí, že s ním vaše předplatné bude fungovat. To register a resource provider, you must have permission to the register resource provider operation. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Další informace o registraci a zobrazení poskytovatelů prostředků najdete v tématu [Poskytovatelé a typy prostředků](../../azure-resource-manager/resource-manager-supported-services.md).

- Pokud jste to ještě neudělali, nainstalujte si nástroj [ARMClient](https://github.com/projectkudu/ARMClient). Jedná se o nástroj, který posílá žádosti HTTPS do rozhraní API založených na Azure Resource Manageru.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte definici **Auditovat virtuální počítače, které nepoužívají spravované disky**. This policy definition identifies resources that aren't compliant to the conditions set in the policy definition.

Spuštěním následujícího příkazu vytvořte přiřazení zásady:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

Předchozí příkaz používá následující informace:

- **Name** – skutečný název přiřazení. V tomto příkladu je použitý název _audit-vm-manageddisks_.
- **DisplayName** – zobrazovaný název přiřazení zásady. In this case, you're using _Audit VMs without managed disks Assignment_.
- **Policy** – ID definice zásady, kterou používáte k vytvoření tohoto přiřazení. In this case, it's the ID of policy definition _Audit VMs that do not use managed disks_. ID definice zásady získáte spuštěním příkazu `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`.
- **Scope** – Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupinu prostředků. Nezapomeňte nahradit &lt;scope&gt; názvem vaší skupiny prostředků.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Pokud chcete zobrazit prostředky, které nedodržují předpisy v rámci tohoto nového přiřazení, získejte ID přiřazení zásady spuštěním následujících příkazů:

```azurepowershell-interactive
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

For more information about policy assignment IDs, see [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment).

Pak spuštěním následujícího příkazu získejte a zapište do souboru JSON ID prostředků, které nedodržují předpisy:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Vaše výsledky budou vypadat přibližně jako v následujícím příkladu:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Tyto výsledky jsou srovnatelné s tím, co se obvykle zobrazuje v části **Nekompatibilní prostředky** v zobrazení na webu Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

To remove the assignment created, use the following command:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

To learn more about assigning policies to validate that new resources are compliant, continue to the tutorial for:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)