---
title: Postup konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablony
description: Podrobné pokyny pro konfiguraci spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablony Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43f5e04440f55c44a53b85aa4d3600e0d926424d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330005"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablon

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se pomocí šablony nasazení Azure Resource Manager dozvíte, jak provádět následující spravované identity pro operace prostředků Azure na virtuálním počítači Azure:

## <a name="prerequisites"></a>Předpoklady

- Pokud nejste obeznámeni s používáním šablony nasazení Azure Resource Manager, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#how-does-it-work)** .
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Stejně jako u Azure Portal a skriptování poskytují [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) šablony možnost nasazení nových nebo upravených prostředků definovaných skupinou prostředků Azure. K dispozici je několik možností pro úpravu a nasazení šablony, včetně místních i na portálu, včetně:

   - Použití [vlastní šablony z Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), což vám umožňuje vytvořit zcela novou šablonu, nebo ji založit na stávající společné nebo [rychlé šabloně](https://azure.microsoft.com/documentation/templates/).
   - Odvození z existující skupiny prostředků exportováním šablony z [původního nasazení](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)nebo z [aktuálního stavu nasazení](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
   - Použití místního [editoru JSON (například vs Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a následného nahrávání a nasazování pomocí PowerShellu nebo rozhraní příkazového řádku.
   - Použití [projektu skupiny prostředků Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) sady Visual Studio k vytvoření a nasazení šablony.  

Bez ohledu na zvolenou možnost je syntaxe šablony stejná při počátečním nasazení a opětovném nasazení. Povolení systému nebo uživatelsky přiřazené spravované identity na novém nebo existujícím virtuálním počítači se provádí stejným způsobem. Ve výchozím nastavení Azure Resource Manager také [přírůstkovou aktualizaci](../../azure-resource-manager/deployment-modes.md) nasazení.

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části povolíte a zakážete spravovanou identitu přiřazenou systémem pomocí šablony Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Povolit spravovanou identitu přiřazenou systémem během vytváření virtuálního počítače Azure nebo na existujícím virtuálním počítači

Aby se na virtuálním počítači povolila spravovaná identita přiřazená systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Bez ohledu na to, jestli se k Azure přihlašujete místně nebo prostřednictvím Azure Portal, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

2. Pokud chcete povolit spravovanou identitu přiřazenou systémem, načtěte šablonu do editoru, vyhledejte v části `resources` prostředek `Microsoft.Compute/virtualMachines` a přidejte vlastnost `"identity"` na stejnou úroveň jako vlastnost `"type": "Microsoft.Compute/virtualMachines"`. Použijte následující syntaxi:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   },
   ```



3. Až budete hotovi, v části `resource` v šabloně by se měly přidat následující oddíly, které by měly vypadat takto:

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
                },
            },
        
            //The following appears only if you provisioned the optional VM extension (to be deprecated)
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Přiřaďte roli spravovanou identitu přiřazenou systémem virtuálního počítače.

Až na svém VIRTUÁLNÍm počítači povolíte spravovanou identitu přiřazenou systémem, můžete jí udělit roli, jako je **Čtenář** přístupu ke skupině prostředků, ve které se vytvořila.

Pokud chcete přiřadit roli k identitě přiřazené systému vašeho virtuálního počítače, váš účet potřebuje přiřazení role [Správce přístupu uživatele](/azure/role-based-access-control/built-in-roles#user-access-administrator) .

1. Bez ohledu na to, jestli se k Azure přihlašujete místně nebo prostřednictvím Azure Portal, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.
 
2. Načtěte šablonu do [editoru](#azure-resource-manager-templates) a přidejte následující informace, které **vašemu virtuálnímu počítači umožní** přístup ke skupině prostředků, ve které se vytvořila.  Struktura šablony se může lišit v závislosti na editoru a modelu nasazení, který zvolíte.
   
   V části `parameters` přidejte následující:

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    V části `variables` přidejte následující:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    V části `resources` přidejte následující:

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

Pokud chcete z virtuálního počítače odebrat spravovanou identitu přiřazenou systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Bez ohledu na to, jestli se k Azure přihlašujete místně nebo prostřednictvím Azure Portal, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

2. Načtěte šablonu do [editoru](#azure-resource-manager-templates) a v části `resources` vyhledejte prostředek `Microsoft.Compute/virtualMachines` s zajímavou částí. Pokud máte virtuální počítač, který má jenom spravovanou identitu přiřazenou systémem, můžete ho zakázat změnou typu identity na `None`.  
   
   **Microsoft. COMPUTE/virtualMachines API verze 2018-06-01**

   Pokud má váš virtuální počítač i spravované identity přiřazené systémem a uživatelem, odeberte z typu identity `SystemAssigned` a zachovejte `UserAssigned` společně s hodnotami slovníků `userAssignedIdentities`.

   **Microsoft. COMPUTE/virtualMachines API verze 2018-06-01**
   
   Pokud je vaše `apiVersion` `2017-12-01` a váš virtuální počítač obsahuje spravované identity systémem i uživatelem, odeberte `SystemAssigned` z typu identity a zachovejte `UserAssigned` společně s polem `identityIds` u uživatelsky přiřazených spravovaných identit.  
   
Následující příklad ukazuje, jak odebrat spravovanou identitu přiřazenou systémem z virtuálního počítače bez přiřazených uživatelem spravovaných identit:

```JSON
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

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části pomocí šablony Azure Resource Manager přiřadíte k virtuálnímu počítači Azure spravovanou identitu s přiřazenou uživatelem.

> [!Note]
> Chcete-li vytvořit uživatelem přiřazenou identitu pomocí šablony Azure Resource Manager, přečtěte si téma [Vytvoření uživatelem přiřazené spravované identity](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Přiřazení spravované identity přiřazené uživateli k virtuálnímu počítači Azure

K přiřazení uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači potřebuje váš účet [přispěvatele virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a přiřazení rolí [spravovaného operátoru identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. V rámci elementu `resources` přidejte následující položku, která uživateli přiřadí spravovanou identitu přiřazenou k vašemu VIRTUÁLNÍmu počítači.  Nezapomeňte nahradit `<USERASSIGNEDIDENTITY>` názvem uživatelsky přiřazené spravované identity, kterou jste vytvořili.

   **Microsoft. COMPUTE/virtualMachines API verze 2018-06-01**

   Pokud je vaše `apiVersion` `2018-06-01`, budou spravované identity přiřazené uživatelem uloženy ve formátu slovníku `userAssignedIdentities` a hodnota `<USERASSIGNEDIDENTITYNAME>` musí být uložena v proměnné definované v části `variables` vaší šablony.

   ```json
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
    
   Pokud je vaše `apiVersion` `2017-12-01`, budou spravované identity přiřazené uživatelem uloženy v poli `identityIds` a hodnota `<USERASSIGNEDIDENTITYNAME>` musí být uložena v proměnné definované v části `variables` vaší šablony.
    
   ```json
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
       
3. Až budete hotovi, v části `resource` v šabloně by se měly přidat následující oddíly, které by měly vypadat takto:
   
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
        },
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                  
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
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
        },
                 
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                   
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
            }
        }
       }
    ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání spravované identity přiřazené uživatelem z virtuálního počítače Azure

K odebrání uživatelsky přiřazené identity z virtuálního počítače potřebuje váš účet přiřazení role [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Bez ohledu na to, jestli se k Azure přihlašujete místně nebo prostřednictvím Azure Portal, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

2. Načtěte šablonu do [editoru](#azure-resource-manager-templates) a v části `resources` vyhledejte prostředek `Microsoft.Compute/virtualMachines` s zajímavou částí. Pokud máte virtuální počítač, který má pouze spravovanou identitu přiřazenou uživatelem, můžete ho zakázat změnou typu identity na `None`.
 
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
    
   Pokud chcete z virtuálního počítače odebrat jednu spravovanou identitu přiřazenou uživatelem, odeberte ji ze slovníku `useraAssignedIdentities`.

   Pokud máte spravovanou identitu přiřazenou systémem, zachovejte ji v hodnotě `type` pod hodnotou `identity`.
 
   **Microsoft. COMPUTE/virtualMachines API verze 2017-12-01**

   Pokud chcete z virtuálního počítače odebrat jednu spravovanou identitu přiřazenou uživatelem, odeberte ji z pole `identityIds`.

   Pokud máte spravovanou identitu přiřazenou systémem, zachovejte ji v hodnotě `type` pod hodnotou `identity`.
   
## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)

