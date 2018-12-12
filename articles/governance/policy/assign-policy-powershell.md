---
title: Rychlý start – Použití PowerShellu k vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, v prostředí Azure
description: V tomto rychlém startu pomocí PowerShellu vytvoříte přiřazení zásady Azure Policy pro identifikaci prostředků, které nedodržují předpisy.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 79e192bae8a1d9ac3e27f35dc2f64befc5a75a84
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078802"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>Rychlý start: Vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí modulu Azure RM PowerShellu

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. V tomto rychlém startu vytvoříte přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky. Po dokončení budete identifikovat virtuální počítače, které *nedodržují předpisy* přiřazení zásady.

Modul AzureRM PowerShellu slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tato příručka vysvětluje, jak pomocí AzureRM vytvořit přiřazení zásady. Tato zásada identifikuje v prostředí Azure prostředky, které nedodržují předpisy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Pokud jste to ještě neudělali, nainstalujte si nástroj [ARMClient](https://github.com/projectkudu/ARMClient). Jedná se o nástroj, který posílá žádosti HTTPS do rozhraní API založených na Azure Resource Manageru.
- Než začnete, ujistěte se, že je nainstalovaná nejnovější verze PowerShellu. Podrobné informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).
- Aktualizujte modul AzureRM PowerShellu na nejnovější verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).
- Pomocí Azure PowerShellu zaregistrujte poskytovatele prostředků Policy Insights. Registrace poskytovatele prostředků zajistí, že s ním vaše předplatné bude fungovat. Zaregistrovat poskytovatele prostředků, musíte mít oprávnění pro operace registrace poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Další informace o registraci a zobrazení poskytovatelů prostředků najdete v tématu [Poskytovatelé a typy prostředků](../../azure-resource-manager/resource-manager-supported-services.md).

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte *Audit virtuálních počítačů bez spravovaných disků* definice. Tato definice zásady identifikuje prostředky, které nedodržují předpisy podmínky nastavené v definici zásad.

Spuštěním následujících příkazů vytvořte nové přiřazení zásady:

```azurepowershell-interactive
$rg = Get-AzureRmResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Předchozí příkazy používají následující informace:

- **Name** – skutečný název přiřazení.  V tomto příkladu je použitý název *audit-vm-manageddisks*.
- **DisplayName** – zobrazovaný název přiřazení zásady. V tomto případě používáte *Audit virtuálních počítačů bez spravovaných disků přiřazení*.
- **Definition** – Definice zásady, kterou používáte k vytvoření tohoto přiřazení. V takovém případě je ID definice zásady *Audit virtuálních počítačů, které nepoužívají spravované disky*.
- **Scope** – Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupinu prostředků. Nezapomeňte nahradit &lt;scope&gt; názvem vaší skupiny prostředků.

Nyní jste připraveni identifikovat prostředky, které nedodržují předpisy, abyste porozuměli stavu dodržování předpisů ve vašem prostředí.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Pomocí následujících informací identifikujte prostředky, které nedodržují předpisy přiřazení zásady, kterou jste vytvořili. Spusťte následující příkazy:

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Další informace o ID přiřazení zásad najdete v tématu věnovaném rutině [Get-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Pak spuštěním následujícího příkazu získejte a zapište do souboru JSON ID prostředků, které nedodržují předpisy:

```
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

Odebrat přiřazení vytvořené, použijte následující příkaz:

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazování zásad pro ověření, že nové prostředky jsou kompatibilní, i nadále najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)