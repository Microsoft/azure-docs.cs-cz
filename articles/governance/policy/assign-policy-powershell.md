---
title: Vytvořit zásadu pro nekompatibilní prostředky pomocí Azure Powershellu
description: Použití Azure Powershellu k vytvoření přiřazení Azure Policy pro identifikaci prostředků, které nedodržují předpisy.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: de8192ee0f0dad1ccc385aa28892a3ef4f5c4a86
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338729"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Vytvoření přiřazení zásady pro identifikaci neodpovídajících prostředků pomocí Azure Powershellu

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. V tomto rychlém startu vytvoříte přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky. Po dokončení budete identifikovat virtuální počítače, které *nedodržují předpisy* přiřazení zásady.

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tato příručka vysvětluje, jak použít Az k vytvoření přiřazení zásady. Tato zásada identifikuje v prostředí Azure prostředky, které nedodržují předpisy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud jste to ještě neudělali, nainstalujte si nástroj [ARMClient](https://github.com/projectkudu/ARMClient). Jedná se o nástroj, který posílá žádosti HTTPS do rozhraní API založených na Azure Resource Manageru.
- Než začnete, ujistěte se, že je nainstalovaná nejnovější verze prostředí Azure PowerShell. Zobrazit [instalace modulu Azure PowerShell](/powershell/azure/install-az-ps) podrobné informace.
- Pomocí Azure PowerShellu zaregistrujte poskytovatele prostředků Policy Insights. Registrace poskytovatele prostředků zajistí, že s ním vaše předplatné bude fungovat. Zaregistrovat poskytovatele prostředků, musíte mít oprávnění pro operace registrace poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

  ```azurepowershell-interactive
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Další informace o registraci a zobrazení poskytovatelů prostředků najdete v tématu [Poskytovatelé a typy prostředků](../../azure-resource-manager/resource-manager-supported-services.md).

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte *Audit virtuálních počítačů bez spravovaných disků* definice. Tato definice zásady identifikuje prostředky, které nedodržují předpisy podmínky nastavené v definici zásad.

Spuštěním následujících příkazů vytvořte nové přiřazení zásady:

```azurepowershell-interactive
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
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
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Další informace o ID přiřazení zásad najdete v tématu [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment).

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

Odebrat přiřazení vytvořené, použijte následující příkaz:

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazování zásad pro ověření, že nové prostředky jsou kompatibilní, i nadále najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)