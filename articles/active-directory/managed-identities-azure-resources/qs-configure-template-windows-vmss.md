---
title: Konfigurace šablony pro použití spravovaných identit ve škálovacích sadách virtuálních strojů – Azure AD
description: Podrobné pokyny pro konfiguraci spravovaných identit pro prostředky Azure na škálovací sadě virtuálních strojů pomocí šablony Azure Resource Manager.
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
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5e324ea20b2ea82fac5b5132893d3558bd3b41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425557"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Konfigurace spravovaných identit pro prostředky Azure na škálování virtuálních strojů Azure pomocí šablony

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, bez pověření ve vašem kódu.

V tomto článku se dozvíte, jak provádět následující spravované identity pro operace prostředků Azure na škálovací sadě virtuálních strojů Azure pomocí šablony nasazení Azure Resource Manageru:
- Povolení a zakázání spravované identity přiřazené systému na škálovací sadě virtuálních strojů Azure
- Přidání a odebrání spravované identity přiřazené uživateli ve škálovací sadě virtuálních strojů Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- K provedení operací správy v tomto článku váš účet potřebuje následující přiřazení řízení přístupu azure na základě rolí:

    > [!NOTE]
    > Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

    - [Přispěvatel virtuálního počítače](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) pro vytvoření škálovací sady virtuálních strojů a povolení a odebrání spravované identity systému nebo uživatelem přiřazené ze škálovací sady virtuálních strojů.
    - [Role spravovaného přispěvatele identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) k vytvoření spravované identity přiřazené uživateli.
    - Role [spravovaného operátora identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pro přiřazení a odebrání spravované identity přiřazené uživateli z škálovací sady virtuálních strojů a do ní.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Stejně jako u webu Azure Portal a skriptování poskytují šablony [Azure Resource Manageru](../../azure-resource-manager/management/overview.md) možnost nasazovat nové nebo upravené prostředky definované skupinou prostředků Azure. Pro úpravy a nasazení šablon je k dispozici několik možností, a to jak místní, tak na portálu, včetně:

   - Použití [vlastní šablony z Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), která umožňuje vytvořit šablonu od začátku nebo ji založit na existující společné nebo rychlé počáteční [šabloně](https://azure.microsoft.com/documentation/templates/).
   - Odvození z existující skupiny prostředků exportem šablony z [původního nasazení](../../azure-resource-manager/templates/export-template-portal.md)nebo z [aktuálního stavu nasazení](../../azure-resource-manager/templates/export-template-portal.md).
   - Pomocí místního [editoru JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a potom nahrávání a nasazování pomocí prostředí PowerShell nebo CLI.
   - Použití projektu Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) k vytvoření a nasazení šablony.  

Bez ohledu na možnost, kterou zvolíte, syntaxe šablony je stejná během počátečního nasazení a opětovného nasazení. Povolení spravovaných identit pro prostředky Azure na novém nebo existujícím virtuálním počítači se provádí stejným způsobem. Ve výchozím nastavení azure resource manager také provádí [přírůstkovou aktualizaci](../../azure-resource-manager/templates/deployment-modes.md) nasazení.

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části povolíte a zakážete systémově přiřazenou spravovanou identitu pomocí šablony Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Povolení spravované identity přiřazené systémem při vytváření škálovací sady virtuálních počítačů nebo existující škálovací sady virtuálních počítačů

1. Ať už se k Azure přihlásíte místně nebo přes portál Azure, použijte účet, který je přidružený k předplatnému Azure, které obsahuje škálovací sadu virtuálních strojů.
2. Chcete-li povolit spravovanou identitu přiřazenou systémem, `Microsoft.Compute/virtualMachinesScaleSets` načtěte šablonu do `identity` editoru, vyhledejte `"type": "Microsoft.Compute/virtualMachinesScaleSets"` zdroj zájmu v části prostředky a přidejte vlastnost na stejné úrovni jako vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Volitelně můžete zřídit spravované identity pro rozšíření škálovací sady prostředků `extensionProfile` Azure prostředků virtuálního počítače zadáním v elementu šablony. Tento krok je volitelný, protože můžete použít koncový bod identity služby Azure Instance Metadata (IMDS), abyste také načetli tokeny.  Další informace najdete [v tématu Migrace z rozšíření virtuálního počítače do Azure IMDS pro ověřování](howto-migrate-vm-extension.md).


4. Po dokončení byste měli do části zdrojů šablony přidat následující části a měly by se podobat následujícímu:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázání spravované identity přiřazené systémem ze škálovací sady virtuálních strojů Azure

Pokud máte škálovací sadu virtuálních strojů, která už nepotřebuje spravovanou identitu přiřazenou systémem:

1. Ať už se k Azure přihlásíte místně nebo přes portál Azure, použijte účet, který je přidružený k předplatnému Azure, které obsahuje škálovací sadu virtuálních strojů.

2. Načtěte šablonu do `Microsoft.Compute/virtualMachineScaleSets` [editoru](#azure-resource-manager-templates) a `resources` vyhledejte zdroj zájmu v části. Pokud máte virtuální hod, který má jenom systémově přiřazenou spravovanou `None`identitu, můžete ho zakázat změnou typu identity na .

   **Microsoft.Compute/virtualMachineScaleSets API verze 2018-06-01**

   Pokud vaše apiVersion je `2018-06-01` a váš virtuální počítač má systém `SystemAssigned` ové i uživatelem `UserAssigned` přiřazené spravované identity, odeberte z typu identity a podržte spolu s hodnotami slovníku userAssignedIdentities.

   **Microsoft.Compute/virtualMachineScaleSets API verze 2018-06-01**

   Pokud vaše `2017-12-01` apiVersion je a škálovací sada virtuálního počítače má `SystemAssigned` systémové i uživatelem `UserAssigned` přiřazené `identityIds` spravované identity, odeberte z typu identity a pokračujte spolu s polem spravovaných identit přiřazených uživatelem.



   Následující příklad ukazuje, jak odebrat systémem přiřazenou spravovanou identitu ze škálovací sady virtuálních strojů bez spravovaných identit přiřazených uživatelem:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části přiřadíte uživatelem přiřazenou spravovanou identitu k škálovací sadě virtuálních strojů pomocí šablony Azure Resource Manager.

> [!Note]
> Pokud chcete vytvořit spravovanou identitu přiřazenou uživateli pomocí šablony Správce prostředků Azure, přečtěte si informace [o vytvoření spravované identity přiřazené uživateli](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživateli k škálovací sadě virtuálních strojů

1. Pod `resources` element přidejte následující položku a přiřaďte uživatelem přiřazenou spravovanou identitu k škálovací sadě virtuálních strojů.  Nezapomeňte nahradit `<USERASSIGNEDIDENTITY>` názvem uživatelem přiřazené spravované identity, kterou jste vytvořili.

   **Microsoft.Compute/virtualMachineScaleSets API verze 2018-06-01**

   Pokud vaše `2018-06-01`apiVersion je , vaše uživatelem přiřazené spravované identity jsou uloženy ve `userAssignedIdentities` formátu slovníku `<USERASSIGNEDIDENTITYNAME>` a hodnota musí být uloženy v proměnné definované v `variables` části šablony.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API verze 2017-12-01**

   Pokud `apiVersion` je `2017-12-01` nebo starší, jsou spravované identity přiřazené uživatelem uloženy v `identityIds` poli a `<USERASSIGNEDIDENTITYNAME>` hodnota musí být uložena v proměnné definované v části proměnné šablony.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ```
> [!NOTE]
> Volitelně můžete zřídit spravované identity pro rozšíření škálovací sady prostředků `extensionProfile` Azure prostředků virtuálního počítače zadáním v elementu šablony. Tento krok je volitelný, protože můžete použít koncový bod identity služby Azure Instance Metadata (IMDS), abyste také načetli tokeny.  Další informace najdete [v tématu Migrace z rozšíření virtuálního počítače do Azure IMDS pro ověřování](howto-migrate-vm-extension.md).

3. Po dokončení by měla šablona vypadat podobně jako následující:

   **Microsoft.Compute/virtualMachineScaleSets API verze 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API verze 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Odebrání spravované identity přiřazené uživateli ze škálovací sady virtuálních strojů Azure

Pokud máte škálovací sadu virtuálních strojů, která už nepotřebuje spravovanou identitu přiřazenou uživatelem:

1. Ať už se k Azure přihlásíte místně nebo přes portál Azure, použijte účet, který je přidružený k předplatnému Azure, které obsahuje škálovací sadu virtuálních strojů.

2. Načtěte šablonu do `Microsoft.Compute/virtualMachineScaleSets` [editoru](#azure-resource-manager-templates) a `resources` vyhledejte zdroj zájmu v části. Pokud máte škálovací sadu virtuálních strojů, která má jenom přiřazenou spravovanou `None`identitu uživatelem, můžete ji zakázat změnou typu identity na .

   Následující příklad ukazuje, jak odebrat všechny uživatelem přiřazené spravované identity z virtuálního virtuálního uživatele bez spravovaných identit přiřazených systémem:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft.Compute/virtualMachineScaleSets API verze 2018-06-01**

   Chcete-li odebrat spravovanou identitu přiřazenou jedinému uživateli ze škálovací sady virtuálních strojů, odeberte ji ze slovníku. `userAssignedIdentities`

   Pokud máte systémem přiřazenou identitu, uchovávejte ji v hodnotě `type` pod hodnotou. `identity`

   **Microsoft.Compute/virtualMachineScaleSets API verze 2017-12-01**

   Chcete-li odebrat spravovanou identitu přiřazenou jedinému uživateli ze škálovací sady virtuálních strojů, odeberte ji z `identityIds` pole.

   Pokud máte systémem přiřazenou spravovanou identitu, `type` uchovávejte ji v hodnotě pod hodnotou. `identity`

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md).
