---
title: 'Rychlý start: Použití PowerShellu k vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, v prostředí Azure | Microsoft Docs'
description: V tomto rychlém startu pomocí PowerShellu vytvoříte přiřazení zásady Azure Policy pro identifikaci prostředků, které nedodržují předpisy.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 3/14/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 45c5ccd0f891a5592eee7400de108c5097f75286
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>Rychlý start: Vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí modulu Azure RM PowerShellu

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. V tomto rychlém startu vytvoříte přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky. Po dokončení budete identifikovat virtuální počítače, které *nedodržují předpisy* přiřazení zásady.

Modul AzureRM PowerShellu slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tato příručka vysvětluje, jak pomocí AzureRM vytvořit přiřazení zásady. Tato zásada identifikuje v prostředí Azure prostředky, které nedodržují předpisy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Než začnete, ujistěte se, že je nainstalovaná nejnovější verze PowerShellu. Podrobné informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).
- Aktualizujte modul AzureRM PowerShellu na nejnovější verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíme definici *Audit virtuálních počítačů bez spravovaných disků*. Tato definice zásady identifikuje prostředky, které nesplňují podmínky nastavené v definici zásady.

Spuštěním následujících příkazů vytvořte nové přiřazení zásady:

```powershell
$rg = Get-AzureRmResourceGroup -Name "<resourceGroupName>"

$definition = Get-AzureRmPolicyDefinition -Name "Audit Virtual Machines without Managed Disks"

New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition -Sku @{Name='A1';Tier='Standard'}

```

Předchozí příkazy používají následující informace:

- **Name** – Zobrazovaný název přiřazení zásady. V tomto případě používáte přiřazení *Audit virtuálních počítačů bez spravovaných disků*.
- **Definition** – Definice zásady, kterou používáte k vytvoření tohoto přiřazení. V tomto případě je to definice zásady *Audit virtuálních počítačů bez spravovaných disků*.
- **Scope** – Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupinu prostředků. Nezapomeňte nahradit &lt;scope&gt; názvem vaší skupiny prostředků.
- **Sku** – Tento příkaz vytvoří přiřazení zásady s úrovní Standard. Úroveň Standard vám umožní dosáhnout správy, vyhodnocování dodržování předpisů a odstraňování problémů ve velkém měřítku. Další podrobnosti o cenových úrovních najdete v tématu [Ceny služby Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).


Nyní jste připraveni identifikovat prostředky, které nedodržují předpisy, abyste porozuměli stavu dodržování předpisů ve vašem prostředí.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Pomocí následujících informací identifikujte prostředky, které nedodržují předpisy přiřazení zásady, kterou jste vytvořili. Spusťte následující příkazy:

```powershell
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```powershell
$policyAssignment.PolicyAssignmentId
```

Další informace o ID přiřazení zásad najdete v tématu věnovaném příkazu [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Pak spuštěním následujícího příkazu získejte a zapište do souboru JSON ID prostředků, které nedodržují předpisy:

```powershell
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```
Vaše výsledky budou vypadat přibližně jako v následujícím příkladu:


```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
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

Další příručky v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími kurzy, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, spuštěním následujícího příkazu můžete odstranit přiřazení, která jste vytvořili:

```powershell
Remove-AzureRmPolicyAssignment -Name "Audit Virtual Machines without Managed Disks Assignment" -Scope /subscriptions/<subscriptionID>/<resourceGroupName>
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazování zásad a zajištění, aby **budoucí** vytvořené prostředky dodržovaly předpisy, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./create-manage-policy.md)
