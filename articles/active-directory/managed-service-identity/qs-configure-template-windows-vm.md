---
title: Konfigurace MSI ve Virtuálním počítači Azure pomocí šablony
description: Podrobné pokyny ke konfiguraci Identity spravované služby (MSI) na virtuálním počítači Azure pomocí šablony Azure Resource Manageru.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: ea51252de4877cdeee093c4f21f68f59a061cdc9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213509"
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Konfigurace virtuálních počítačů spravovaná identita služby s použitím šablony

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak provádět následující operace Identity spravované služby na virtuálním počítači Azure pomocí šablony nasazení Azure Resource Manageru:

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) k vytvoření virtuálního počítače a povolit a odeberte systém a/nebo uživatel spravované identity přiřazené z virtuálního počítače Azure.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role pro vytvoření identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat identity přiřazené uživateli, ze kterých a do virtuálního počítače.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Stejně jako webu Azure portal a skriptování, [Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md) šablony poskytují možnost nasazení nové nebo upravené zdroje, které jsou definované ve skupině prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místních i založené na portálu, včetně:

   - Použití [vlastní šablonu z Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), která umožňuje vytvoření zcela nové šablony, nebo ji založit na existující běžné nebo [šablonu pro rychlý Start](https://azure.microsoft.com/documentation/templates/).
   - Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo [aktuální stav nasazení](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Pomocí místní [editor JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a nahrání a nasazení pomocí Powershellu nebo rozhraní příkazového řádku.
   - Pomocí sady Visual Studio [projekt skupiny prostředků Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu.  

Bez ohledu na vámi zvolené možnosti syntaxe šablony je stejný během počátečního nasazení a opětovné nasazení. Povolení systému nebo na nový nebo existující virtuální počítač identity přiřazené uživateli se provádí stejným způsobem. Také ve výchozím nastavení, provede Azure Resource Manageru [přírůstkové aktualizace](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) až po nasazení.

## <a name="system-assigned-identity"></a>Identitu přiřazenou systémem

V této části se povolí a zakáže systému přiřazené identity pomocí šablony Azure Resource Manageru.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Povolit při vytváření virtuálního počítače Azure nebo z existujícího virtuálního počítače identitu přiřazenou systémem

1. Ať už místně se přihlaste do Azure nebo prostřednictvím portálu Azure portal pomocí účtu, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač.

2. Po načtení šablony do editoru, vyhledejte `Microsoft.Compute/virtualMachines` prostředků zájmu v rámci `resources` oddílu. Váš může vypadat trochu jinak než na následujícím snímku obrazovky, v závislosti na používaném editoru a určuje, zda jsou úpravy šablony pro existující nebo nové nasazení.

   >[!NOTE] 
   > Tento příklad předpokládá proměnných, například `vmName`, `storageAccountName`, a `nicName` byly definovány v šabloně.
   >

   ![Snímek obrazovky se šablona – vyhledejte virtuální počítač](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Pokud chcete povolit identitu přiřazenou systémem, přidejte `"identity"` vlastnost na stejné úrovni jako `"type": "Microsoft.Compute/virtualMachines"` vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Volitelné) Přidat rozšíření MSI virtuálního počítače jako `resources` elementu. Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také.  Použijte následující syntaxi:

   >[!NOTE] 
   > V následujícím příkladu se předpokládá rozšíření virtuálního počítače Windows (`ManagedIdentityExtensionForWindows`) se nasazuje. Můžete také nakonfigurovat pro Linux s použitím `ManagedIdentityExtensionForLinux` namísto toho `"name"` a `"type"` elementy.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
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
           },
           "protectedSettings": {}
       }
   }
   ```

5. Až budete hotovi, vaše šablona by měl vypadat nějak takto:

   ![Snímek obrazovky po aktualizaci šablony](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="assign-a-role-the-vms-system-assigned-identity"></a>Přiřazení role Virtuálního počítače identitu přiřazenou systémem

Po povolení identitou přiřazenou systémem ve virtuálním počítači, můžete jí udělit roli, jako **čtečky** přístup do skupiny prostředků, ve kterém byla vytvořena.

1. Ať už místně se přihlaste do Azure nebo prostřednictvím portálu Azure portal pomocí účtu, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač.
 
2. Načíst šablonu do [editor](#azure-resource-manager-templates) a přidejte následující informace poskytují vašemu virtuálnímu počítači **čtečky** přístup do skupiny prostředků, ve kterém byla vytvořena.  Struktury vaší šablony může lišit v závislosti na editoru a model nasazení, které zvolíte.
   
   V části `parameters` části přidejte následující:

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    V části `variables` části přidejte následující:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    V části `resources` části přidejte následující:

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

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Zakázat systému identity přiřazené z virtuálního počítače Azure

Pokud máte virtuální počítač, který už je identita spravované služby:

1. Ať už místně se přihlaste do Azure nebo prostřednictvím portálu Azure portal pomocí účtu, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač.

2. Načíst šablonu do [editor](#azure-resource-manager-templates) a vyhledejte `Microsoft.Compute/virtualMachines` prostředků zájmu v rámci `resources` oddílu. Pokud máte virtuální počítač, který má jenom identitou přiřazenou systémem, můžete jej zakázat tak, že změníte typ identity k `None`.  Pokud má virtuální počítač systémových i uživatelských identit přiřazených, odeberte `SystemAssigned` z typ identity a zachovat `UserAssigned` spolu s `identityIds` pole uživatelsky přiřazených identit.  Následující příklad ukazuje, jak odebrat systému identity přiřazené z virtuálního počítače bez uživatele identit přiřazených:
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```

## <a name="user-assigned-identity"></a>Identity přiřazené uživateli

V této části můžete přiřadit identity přiřazené uživateli na Virtuálním počítači Azure pomocí šablony Azure Resource Manageru.

> [!Note]
> Vytvoření identity přiřazené uživateli pomocí šablony Azure Resource Manageru, najdete v článku [vytvoření identity přiřazené uživateli](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Přiřazování identity přiřazené k virtuálnímu počítači Azure uživateli

1. V části `resources` prvku, přidejte následující položku pro přiřazení identit uživatel přiřazený k virtuálnímu počítači.  Nezapomeňte nahradit `<USERASSIGNEDIDENTITY>` s názvem identity přiřazené uživateli jste vytvořili.
   
   > [!Important]
   > `<USERASSIGNEDIDENTITYNAME>` Hodnoty zobrazené v následujícím příkladu musí být uložen v proměnné.  Aktuálně se podporují provádění přiřazení uživatelsky přiřazených identit k virtuálnímu počítači v šabloně Resource Manageru také verze rozhraní api musí odpovídat verzi v následujícím příkladu.
    
    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
            ]
        },
    ```
    
2. (Volitelné) Části `resources` prvku, přidejte následující položku k vašemu virtuálnímu počítači přiřadit rozšíření spravovaná identita. Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také. Použijte následující syntaxi:
    ```json
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
    ```
    
3.  Jakmile budete hotovi, vaše šablona by měl vypadat nějak takto:

      ![Snímek obrazovky identity přiřazené uživateli](./media/qs-configure-template-windows-vm/qs-configure-template-windows-vm-ua-final.PNG)


## <a name="related-content"></a>Související obsah

- Širší perspektivy o MSI najdete v článku [identita spravované služby přehled](overview.md).

