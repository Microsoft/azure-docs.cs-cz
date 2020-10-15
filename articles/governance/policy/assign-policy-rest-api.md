---
title: 'Rychlý Start: nové přiřazení zásad s REST API'
description: V tomto rychlém startu použijete REST API k vytvoření přiřazení Azure Policy k identifikaci prostředků, které nedodržují předpisy.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: ab05079c5bb319f0808a743a1d668649df51b1b3
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074001"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí REST API

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. _Neodpovídají_ přiřazení zásad.

REST API slouží k vytváření a správě prostředků Azure. Tato příručka používá REST API k vytvoření přiřazení zásady a identifikaci prostředků, které nedodržují předpisy, v prostředí Azure.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Pokud jste to ještě neudělali, nainstalujte [ARMClient](https://github.com/projectkudu/ARMClient). Jedná se o nástroj, který odesílá požadavky HTTP na Azure Resource Manager rozhraní REST API. Pomocí funkce vyzkoušet si ji můžete také použít v dokumentaci nebo nástrojích, jako je rutina [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) nebo [post](https://www.postman.com)prostředí PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásad

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte **virtuální počítače auditu, které nepoužívají definici Managed disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Tato definice zásady identifikuje prostředky, které nesplňují podmínky nastavené v definici zásady.

Spuštěním následujícího příkazu vytvořte přiřazení zásady:

   - Identifikátor URI v REST API

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - Text požadavku

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
       }
     }
     ```

Předchozí koncový bod a tělo požadavku používají následující informace:

REST API IDENTIFIKÁTOR URI:
- **Scope** – Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může být v rozsahu od skupiny pro správu k individuálnímu prostředku. Nezapomeňte nahradit `{scope}` jedním z následujících způsobů:
  - Skupina pro správu: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Formě `/subscriptions/{subscriptionId}`
  - Skupina prostředků: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Partner `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **Name** – skutečný název přiřazení. V tomto příkladu je použitý název _audit-vm-manageddisks_.

Text žádosti:
- **DisplayName** – zobrazovaný název přiřazení zásady. V takovém případě použijete _přiřazení audit virtuálních počítačů bez spravovaných disků_.
- **Popis** – podrobnější vysvětlení toho, co zásada dělá nebo proč je přiřazena tomuto oboru.
- **policyDefinitionId** – ID definice zásady, na základě kterého používáte k vytvoření přiřazení. V tomto případě se jedná o ID _virtuálních počítačů auditu definice zásad, které nepoužívají spravované disky_.

## <a name="identify-non-compliant-resources"></a>Zjištění nevyhovujících prostředků

Pokud chcete zobrazit prostředky, které nedodržují předpisy v rámci tohoto nového přiřazení, spusťte následující příkaz, který načte ID prostředků nekompatibilních prostředků, které jsou ve formátu JSON:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
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
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

Tyto výsledky jsou srovnatelné s tím, co se obvykle zobrazuje v části **Nekompatibilní prostředky** v zobrazení na webu Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat vytvořené přiřazení, použijte následující příkaz:

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

Nahraďte `{scope}` rozsahem, který jste použili při prvním vytvoření přiřazení zásady.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazování zásad k ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)
