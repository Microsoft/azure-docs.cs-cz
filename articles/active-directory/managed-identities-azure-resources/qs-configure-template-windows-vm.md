---
title: Konfigurace spravovaných identit na virtuálním počítači Azure pomocí šablony – Azure AD
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
ms.openlocfilehash: e5540697e8e64586d73e34d253fb95e549fc0301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972151"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablon

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, bez pověření ve vašem kódu.

V tomto článku se pomocí šablony nasazení Azure Resource Manageru dozvíte, jak provádět následující spravované identity pro operace prostředků Azure na virtuálním počítači Azure:

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s použitím šablony nasazení Azure Resource Manager, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Stejně jako u webu Azure Portal a skriptování poskytují šablony [Azure Resource Manageru](../../azure-resource-manager/management/overview.md) možnost nasazovat nové nebo upravené prostředky definované skupinou prostředků Azure. Pro úpravy a nasazení šablon je k dispozici několik možností, a to jak místní, tak na portálu, včetně:

   - Použití [vlastní šablony z Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), která umožňuje vytvořit šablonu od začátku nebo ji založit na existující společné nebo rychlé počáteční [šabloně](https://azure.microsoft.com/documentation/templates/).
   - Odvození z existující skupiny prostředků exportem šablony z [původního nasazení](../../azure-resource-manager/templates/export-template-portal.md)nebo z [aktuálního stavu nasazení](../../azure-resource-manager/templates/export-template-portal.md).
   - Pomocí místního [editoru JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a potom nahrávání a nasazování pomocí prostředí PowerShell nebo CLI.
   - Použití projektu Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) k vytvoření a nasazení šablony.  

Bez ohledu na možnost, kterou zvolíte, syntaxe šablony je stejná během počátečního nasazení a opětovného nasazení. Povolení spravované identity přiřazené k systému nebo uživatelem na novém nebo existujícím virtuálním počítači se provádí stejným způsobem. Ve výchozím nastavení azure resource manager také provádí [přírůstkovou aktualizaci](../../azure-resource-manager/templates/deployment-modes.md) nasazení.

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části povolíte a zakážete spravovanou identitu přiřazenou systémem pomocí šablony Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Povolení spravované identity přiřazené systémem při vytváření virtuálního počítače Azure nebo na existujícím virtuálním počítači

Chcete-li povolit systémem přiřazenou spravovanou identitu na virtuálním počítači, váš účet potřebuje přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Ať už se k Azure přihlásíte místně nebo přes portál Azure, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

2. Chcete-li povolit spravovanou identitu přiřazenou systémem, načtěte šablonu do editoru, vyhledejte `Microsoft.Compute/virtualMachines` zdroj zájmu v rámci `resources` oddílu a přidejte `"identity"` vlastnost na stejné úrovni jako `"type": "Microsoft.Compute/virtualMachines"` vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Až budete hotovi, do `resource` části šablony by měly být přidány následující části a měly by se podobat následujícímu:

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

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Přiřazení role spravované identity přiřazené v systému virtuálního uživatele

Po povolení spravované identity přiřazené systému na vašem virtuálním počítači, můžete chtít udělit roli, jako je například přístup **čtenáře** ke skupině prostředků, ve kterém byla vytvořena.

Chcete-li přiřadit roli k systémové identitě vašeho virtuálního počítače, váš účet potřebuje přiřazení role [Správce přístupu uživatelů.](/azure/role-based-access-control/built-in-roles#user-access-administrator)

1. Ať už se k Azure přihlásíte místně nebo přes portál Azure, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

2. Načtěte šablonu do [editoru](#azure-resource-manager-templates) a přidejte následující informace, které vaší **čtečce** virtuálních zařízení poskytnou přístup ke skupině prostředků, ve které byla vytvořena.  Struktura šablony se může lišit v závislosti na editoru a modelu nasazení, který zvolíte.

   Pod `parameters` oddíl přidejte následující:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Pod `variables` oddíl přidejte následující:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Pod `resources` oddíl přidejte následující:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Zakázání spravované identity přiřazené systému z virtuálního počítače Azure

Chcete-li odebrat systémem přiřazenou spravovanou identitu z virtuálního počítače, váš účet potřebuje přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Ať už se k Azure přihlásíte místně nebo přes portál Azure, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

2. Načtěte šablonu do `Microsoft.Compute/virtualMachines` [editoru](#azure-resource-manager-templates) a `resources` vyhledejte zdroj zájmu v části. Pokud máte virtuální hod, který má jenom systémově přiřazenou spravovanou `None`identitu, můžete ho zakázat změnou typu identity na .  

   **Microsoft.Compute/virtualMachines API verze 2018-06-01**

   Pokud váš virtuální počítač má systémové i uživatelem `SystemAssigned` přiřazené spravované identity, odeberte z typu identity a udržujte `UserAssigned` spolu s hodnotami slovníku. `userAssignedIdentities`

   **Microsoft.Compute/virtualMachines API verze 2018-06-01**

   Pokud `apiVersion` váš `2017-12-01` je a váš virtuální počítač má systémové `SystemAssigned` i uživatelem přiřazené `UserAssigned` spravované `identityIds` identity, odeberte z typu identity a pokračujte spolu s polem spravovaných identit přiřazených uživatelem.  

Následující příklad ukazuje, jak odebrat systémem přiřazenou spravovanou identitu z virtuálního virtuálního uživatele bez spravovaných identit přiřazených uživatelem:

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

V této části přiřadíte uživatelem přiřazenou spravovanou identitu k virtuálnímu počítači Azure pomocí šablony Azure Resource Manager.

> [!Note]
> Pokud chcete vytvořit spravovanou identitu přiřazenou uživateli pomocí šablony Správce prostředků Azure, přečtěte si informace [o vytvoření spravované identity přiřazené uživateli](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Přiřazení spravované identity přiřazené uživateli k virtuálnímu počítači Azure

Chcete-li přiřadit uživatelem přiřazenou identitu k virtuálnímu počítači, váš účet potřebuje přiřazení rolí [Přispěvatel virtuálního počítače](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a [Operátor spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Pod `resources` element přidejte následující položku a přiřaďte k virtuálnímu počítači spravovanou identitu přiřazenou uživatelem.  Nezapomeňte nahradit `<USERASSIGNEDIDENTITY>` názvem uživatelem přiřazené spravované identity, kterou jste vytvořili.

   **Microsoft.Compute/virtualMachines API verze 2018-06-01**

   Pokud `apiVersion` je `2018-06-01`váš , jsou spravované identity `userAssignedIdentities` přiřazené uživatelem `<USERASSIGNEDIDENTITYNAME>` uloženy ve formátu slovníku `variables` a hodnota musí být uložena v proměnné definované v části šablony.

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

   **Microsoft.Compute/virtualMachines API verze 2017-12-01**

   Pokud `apiVersion` je `2017-12-01`váš , vaše uživatelem přiřazené `identityIds` spravované `<USERASSIGNEDIDENTITYNAME>` identity jsou uloženy v `variables` poli a hodnota musí být uložena v proměnné definované v části šablony.

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

3. Až budete hotovi, do `resource` části šablony by měly být přidány následující části a měly by se podobat následujícímu:

   **Microsoft.Compute/virtualMachines API verze 2018-06-01**    

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
   **Microsoft.Compute/virtualMachines API verze 2017-12-01**

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání spravované identity přiřazené uživateli z virtuálního počítače Azure

Chcete-li odebrat identitu přiřazenou uživateli z virtuálního počítače, váš účet potřebuje přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Ať už se k Azure přihlásíte místně nebo přes portál Azure, použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

2. Načtěte šablonu do `Microsoft.Compute/virtualMachines` [editoru](#azure-resource-manager-templates) a `resources` vyhledejte zdroj zájmu v části. Pokud máte virtuální hod, který má jenom uživatelem přiřazenou spravovanou `None`identitu, můžete ho zakázat změnou typu identity na .

   Následující příklad ukazuje, jak odebrat všechny uživatelem přiřazené spravované identity z virtuálního virtuálního uživatele bez spravovaných identit přiřazených systémem:

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

   **Microsoft.Compute/virtualMachines API verze 2018-06-01**

   Chcete-li odebrat spravovanou identitu přiřazenou jednomu `useraAssignedIdentities` uživateli z virtuálního uživatele, odeberte ji ze slovníku.

   Pokud máte systémem přiřazenou spravovanou identitu, `type` uchovávejte ji v hodnotě pod hodnotou. `identity`

   **Microsoft.Compute/virtualMachines API verze 2017-12-01**

   Chcete-li odebrat spravovanou identitu přiřazenou jednomu `identityIds` uživateli z virtuálního uživatele, odeberte ji z pole.

   Pokud máte systémem přiřazenou spravovanou identitu, `type` uchovávejte ji v hodnotě pod hodnotou. `identity`

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md).
