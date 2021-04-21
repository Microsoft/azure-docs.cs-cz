---
title: Oprava poškozeného účtu Azure automanage
description: Pokud jste nedávno přesunuli předplatné, které obsahuje účet pro samoobslužné správu pro nového tenanta, budete ho muset znovu nakonfigurovat. V tomto článku se dozvíte, jak.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e6bf5404a33e0b4e57c2ff8d82d8791eda3d0f06
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834187"
---
# <a name="repair-an-automanage-account"></a>Opravit účet pro autosprávu
Vaším [účtem Azure automanage](./automanage-virtual-machines.md#automanage-account) je kontext zabezpečení nebo identita, pod kterou se automatizované operace vyskytují. Pokud jste nedávno přesunuli předplatné, které obsahuje účet pro autosprávu pro nového tenanta, je nutné znovu nakonfigurovat tento účet. Pokud ho chcete znovu nakonfigurovat, musíte obnovit typ identity a přiřadit příslušné role pro tento účet.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>Krok 1: resetování typu identity účtu s možností automanage
Obnovte typ identity účtu automanage pomocí následující šablony Azure Resource Manager (ARM). Uložte soubor místně jako armdeploy.jsnebo podobný název. Poznamenejte si název a umístění účtu vaší automanage, protože se jedná o požadované parametry v šabloně ARM.

1. Pomocí následující šablony vytvořte nasazení Správce prostředků. Použijte `identityType = None`.
    * Nasazení můžete vytvořit v rozhraní příkazového řádku Azure pomocí `az deployment sub create` . Další informace najdete v tématu [AZ Deployment sub](/cli/azure/deployment/sub).
    * Nasazení můžete vytvořit v PowerShellu pomocí `New-AzDeployment` modulu. Další informace najdete v tématu [New-AzDeployment](/powershell/module/az.resources/new-azdeployment).

1. Spusťte stejnou šablonu ARM znovu s `identityType = SystemAssigned` .

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
Účet pro správu automanage vyžaduje role přispěvatele a zásad prostředků přispěvatele v předplatném, které obsahuje virtuální počítače, které spravuje spravovaná Správa. Tyto role můžete přiřadit pomocí Azure Portal, šablon ARM nebo rozhraní příkazového řádku Azure CLI.

Pokud používáte šablonu ARM nebo rozhraní příkazového řádku Azure CLI, budete potřebovat hlavní ID (označované také jako ID objektu) vašeho účtu automanage. (Toto ID nebudete potřebovat, pokud používáte Azure Portal.) Toto ID můžete najít pomocí těchto metod:

- [Azure CLI](/cli/azure/ad/sp): použijte příkaz `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: Přejít na **Azure Active Directory** a vyhledat účet automanage podle názvu. V části **podnikové aplikace** vyberte při zobrazení název účtu pro správu znovu.

### <a name="azure-portal"></a>portál Azure
1. V části **předplatná** vyberte předplatné, které obsahuje vaše spravované virtuální počítače.
1. Přejděte na **řízení přístupu (IAM)**.
1. Vyberte **Přidat přiřazení rolí**.
1. Vyberte roli **Přispěvatel** a zadejte název vašeho účtu pro správu.
1. Vyberte **Uložit**.
1. Opakujte kroky 3 až 5 a tentokrát s rolí **Přispěvatel zásad prostředků** .

### <a name="arm-template"></a>Šablona ARM
Spusťte následující šablonu ARM. Budete potřebovat ID objektu zabezpečení vašeho účtu pro správu. Na začátku této části je postup, jak ho získat. Až budete vyzváni, zadejte ID.

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
Spusťte tyto příkazy:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>Další kroky
[Další informace o službě Azure automanage](./automanage-virtual-machines.md)
