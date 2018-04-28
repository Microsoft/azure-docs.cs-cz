---
title: Postup konfigurace MSI ve virtuálním počítači Azure pomocí šablony
description: Podrobné pokyny pro konfiguraci a spravovaná služba Identity (MSI) ve virtuálním počítači Azure, pomocí šablony Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 521c5a3c0ad55afa0b71628195be7782b0e43b67
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Konfigurace služby Identita spravované virtuálního počítače pomocí šablony

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se naučíte, jak provádět následující operace identita spravované služby ve virtuálním počítači Azure, pomocí šablony Azure Resource Manager nasazení:

## <a name="prerequisites"></a>Požadavky

- Pokud jste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systémem přiřazený a uživatel s přiřazenou identity](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

S Azure portálu a skriptování, šablony Azure Resource Manager poskytuje schopnost nasadit nové nebo upravené zdroje, které jsou definované skupiny prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místní i založené na portálu, včetně:

   - Použití [vlastní šablonu z Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), který umožňuje vytvořit šablonu od začátku, nebo ji založit na existující běžné nebo [šablony rychlý Start](https://azure.microsoft.com/documentation/templates/).
   - Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo z [aktuální stav nasazení](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Pomocí místní [editor JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a pak nahrání a nasazení pomocí prostředí PowerShell nebo rozhraní příkazového řádku.
   - Pomocí sady Visual Studio [projekt skupiny prostředků Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu.  

Bez ohledu na to, kterou si zvolíte možnost syntaxe šablony je stejné během počátečního nasazení a opětovné nasazení. Povolení systému nebo uživatel s přiřazenou identita na nový nebo existující virtuální počítač se provádí stejným způsobem. Také ve výchozím nastavení, nemá Azure Resource Manager [přírůstkové aktualizace](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) pro nasazení.

## <a name="system-assigned-identity"></a>Systém přiřazené identity

V této části bude povolit nebo zakázat systému přiřazené identity pomocí šablony Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Povolit systém přiřazené identity během vytváření virtuální počítač Azure nebo na existující virtuální počítač

1. Ať už místně se přihlaste k Azure nebo prostřednictvím portálu Azure pomocí účtu, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Ujistěte se také, že váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače (například role "Přispěvatel virtuálních počítačů").

2. Po načtení šablony do editoru, vyhledejte `Microsoft.Compute/virtualMachines` prostředků zájmu v rámci `resources` části. Váš může vypadat mírně lišit od následující snímek obrazovky, v závislosti na editoru, kterou používáte, a jestli upravujete šablonu pro nové nasazení nebo existující.

   >[!NOTE] 
   > Tento příklad předpokládá proměnné, jako například `vmName`, `storageAccountName`, a `nicName` byla definována v šabloně.
   >

   ![Snímek obrazovky šablonu - najít virtuálního počítače](../media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Systém přiřazené identity povolit, přidejte `"identity"` vlastnost na stejné úrovni jako `"type": "Microsoft.Compute/virtualMachines"` vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Volitelné) Přidání rozšíření virtuálního počítače MSI jako `resources` elementu. Tento krok je nepovinný, protože identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k načtení také tokeny.  Použijte následující syntaxi:

   >[!NOTE] 
   > V následujícím příkladu se předpokládá, rozšíření virtuálního počítače s Windows (`ManagedIdentityExtensionForWindows`) se nasazuje. Můžete také nakonfigurovat pro Linux pomocí `ManagedIdentityExtensionForLinux` namísto toho `"name"` a `"type"` elementy.
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

5. Když jste hotovi, vaše šablona by měla vypadat podobně jako následující:

   ![Snímek obrazovky šablony po aktualizaci](../media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Zakázat systému přiřazené identity z virtuálního počítače Azure

> [!NOTE]
> Zakázání spravované identita služby z virtuálního počítače není aktuálně podporováno. Do té doby můžete přepnout mezi použitím přiřazené systému a přiřadit identity uživatelů.

Pokud máte virtuální počítač, který už nepotřebuje identita spravované služby:

1. Ať už místně se přihlaste k Azure nebo prostřednictvím portálu Azure pomocí účtu, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Ujistěte se také, že váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače (například role "Přispěvatel virtuálních počítačů").

2. Změnit typ identity na `UserAssigned`.

## <a name="user-assigned-identity"></a>Uživatel s přiřazenou identity

V této části vytvoříte virtuální počítač Azure pomocí šablony Azure Resource Manager a identity uživatele.

 ### <a name="create-and-assign-a-user-assigned-identity-to-an-azure-vm"></a>Vytvořit a přiřadit uživatele přiřazené identity virtuálního počítače Azure

1. Provést první krok v části [povolit identita systémové přiřazen při vytvoření virtuálního počítače Azure, nebo na existující virtuální počítač](#enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm)

2.  V části proměnné, která obsahuje konfigurační proměnné pro virtuální počítač Azure přidejte záznam pro uživatelské jméno přiřazené identity podobný následujícímu.  Tím se vytvoří uživatel s přiřazenou identity během procesu vytváření virtuálního počítače Azure:
    
    > [!IMPORTANT]
    > Vytvoření uživatele přiřazené identity s speciální znaky (tj. podtržítko) v názvu není aktuálně podporován. Použijte alfanumerické znaky. Vraťte se zpět pro aktualizace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

    ```json
    "variables": {
        "vmName": "[parameters('vmName')]",
        //other vm configuration variables...
        "identityName": "[concat(variables('vmName'), 'id')]"
    ```

3. V části `resources` elementu, přidejte následující položku pro vytvoření identity uživatele přiřazené:

    ```json
    {
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
        "name": "[variables('identityName')]",
        "apiVersion": "2015-08-31-PREVIEW",
        "location": "[resourceGroup().location]"
    },
    ```

4. V části Další `resources` element přidejte následující položky lze přiřadit spravovanou identitu rozšíření virtuálního počítače:

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
        "apiVersion": "2015-05-01-preview",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForLinux",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
5. Pak přidejte následující položku přiřazení vaše uživatele přiřazené identity virtuálního počítače:

    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('identityName'))]"
            ]
        },
    ```
6.  Až skončíte, vaše šablona by měla vypadat podobně jako následující:
    > [!NOTE]
    > Šablona neobsahuje seznam všech proměnných potřebné k vytvoření virtuálního počítače.  `//other configuration variables...` používá se místo všech potřebných konfiguračních proměnných kvůli stručnosti.

      ![Snímek obrazovky identitu uživatele přiřazené](../media/msi-qs-configure-template-windows-vm/template-user-assigned-identity.png)


## <a name="related-content"></a>Související obsah

- Širší perspektivy o MSI, přečtěte si [identita spravované služby přehled](overview.md).

