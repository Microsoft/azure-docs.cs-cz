---
title: Oprava poškozeného účtu Azure automanage
description: Informace o tom, jak opravit poškozený účet automatické správy
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: ad54b37da8a4945162b507232f33083890ec1fff
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557674"
---
# <a name="repair-a-broken-automanage-account"></a>Opravit poškozený účet automanage
Účet pro automatické [spravování](./automanage-virtual-machines.md#automanage-account) je kontext zabezpečení nebo identita, pod kterou dojde k automatickým operacím. Pokud jste nedávno přesunuli předplatné obsahující účet pro správu do nového tenanta, budete muset znovu nakonfigurovat svůj účet pro správu. Pokud chcete znovu nakonfigurovat svůj účet pro správu, budete muset resetovat typ identity a přiřadit příslušné role pro tento účet.

## <a name="step-1-reset-automanage-account-identity-type"></a>Krok 1: resetování typu identity účtu pro správu
Obnovte typ identity účtu automanage pomocí šablony Azure Resource Manager (ARM) níže. Uložte soubor místně jako `armdeploy.json` nebo podobný. Poznamenejte si název a umístění účtu automanage, protože tyto parametry jsou v šabloně ARM povinné.

1. Vytvořte nové nasazení ARM pomocí níže uvedené šablony a použijte `identityType = None`
    * Můžete to provést pomocí Azure CLI pomocí `az deployment sub create` . Přečtěte si další informace o `az deployment sub` příkazu. [here](https://docs.microsoft.com/cli/azure/deployment/sub)
    * Můžete to také provést pomocí PowerShellu pomocí `New-AzDeployment` modulu. Další informace o `New AzDeployment` modulu najdete [tady](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment).

1. Spustit stejnou šablonu ARM znovu s `identityType = SystemAssigned`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Krok 2: přiřazení odpovídajících rolí účtu pro autosprávu
Účet pro správu automanage vyžaduje role přispěvatele a přispěvatele zásad prostředků v předplatném, které obsahují virtuální počítače, které spravuje spravovaná Správa. Tyto role můžete přiřadit pomocí Azure Portal, šablon ARM nebo rozhraní příkazového řádku Azure CLI.

Pokud používáte šablonu ARM nebo rozhraní příkazového řádku Azure CLI, budete potřebovat hlavní ID (označované také jako ID objektu) vašeho účtu automanage (to není nutné, pokud používáte Azure Portal). ID objektu zabezpečení (ID objektu) účtu pro správu můžete najít pomocí následujících metod:

- [Azure CLI](https://docs.microsoft.com/cli/azure/ad/sp): použijte příkaz `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: přejděte do **Azure Active Directory** a vyhledejte účet automanage podle názvu. V části **podnikové aplikace** při zobrazení vyberte název účtu pro správu.

### <a name="azure-portal"></a>portál Azure
1. V části **předplatná** přejděte k předplatnému, které obsahuje vaše spravované virtuální počítače.
1. Přejděte na **řízení přístupu (IAM)**.
1. Klikněte na **Přidat přiřazení rolí**.
1. Vyberte roli **Přispěvatel** a zadejte název svého účtu pro správu vašich rolí.
1. Stiskněte **Uložit**.
1. Opakujte kroky 3-5, tentokrát s rolí **Přispěvatel zásad prostředků** .

### <a name="arm-template"></a>Šablona ARM
Spusťte následující šablonu ARM. Budete potřebovat hlavní ID vašeho účtu automatického spravování – kroky pro získání ID objektu zabezpečení výše. Po zobrazení výzvy zadejte.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
Spusťte následující příkazy:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Contributor" --scope /subscriptions/<your subscription id>

az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription id>
```

## <a name="next-steps"></a>Další kroky
Další informace o Azure automanage [najdete tady](./automanage-virtual-machines.md).
