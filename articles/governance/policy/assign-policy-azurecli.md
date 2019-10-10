---
title: Vytvoření zásady pro audit prostředků pomocí Azure CLI
description: Pomocí Azure CLI vytvořte přiřazení Azure Policy k identifikaci prostředků, které nedodržují předpisy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: azure-policy
ms.openlocfilehash: a7e65e304039c266f92f8068b796323197058cc1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255956"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí Azure CLI

Prvním krokem při porozumění dodržování předpisů v Azure je určení stavu vašich prostředků.
Tento rychlý Start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. Neodpovídají přiřazení zásad.

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tato příručka používá Azure CLI k vytvoření přiřazení zásady a identifikaci prostředků, které nedodržují předpisy, v prostředí Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Tento rychlý Start vyžaduje, abyste spustili Azure CLI verze 2.0.4 nebo novější, aby bylo možné místně nainstalovat a používat rozhraní příkazového řádku. Pokud chcete zjistit verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Požadované součásti

Zaregistrujte poskytovatele prostředků služby Azure Policy Insights pomocí Azure CLI. Když zaregistrujete poskytovatele prostředků, zajistíte, že vaše předplatné spolupracuje s ním. Chcete-li zaregistrovat poskytovatele prostředků, musíte mít oprávnění k operaci registrovat poskytovatele prostředků. Tato operace je obsažena v rolích přispěvatel a Owner. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

```azurecli-interactive
az provider register --namespace 'Microsoft.PolicyInsights'
```

Další informace o registraci a zobrazení poskytovatelů prostředků najdete v tématu [poskytovatelé a typy prostředků](../../azure-resource-manager/resource-manager-supported-services.md) .

Pokud jste to ještě neudělali, nainstalujte si [ARMClient](https://github.com/projectkudu/ARMClient). Jedná se o nástroj, který odesílá požadavky HTTP na Azure Resource Manager rozhraní API na základě.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte **virtuální počítače auditu, které nepoužívají definici spravovaných disků** . Tato definice zásady identifikuje prostředky, které nesplňují podmínky nastavené v definici zásady.

Spuštěním následujícího příkazu vytvořte přiřazení zásady:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

Předchozí příkaz používá následující informace:

- **Název** – skutečný název přiřazení. V tomto příkladu se použil *audit-VM-manageddisks* .
- **DisplayName** – zobrazovaný název přiřazení zásady. V takovém případě použijete *přiřazení audit virtuálních počítačů bez spravovaných disků*.
- **Policy (zásady** ) – ID definice zásady na základě toho, kterou používáte k vytvoření přiřazení. V tomto případě se jedná o ID *virtuálních počítačů auditu definice zásad, které nepoužívají spravované disky*. Chcete-li získat ID definice zásady, spusťte tento příkaz: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Scope** – obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude uplatňovat. Může být v rozsahu od předplatného do skupin prostředků. Nezapomeňte nahradit &lt;scope @ no__t-1 názvem vaší skupiny prostředků.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Pokud chcete zobrazit prostředky, které nedodržují předpisy v rámci tohoto nového přiřazení, Získejte ID přiřazení zásady spuštěním následujících příkazů:

```azurepowershell-interactive
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Další informace o ID přiřazení zásad najdete v tématu [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment).

Potom spuštěním následujícího příkazu Získejte ID prostředků nevyhovujících prostředků, které jsou ve výstupu, do souboru JSON:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Vaše výsledky budou vypadat podobně jako v následujícím příkladu:

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

Výsledky jsou srovnatelné s tím, co se obvykle zobrazuje v seznamu **nekompatibilní prostředky** v zobrazení Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat vytvořené přiřazení, použijte následující příkaz:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, v prostředí Azure.

Další informace o přiřazování zásad k ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a Správa zásad](./tutorials/create-and-manage.md)