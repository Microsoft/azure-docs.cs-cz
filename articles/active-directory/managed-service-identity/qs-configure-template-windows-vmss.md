---
title: Konfigurace Identity spravované služby na škálovací sadu pomocí šablony Azure virtuálních počítačů
description: Podrobné pokyny ke konfiguraci Identity spravované služby ve VMSS Azure pomocí šablony Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 69b88b5aabbadadea3b04f96d9a21f266c85a52f
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125380"
---
# <a name="configure-managed-service-identity-on-virtual-machine-scale-using-a-template"></a>Konfigurace Identity spravované služby v měřítku virtuálního počítače pomocí šablony

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak provádět následující operace Identity spravované služby na škálovací sadu virtuálních počítačů Azure pomocí šablony nasazení Azure Resource Manageru:
- Povolit a zakázat identitu přiřazenou systémem na škálovací sadu virtuálních počítačů Azure
- Přidání a odebrání identity přiřazené uživateli na škálovací sadu virtuálních počítačů Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) chcete vytvořit škálovací sadu virtuálních počítačů a povolit a odeberte systém a/nebo uživatel přiřazenou spravovanou identitu ze škálovací sady virtuálních počítačů.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role pro vytvoření identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat identity přiřazené uživateli, ze kterých a do škálovací sady virtuálních počítačů.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Stejně jako webu Azure portal a skriptování, [Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md) šablony poskytují možnost nasazení nové nebo upravené zdroje, které jsou definované ve skupině prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místních i založené na portálu, včetně:

   - Použití [vlastní šablonu z Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), která umožňuje vytvoření zcela nové šablony, nebo ji založit na existující běžné nebo [šablonu pro rychlý Start](https://azure.microsoft.com/documentation/templates/).
   - Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo [aktuální stav nasazení](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Pomocí místní [editor JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a nahrání a nasazení pomocí Powershellu nebo rozhraní příkazového řádku.
   - Pomocí sady Visual Studio [projekt skupiny prostředků Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu.  

Bez ohledu na vámi zvolené možnosti syntaxe šablony je stejný během počátečního nasazení a opětovné nasazení. Povolení Identity spravované služby na novou nebo existující virtuální počítač se provádí stejným způsobem. Také ve výchozím nastavení, provede Azure Resource Manageru [přírůstkové aktualizace](../../azure-resource-manager/deployment-modes.md) až po nasazení.

## <a name="system-assigned-identity"></a>Identitu přiřazenou systémem

V této části se povolí a zakáže systém přiřadil identity pomocí šablony Azure Resource Manageru.

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Povolit identitu přiřazenou systémem během vytváření vytvoření škálovací sady virtuálních počítačů nebo existující škálovací sady virtuálních počítačů

1. Ať už jste přihlášení do Azure místně nebo prostřednictvím portálu Azure portal, pomocí účtu, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.
   
2. Chcete-li povolit identitu přiřazenou systémem, načtení šablony do editoru, vyhledejte `Microsoft.Compute/virtualMachinesScaleSets` prostředků zájmu v prostředcích a přidejte `identity` vlastnost na stejné úrovni jako `"type": "Microsoft.Compute/virtualMachines"` vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

3. (Volitelné) Přidejte identitu spravované služby rozšíření jako virtuálního počítače škálovací sady `extensionsProfile` elementu. Tento krok je volitelný, i identitu služby Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také.  Použijte následující syntaxi:

   >[!NOTE] 
   > V následujícím příkladu se předpokládá škálovací sadu virtuálních počítačů Windows nastavit rozšíření (`ManagedIdentityExtensionForWindows`) se nasazuje. Můžete také nakonfigurovat pro Linux s použitím `ManagedIdentityExtensionForLinux` namísto toho `"name"` a `"type"` elementy.
   >

   ```json
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
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

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázat identitu přiřazenou systémem ze škálovací sady virtuálních počítačů Azure

Pokud máte virtuální počítač škálovací sadu, která už potřebuje identity spravované služby:

1. Ať už jste přihlášení do Azure místně nebo prostřednictvím portálu Azure portal, pomocí účtu, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Načíst šablonu do [editor](#azure-resource-manager-templates) a vyhledejte `Microsoft.Compute/virtualMachineScaleSets` prostředků zájmu v rámci `resources` oddílu. Pokud máte virtuální počítač, který má jenom identitou přiřazenou systémem, můžete ho zakázat tak, že změníte typ identity k `None`.

   **Microsoft.Compute/virtualMachineScaleSets rozhraní API verze 2018-06-01**

   Pokud je vaše verze rozhraní API `2018-06-01` a systémových i uživatelských identit přiřazených má virtuální počítač, odeberte `SystemAssigned` z typ identity a zachovat `UserAssigned` spolu s hodnotami userAssignedIdentities slovníku.

   **Microsoft.Compute/virtualMachineScaleSets rozhraní API verze 2018-06-01 a starší**

   Pokud je vaše verze rozhraní API `2017-12-01` a škálovací sady virtuálních počítačů má systémových i uživatelských identit přiřazených, odeberte `SystemAssigned` z typ identity a zachovat `UserAssigned` spolu s `identityIds` pole uživatelsky přiřazených identit. 
   
    

   Následující příklad ukazuje, jak odebrat systému identity přiřazené z virtuálního počítače škálovací sady se žádný uživatel identit přiřazených:
   
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

## <a name="user-assigned-identity"></a>Identity přiřazené uživateli

V této části můžete přiřadit uživatele identity přiřazené do virtuálního počítače škálovací sady s použitím šablony Azure Resource Manageru.

> [!Note]
> Vytvoření identity přiřazené uživateli pomocí šablony Azure Resource Manageru, najdete v článku [vytvoření identity přiřazené uživateli](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Přiřadit uživatele identity přiřazené do Azure VMSS

1. V části `resources` prvku, přidejte následující položku identity přiřazené uživateli přiřadit škálovací sadu virtuálních počítačů.  Nezapomeňte nahradit `<USERASSIGNEDIDENTITY>` s názvem identity přiřazené uživateli jste vytvořili.
   
   **Microsoft.Compute/virtualMachineScaleSets rozhraní API verze 2018-06-01**

   Pokud je vaše verze rozhraní API `2018-06-01`, uživatelsky přiřazených identit jsou uloženy v `userAssignedIdentities` slovníku formátu a `<USERASSIGNEDIDENTITYNAME>` hodnota musí být uložen v proměnné definované v `variables` vaší šablony.

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
    
   Pokud vaše `apiVersion` je `2017-12-01` nebo dřívější, uživatelsky přiřazených identit jsou uloženy v `identityIds` pole a `<USERASSIGNEDIDENTITYNAME>` hodnota musí být uložen v proměnné definované v sekci proměnných šablony.

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

2. (Volitelné) Přidejte následující položku v rámci `extensionProfile` element přiřadit rozšíření spravovanou identitu vaší VMSS. Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také. Použijte následující syntaxi:
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
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

   **Microsoft.Compute/virtualMachines rozhraní API verze 2017-12-01 dříve eand**

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
### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Odebrání identity přiřazené uživateli z škálovací sady virtuálních počítačů Azure

Pokud máte virtuální počítač škálovací sadu, která už potřebuje identity spravované služby:

1. Ať už jste přihlášení do Azure místně nebo prostřednictvím portálu Azure portal, pomocí účtu, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Načíst šablonu do [editor](#azure-resource-manager-templates) a vyhledejte `Microsoft.Compute/virtualMachineScaleSets` prostředků zájmu v rámci `resources` oddílu. Pokud máte škálovací sady virtuálního počítače, který má jenom identity přiřazené uživateli, můžete ho zakázat tak, že změníte typ identity k `None`.

   Následující příklad ukazuje, jak odebrat všechny přiřazené identity z virtuálního počítače bez systému identit přiřazených uživateli:

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
    
   K odebrání jednoho uživatele přiřazeny identity ze škálovací sady virtuálních počítačů, odeberte ho z `userAssignedIdentities` slovníku.

   Pokud máte identitou přiřazenou systémem, uložte ho v `type` pod `identity` hodnotu.

   **Microsoft.Compute/virtualMachineScaleSets rozhraní API verze 2017-12-01**

   K odebrání jednoho uživatele přiřazeny identity ze škálovací sady virtuálních počítačů, odeberte ho z `identityIds` pole.

   Pokud máte identitou přiřazenou systémem, uložte ho v `type` pod `identity` hodnotu.
   
## <a name="next-steps"></a>Další postup

- Širší perspektivy o identitu spravované služby najdete v článku [identita spravované služby přehled](overview.md).

