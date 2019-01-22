---
title: Konfigurace spravovaných identit pro prostředky Azure na virtuální počítač škálovací sadu pomocí šablony
description: Nastavit, podrobné pokyny ke konfiguraci spravovaných identit pro prostředky Azure na škálovací sadu virtuálních počítačů pomocí šablony Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 6498079950310e52fcb16172a34b9848e6a98e8b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429017"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-azure-virtual-machine-scale-using-a-template"></a>Konfigurace spravovaných identit pro prostředky Azure v měřítku virtuálních počítačů Azure pomocí šablony

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak k provádění následujících spravovaných identit pro operace se prostředky Azure na škálovací sadu virtuálních počítačů Azure pomocí šablony nasazení Azure Resource Manageru:
- Povolení a zákaz systém přiřadil spravovaná identita na škálovací sadu virtuálních počítačů Azure
- Přidání a odebrání uživatelsky přiřazené spravovanou identitu na škálovací sadu virtuálních počítačů Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení řízení přístupu na základě rolí Azure:

    > [!NOTE]
    > Žádné další Azure vyžaduje přiřazení rolí adresáře AD.

    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) vytvořit škálovací sadu virtuálních počítačů a povolit a odeberte systém a/nebo uživatelsky přiřazené identity spravované ze škálovací sady virtuálních počítačů.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření uživatel přiřazenou se identita spravované.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat uživatel přiřazenou se identita spravované od a do škálovací sady virtuálních počítačů.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Stejně jako webu Azure portal a skriptování, [Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md) šablony poskytují možnost nasazení nové nebo upravené zdroje, které jsou definované ve skupině prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místních i založené na portálu, včetně:

   - Použití [vlastní šablonu z Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), která umožňuje vytvoření zcela nové šablony, nebo ji založit na existující běžné nebo [šablonu pro rychlý Start](https://azure.microsoft.com/documentation/templates/).
   - Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo [aktuální stav nasazení](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Pomocí místní [editor JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a nahrání a nasazení pomocí Powershellu nebo rozhraní příkazového řádku.
   - Pomocí sady Visual Studio [projekt skupiny prostředků Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu.  

Bez ohledu na vámi zvolené možnosti syntaxe šablony je stejný během počátečního nasazení a opětovné nasazení. Povolení spravovaných identit pro prostředky Azure na nový nebo existující virtuální počítač se provádí stejným způsobem. Také ve výchozím nastavení, provede Azure Resource Manageru [přírůstkové aktualizace](../../azure-resource-manager/deployment-modes.md) až po nasazení.

## <a name="system-assigned-managed-identity"></a>Systém přiřadil spravované identity

V této části se povolí a zakáže systém přiřadil spravovanou identitu pomocí šablony Azure Resource Manageru.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Povolit systém přiřadil spravované identity během vytváření vytvoření škálovací sady virtuálních počítačů nebo existující škálovací sady virtuálních počítačů

1. Ať už jste přihlášení do Azure místně nebo prostřednictvím portálu Azure portal, pomocí účtu, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.
   
2. Chcete-li systém přiřadil spravovanou identitu, načtení šablony do editoru, vyhledejte `Microsoft.Compute/virtualMachinesScaleSets` prostředků zájmu v prostředcích a přidejte `identity` vlastnost na stejné úrovni jako `"type": "Microsoft.Compute/virtualMachinesScaleSets"` vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

3. (Volitelné) Přidat škálovací sady virtuálních počítačů spravovaných identit pro rozšíření prostředků Azure jako `extensionsProfile` elementu. Tento krok je volitelný, i identitu služby Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také.  Použijte následující syntaxi:

   >[!NOTE] 
   > V následujícím příkladu se předpokládá škálovací sadu virtuálních počítačů Windows nastavit rozšíření (`ManagedIdentityExtensionForWindows`) se nasazuje. Můžete také nakonfigurovat pro Linux s použitím `ManagedIdentityExtensionForLinux` namísto toho `"name"` a `"type"` elementy.
   >

   ```json
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
                    },
                    "protectedSettings": {}
                }
            }
   ```

4. Jakmile budete hotovi, v dalších částech měli přidat do části prostředku šablony a by měl vypadat takto:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázat systém přiřadil spravovanou identitu ze škálovací sady virtuálních počítačů Azure

Pokud máte škálovací sady virtuálního počítače, který už je systém přiřadil spravovanou identitu:

1. Ať už jste přihlášení do Azure místně nebo prostřednictvím portálu Azure portal, pomocí účtu, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Načíst šablonu do [editor](#azure-resource-manager-templates) a vyhledejte `Microsoft.Compute/virtualMachineScaleSets` prostředků zájmu v rámci `resources` oddílu. Pokud máte virtuální počítač, který má jenom systém přiřadil spravovanou identitu, můžete jej zakázat tak, že změníte typ identity k `None`.

   **Microsoft.Compute/virtualMachineScaleSets rozhraní API verze 2018-06-01**

   Pokud je vaše verze rozhraní API `2018-06-01` a váš virtuální počítač má systém i uživatelsky přiřazené identity spravované odebrat `SystemAssigned` z typ identity a zachovat `UserAssigned` spolu s hodnotami userAssignedIdentities slovníku.

   **Microsoft.Compute/virtualMachineScaleSets rozhraní API verze 2018-06-01**

   Pokud je vaše verze rozhraní API `2017-12-01` a škálovací sady virtuálních počítačů má systém i uživatelsky přiřazené identity spravované odebrat `SystemAssigned` z typ identity a zachovat `UserAssigned` spolu s `identityIds` pole uživatelsky přiřazené spravované identity. 
   
    

   Následující příklad ukazuje, jak odebrat spravovanou identitu systém přiřadil z virtuálního počítače škálovací sady s žádné spravované identity přiřazené uživateli:
   
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

## <a name="user-assigned-managed-identity"></a>Uživatel přiřazenou spravované identity

V této části přiřadíte uživateli přiřazena spravovanou identitu virtuálního počítače škálovací sady s použitím šablony Azure Resource Manageru.

> [!Note]
> Vytvoření uživatelsky přiřazené spravovanou identitu pomocí šablony Azure Resource Manageru najdete v tématu [vytvoření uživatelsky přiřazené identity spravované](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virutal-machine-scale-set"></a>Přiřadit uživateli přiřazena spravovanou identitu do škálovací sady virtuálních počítačů

1. V části `resources` prvku, přidejte následující položku spravované identity přiřazené uživateli přiřadit škálovací sadu virtuálních počítačů.  Nezapomeňte nahradit `<USERASSIGNEDIDENTITY>` názvem uživatel přiřazenou spravované identity, které jste vytvořili.
   
   **Microsoft.Compute/virtualMachineScaleSets rozhraní API verze 2018-06-01**

   Pokud je vaše verze rozhraní API `2018-06-01`, uživatelsky přiřazené spravovaných identit jsou uloženy v `userAssignedIdentities` slovníku formátu a `<USERASSIGNEDIDENTITYNAME>` hodnota musí být uložen v proměnné definované v `variables` vaší šablony.

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

   **Microsoft.Compute/virtualMachineScaleSets rozhraní API verze 2017-12-01**
    
   Pokud vaše `apiVersion` je `2017-12-01` nebo dřívější, uživatelsky přiřazené spravovaných identit jsou uloženy v `identityIds` pole a `<USERASSIGNEDIDENTITYNAME>` hodnota musí být uložen v proměnné definované v sekci proměnných šablony.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ``` 

2. (Volitelné) Přidejte následující položku v rámci `extensionProfile` element přiřadit vaše VMSS spravovaných identit pro rozšíření prostředků Azure. Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také. Použijte následující syntaxi:
   
    ```JSON
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
                        },
                        "protectedSettings": {}
                    }
                }
    ```

3. Jakmile budete hotovi, vaše šablona by měl vypadat nějak takto:
   
   **Microsoft.Compute/virtualMachineScaleSets rozhraní API verze 2018-06-01**   

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

   **Microsoft.Compute/virtualMachines rozhraní API verze 2017-12-01**

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
### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Odeberte uživatelsky přiřazené identity spravované z škálovací sady virtuálních počítačů Azure

Pokud máte škálovací sady virtuálního počítače, který už je uživatel přiřazenou spravovanou identitu:

1. Ať už jste přihlášení do Azure místně nebo prostřednictvím portálu Azure portal, pomocí účtu, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Načíst šablonu do [editor](#azure-resource-manager-templates) a vyhledejte `Microsoft.Compute/virtualMachineScaleSets` prostředků zájmu v rámci `resources` oddílu. Pokud máte škálovací sadu virtuálních počítačů, který má uživatel přiřazenou spravované identity jenom, je můžete zakázat tak, že změníte typ identity k `None`.

   Následující příklad ukazuje, jak odebrat všechny přiřazené uživatele spravovaných identit z virtuálního počítače se žádný systém přiřadil spravovaných identit:

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
   
   **Microsoft.Compute/virtualMachineScaleSets rozhraní API verze 2018-06-01**
    
   Pokud chcete odebrat jeden uživatel přiřazenou spravovanou identitu ze škálovací sady virtuálních počítačů, odeberte ho z `userAssignedIdentities` slovníku.

   Pokud máte systém přiřadil identitou, uložte ho v `type` pod `identity` hodnotu.

   **Microsoft.Compute/virtualMachineScaleSets rozhraní API verze 2017-12-01**

   Pokud chcete odebrat jeden uživatel přiřazenou spravovanou identitu ze škálovací sady virtuálních počítačů, odeberte ho z `identityIds` pole.

   Pokud máte systém přiřadil spravovanou identitu, uložte ho v `type` pod `identity` hodnotu.
   
## <a name="next-steps"></a>Další postup

- [Spravovaných identit pro prostředky Azure přehled](overview.md).

