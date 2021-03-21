---
title: Konfigurace spravovaných identit na virtuálním počítači Azure pomocí šablony – Azure AD
description: Podrobné pokyny pro konfiguraci spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablony Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4948e17d1e0e782a8fa18c3eb5a2185e816a459a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631400"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablon

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu.

V tomto článku se pomocí šablony nasazení Azure Resource Manager dozvíte, jak provádět následující spravované identity pro operace prostředků Azure na virtuálním počítači Azure:

## <a name="prerequisites"></a>Předpoklady

- Pokud nejste obeznámeni s používáním šablony nasazení Azure Resource Manager, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#managed-identity-types)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Stejně jako u Azure Portal a skriptování poskytují [Azure Resource Manager](../../azure-resource-manager/management/overview.md) šablony možnost nasazení nových nebo upravených prostředků definovaných skupinou prostředků Azure. K dispozici je několik možností pro úpravu a nasazení šablony, včetně místních i na portálu, včetně:

   - Použití [vlastní šablony z Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), což vám umožňuje vytvořit zcela novou šablonu, nebo ji založit na stávající společné nebo [rychlé šabloně](https://azure.microsoft.com/documentation/templates/).
   - Odvození z existující skupiny prostředků exportováním šablony z [původního nasazení](../../azure-resource-manager/templates/export-template-portal.md)nebo z [aktuálního stavu nasazení](../../azure-resource-manager/templates/export-template-portal.md).
   - Použití místního [editoru JSON (například vs Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)a následného nahrávání a nasazování pomocí PowerShellu nebo rozhraní příkazového řádku.
   - Použití [projektu skupiny prostředků Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) sady Visual Studio k vytvoření a nasazení šablony.  

Bez ohledu na zvolenou možnost je syntaxe šablony stejná při počátečním nasazení a opětovném nasazení. Povolení systému nebo uživatelsky přiřazené spravované identity na novém nebo existujícím virtuálním počítači se provádí stejným způsobem. Ve výchozím nastavení Azure Resource Manager také [přírůstkovou aktualizaci](../../azure-resource-manager/templates/deployment-modes.md) nasazení.

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části povolíte a zakážete spravovanou identitu přiřazenou systémem pomocí šablony Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Povolit spravovanou identitu přiřazenou systémem během vytváření virtuálního počítače Azure nebo na existujícím virtuálním počítači

Aby se na virtuálním počítači povolila spravovaná identita přiřazená systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Bez ohledu na to, jestli se k Azure přihlašujete místně nebo prostřednictvím Azure Portal, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

2. Pokud chcete povolit spravovanou identitu přiřazenou systémem, načtěte šablonu do editoru, vyhledejte `Microsoft.Compute/virtualMachines` prostředek zájmu v rámci `resources` oddílu a přidejte `"identity"` vlastnost na stejnou úroveň jako `"type": "Microsoft.Compute/virtualMachines"` vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Až budete hotovi, v části šablony by se měly přidat následující oddíly, které `resource` by měly vypadat takto:

   ```JSON
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Přiřaďte roli spravovanou identitu přiřazenou systémem virtuálního počítače.

Až na svém VIRTUÁLNÍm počítači povolíte spravovanou identitu přiřazenou systémem, můžete jí udělit roli, jako je **Čtenář** přístupu ke skupině prostředků, ve které se vytvořila.

Pokud chcete přiřadit roli k identitě přiřazené systému vašeho virtuálního počítače, váš účet potřebuje přiřazení role [Správce přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator) .

1. Bez ohledu na to, jestli se k Azure přihlašujete místně nebo prostřednictvím Azure Portal, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

2. Načtěte šablonu do [editoru](#azure-resource-manager-templates) a přidejte následující informace, které **vašemu virtuálnímu počítači umožní** přístup ke skupině prostředků, ve které se vytvořila.  Struktura šablony se může lišit v závislosti na editoru a modelu nasazení, který zvolíte.

   V `parameters` části přidejte následující:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    V `variables` části přidejte následující:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    V `resources` části přidejte následující:

    ```JSON
    {
        "apiVersion": "2017-09-01",
        "type": "Microsoft.Authorization/roleAssignments",
        "name": "[parameters('rbacGuid')]",
        "properties": {
            "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
            "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
            "scope": "[resourceGroup().id]"
        },
         "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
        ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Zakázání spravované identity přiřazené systémem z virtuálního počítače Azure

Pokud chcete z virtuálního počítače odebrat spravovanou identitu přiřazenou systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Bez ohledu na to, jestli se k Azure přihlašujete místně nebo prostřednictvím Azure Portal, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

2. Načtěte šablonu do [editoru](#azure-resource-manager-templates) a vyhledejte `Microsoft.Compute/virtualMachines` prostředek zájmu v rámci `resources` oddílu. Pokud máte virtuální počítač, který má pouze spravovanou identitu přiřazenou systémem, můžete ho zakázat změnou typu identity na `None` .  

   **Microsoft. COMPUTE/virtualMachines API verze 2018-06-01**

   Pokud má váš virtuální počítač i spravované identity přiřazené systémem i uživatelem, odeberte `SystemAssigned` z typu identity a zachovejte `UserAssigned` spolu s `userAssignedIdentities` hodnotami slovníku.

   **Microsoft. COMPUTE/virtualMachines API verze 2018-06-01**

   Pokud máte `apiVersion` `2017-12-01` a váš virtuální počítač obsahuje spravované identity systémem i uživatelem, odeberte `SystemAssigned` z typu identity a zachovejte `UserAssigned` spolu s `identityIds` polem spravovaných identit přiřazených uživatelem.  

Následující příklad ukazuje, jak odebrat spravovanou identitu přiřazenou systémem z virtuálního počítače bez spravovaných identit uživatelem:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části pomocí šablony Azure Resource Manager přiřadíte k virtuálnímu počítači Azure spravovanou identitu s přiřazenou uživatelem.

> [!Note]
> Chcete-li vytvořit uživatelem přiřazenou identitu pomocí šablony Azure Resource Manager, přečtěte si téma [Vytvoření uživatelem přiřazené spravované identity](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Přiřazení spravované identity přiřazené uživateli k virtuálnímu počítači Azure

K přiřazení uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači potřebuje váš účet [přispěvatele virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a přiřazení rolí [spravovaného operátoru identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. V rámci `resources` elementu přidejte následující položku, která uživateli přiřadí spravovanou identitu přiřazenou k vašemu virtuálnímu počítači.  Nezapomeňte nahradit `<USERASSIGNEDIDENTITY>` názvem uživatelsky přiřazené spravované identity, kterou jste vytvořili.

   **Microsoft. COMPUTE/virtualMachines API verze 2018-06-01**

   V takovém případě `apiVersion` `2018-06-01` jsou spravované identity přiřazené uživatelem uloženy ve `userAssignedIdentities` formátu slovníku a `<USERASSIGNEDIDENTITYNAME>` hodnota musí být uložena v proměnné definované v `variables` části šablony.

   ```JSON
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Microsoft. COMPUTE/virtualMachines API verze 2017-12-01**

   V takovém případě `apiVersion` `2017-12-01` jsou spravované identity přiřazené uživatelem uloženy v `identityIds` poli a `<USERASSIGNEDIDENTITYNAME>` hodnota musí být uložena v proměnné definované v `variables` části šablony.

   ```JSON
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. Až budete hotovi, v části šablony by se měly přidat následující oddíly, které `resource` by měly vypadat takto:

   **Microsoft. COMPUTE/virtualMachines API verze 2018-06-01**    

   ```JSON
     "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        }
    ] 
   ```
   **Microsoft. COMPUTE/virtualMachines API verze 2017-12-01**

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        }
   ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání spravované identity přiřazené uživatelem z virtuálního počítače Azure

K odebrání uživatelsky přiřazené identity z virtuálního počítače potřebuje váš účet přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Bez ohledu na to, jestli se k Azure přihlašujete místně nebo prostřednictvím Azure Portal, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

2. Načtěte šablonu do [editoru](#azure-resource-manager-templates) a vyhledejte `Microsoft.Compute/virtualMachines` prostředek zájmu v rámci `resources` oddílu. Pokud máte virtuální počítač, který má pouze spravovanou identitu přiřazenou uživatelem, můžete ho zakázat změnou typu identity na `None` .

   Následující příklad ukazuje, jak odebrat všechny spravované identity přiřazené uživatelem z virtuálního počítače bez spravovaných identit přiřazených systémem:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft. COMPUTE/virtualMachines API verze 2018-06-01**

   Pokud chcete z virtuálního počítače odebrat jednu spravovanou identitu přiřazenou uživatelem, odeberte ji ze `useraAssignedIdentities` slovníku.

   Pokud máte spravovanou identitu přiřazenou systémem, ponechte ji v hodnotě `type` pod `identity` hodnotou.

   **Microsoft. COMPUTE/virtualMachines API verze 2017-12-01**

   Pokud chcete z virtuálního počítače odebrat jednu spravovanou identitu přiřazenou uživatelem, odeberte ji z tohoto `identityIds` pole.

   Pokud máte spravovanou identitu přiřazenou systémem, ponechte ji v hodnotě `type` pod `identity` hodnotou.

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)
