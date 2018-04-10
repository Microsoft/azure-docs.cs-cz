---
title: Použití Azure CLI k vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, v prostředí Azure | Dokumentace Microsoftu
description: Použijte Azure CLI k vytvoření přiřazení zásady Azure Policy pro identifikaci prostředků, které nedodržují předpisy.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/30/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 356521c0d1771906e41c816d523d687fe8902acd
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, v prostředí Azure pomocí Azure CLI

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. *Neodpovídají* přiřazení zásad.

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V této příručce se Azure CLI používá k vytvoření přiřazení zásady a identifikaci prostředků, které nedodržují předpisy, v prostředí Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Tento rychlý start vyžaduje, abyste pro místní používání a instalaci rozhraní příkazového řádku spustili Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Požadavky

Pomocí Azure CLI zaregistrujte poskytovatele prostředků Policy Insights. Registrace poskytovatele prostředků zajistí, že s ním vaše předplatné bude fungovat. Když chcete registrovat poskytovatele prostředků, musíte mít oprávnění k provedení operace akce registrace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

```
az provider register –-namespace 'Microsoft.PolicyInsights
```
Další informace o registraci a zobrazení poskytovatelů prostředků najdete v tématu [Poskytovatelé a typy prostředků](../azure-resource-manager/resource-manager-supported-services.md).


## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte definici Audit virtuálních počítačů bez spravovaných disků. Tato definice zásady identifikuje prostředky, které nesplňují podmínky nastavené v definici zásady.

Spuštěním následujícího příkazu vytvořte přiřazení zásady:

```
az policy assignment create --name 'Audit Virtual Machines without Managed Disks Assignment' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
```

Předchozí příkaz používá následující informace:

- **Name** – Zobrazovaný název přiřazení zásady. V tomto případě používáte přiřazení *Audit virtuálních počítačů bez spravovaných disků*.
- **Policy** – ID definice zásady, kterou používáte k vytvoření tohoto přiřazení. V tomto případě je to definice zásady *Audit virtuálních počítačů bez spravovaných disků*. ID definice zásady získáte spuštěním příkazu `az policy definition show --name 'Audit Virtual Machines without Managed Disks Assignment'`.
- **Scope** – Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupinu prostředků. Nezapomeňte nahradit &lt;scope&gt; názvem vaší skupiny prostředků.
- **Sku** – Tento příkaz vytvoří přiřazení zásady s úrovní Standard. Úroveň Standard vám umožní dosáhnout správy, vyhodnocování dodržování předpisů a odstraňování problémů ve velkém měřítku. Další podrobnosti o cenových úrovních najdete v tématu [Ceny služby Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).


## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Pokud chcete zobrazit prostředky, které nedodržují předpisy v rámci tohoto nového přiřazení, získejte ID přiřazení zásady spuštěním následujících příkazů:

```
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```
$policyAssignment.PolicyAssignmentId
```

Další informace o ID přiřazení zásad najdete v tématu věnovaném příkazu [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Pak spuštěním následujícího příkazu získejte a zapište do souboru JSON ID prostředků, které nedodržují předpisy:

```
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

Další příručky v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími kurzy, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, spuštěním následujícího příkazu odstraňte přiřazení, které jste vytvořili:

```azurecli
az policy assignment delete –name Audit Virtual Machines without Managed Disks Assignment --scope /subscriptions/ <subscriptionID> / <resourceGroupName>
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazování zásad a zajištění, aby prostředky vytvořené **v budoucnu** dodržovaly předpisy, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./create-manage-policy.md)
