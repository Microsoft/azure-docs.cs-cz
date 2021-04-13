---
title: Účet Azure automanage
description: Přečtěte si, jak účet pro autosprávu funguje a jak ho vytvořit.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alsin
ms.openlocfilehash: b79e061ae00c42ed2ec2ac39f5653a868f09a15f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368636"
---
# <a name="automanage-accounts"></a>Účty pro autosprávu

Účet pro automatické spravování je identita, kterou používá služba automanage k provádění automatizovaných operací.

V prostředí Azure Portal máte při povolování funkce automanage na vašich virtuálních počítačích pokročilý rozevírací seznam v okně **Povolit osvědčené postupy pro virtuální počítače Azure** , které vám umožní přiřadit nebo ručně vytvořit účet pro správu.

Účtu automanage budou udělena **role přispěvatelů** a **zásad prostředků přispěvatelům** k předplatným, která obsahují počítače, které chcete spravovat. Stejný účet pro správu můžete použít na počítačích v rámci více předplatných, což udělí oprávnění k autosprávě účtu **přispěvatele** a **zásad prostředků** u všech předplatných.

Pokud je váš virtuální počítač připojený k pracovnímu prostoru Log Analytics v jiném předplatném, bude mít účet pro  správu v tomto jiném předplatném taky **Přispěvatel a přispěvatel zásad prostředků** .

Pokud povolujete autosprávu pomocí nového účtu pro autosprávu, budete potřebovat následující oprávnění k vašemu předplatnému: role **vlastníka** nebo **přispěvatele** spolu s rolemi **Správce přístupu uživatele** .

Pokud povolujete možnost automanage s existujícím účtem pro správu, musíte mít roli **přispěvatele** ve skupině prostředků, která obsahuje vaše virtuální počítače.

> [!NOTE]
> Když zakážete možnosti pro automanage, budete mít i oprávnění účtu automanage u všech přidružených předplatných. Ručně odeberte oprávnění tak, že na stránce IAM předplatného odeberete nebo odstraníte účet pro správu. Účet automanage nelze odstranit, pokud stále spravuje všechny počítače.

## <a name="create-an-automanage-account"></a>Vytvoření účtu pro autosprávu
Účet pro správu můžete vytvořit pomocí portálu nebo pomocí šablony ARM.

### <a name="portal"></a>Portál
1. Přechod na okno pro **správu** v portálu
1. **V existujícím počítači** klikněte na Povolit.
1. V části **Upřesnit** klikněte na vytvořit nový účet.
1. Vyplňte požadovaná pole a klikněte na **vytvořit** .

### <a name="arm-template"></a>Šablona ARM
Vytvoření účtu pro autosprávu pomocí šablony ARM vyžaduje 2 kroky:
1. Vytvoření účtu pro správu a správu
1. Udělte účtu dostatečná oprávnění, která mu umožní provádět operace za vás.
    1. Budete potřebovat ID objektu účtu, který jste vytvořili pro tento krok.
        1. Postup vyhledání podrobností o instančním objektu vašeho účtu (včetně ID objektu) jsou k dispozici [zde](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal#view-the-service-principal).
    1. Po nalezení instančního objektu zkopírujte jeho **ID**. Uložte si ho, protože ho budete potřebovat k delegování níže uvedených oprávnění.

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. Vytvořte si účet automanage (neudělí oprávnění k tomuto účtu).
Pokud chcete vytvořit účet pro autosprávu, uložte následující šablonu ARM `azuredeploy.json` a spusťte níže uvedený příkaz Azure CLI. Až to budete mít, posuňte se k druhé šabloně níže, abyste k účtu pověřili dostatečná oprávnění.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. udělení oprávnění účtu pro správu
Chcete-li udělit dostatečná oprávnění k účtu pro správu, bude nutné provést následující akce:
1. Následující šablonu ARM uložte jako `azuredeploy2.json` a spusťte níže uvedený příkaz Azure CLI.
1. Po zobrazení výzvy zadejte ID objektu účtu pro správu, který jste vytvořili a uložili.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('contributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('resourcePolicyContributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Další kroky
* Další informace o automanage Services pro [Linux](./automanage-linux.md) a [Windows](./automanage-windows-server.md)