---
title: 'Úvodní příručka: Nové přiřazení zásad pomocí azure CLI'
description: V tomto rychlém startu pomocí rozhraní příkazového příkazu Azure vytvoříte přiřazení zásad Azure k identifikaci nekompatibilních prostředků.
ms.date: 01/11/2020
ms.topic: quickstart
ms.openlocfilehash: 7f76191d97a936c745fc2b13b54011e787e0b5e6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75978327"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Úvodní příručka: Vytvoření přiřazení zásad k identifikaci nekompatibilních prostředků pomocí rozhraní příkazového příkazu Azure

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. _Neodpovídají_ přiřazení zásad.

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V této příručce se Azure CLI používá k vytvoření přiřazení zásady a identifikaci prostředků, které nedodržují předpisy, v prostředí Azure.

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

- Tento rychlý start vyžaduje spuštění azure CLI verze 2.0.76 nebo novější k místní instalaci a použití příkazového příkazového příkazu. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

- Zaregistrujte poskytovatele prostředků Azure Policy Insights pomocí azure cli. Registrace poskytovatele prostředků zajistí, že s ním vaše předplatné bude fungovat. Chcete-li zaregistrovat zprostředkovatele prostředků, musíte mít oprávnění k operaci poskytovatele prostředků registru. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Další informace o registraci a zobrazení poskytovatelů prostředků najdete v tématu [Poskytovatelé a typy prostředků](../../azure-resource-manager/management/resource-providers-and-types.md).

- Pokud jste to ještě neudělali, nainstalujte si nástroj [ARMClient](https://github.com/projectkudu/ARMClient). Jedná se o nástroj, který posílá žádosti HTTPS do rozhraní API založených na Azure Resource Manageru.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte definici **Auditovat virtuální počítače, které nepoužívají spravované disky**. Tato definice zásad identifikuje prostředky, které nejsou kompatibilní s podmínkami nastavené v definici zásady.

Spuštěním následujícího příkazu vytvořte přiřazení zásady:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

Předchozí příkaz používá následující informace:

- **Name** – skutečný název přiřazení. V tomto příkladu je použitý název _audit-vm-manageddisks_.
- **DisplayName** – zobrazovaný název přiřazení zásady. V takovém případě používáte _auditovat virtuální počítače bez přiřazení spravovaných disků_.
- **Policy** – ID definice zásady, kterou používáte k vytvoření tohoto přiřazení. V tomto případě je to ID definice zásad _auditní virtuální počítače, které nepoužívají spravované disky_. ID definice zásady získáte spuštěním příkazu `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`.
- **Scope** – Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupinu prostředků. Nezapomeňte nahradit &lt;scope&gt; názvem vaší skupiny prostředků.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Pokud chcete zobrazit prostředky, které nedodržují předpisy v rámci tohoto nového přiřazení, získejte ID přiřazení zásady spuštěním následujících příkazů:

```azurecli-interactive
az policy assignment list --query "[?displayName=='Audit VMs without managed disks Assignment'].id"
```

Další informace o ID přiřazení zásad naleznete [v tématu přiřazení zásad az](/cli/azure/policy/assignment).

Pak spuštěním následujícího příkazu získejte a zapište do souboru JSON ID prostředků, které nedodržují předpisy:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-09-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
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

Chcete-li odebrat vytvořenou úlohu, použijte následující příkaz:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazení zásad k ověření, že nové prostředky jsou kompatibilní, pokračujte v kurzu pro:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)