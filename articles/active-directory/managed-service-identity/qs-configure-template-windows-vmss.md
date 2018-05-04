---
title: Konfigurace MSI ve virtuálním počítači Azure měřítku nastavit pomocí šablony
description: Podrobné pokyny ke konfiguraci a spravovaná služba Identity (MSI) na VMSS Azure pomocí šablony Azure Resource Manager.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 64fe217cf3d845e6a09fe67d03648e79e8a4cadd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Nakonfigurujte identitu VMSS spravované služby pomocí šablony

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se naučíte, jak provádět následující operace identita spravované služby na VMSS Azure pomocí šablony Azure Resource Manager nasazení:
- Povolení a zákaz systému přiřazené identita na VMSS Azure
- Přidání a odebrání uživatele přiřazené identita na VMSS Azure

## <a name="prerequisites"></a>Požadavky

- Pokud jste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systémem přiřazený a uživatel s přiřazenou identity](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

S Azure portálu a skriptování, šablony Azure Resource Manager poskytuje schopnost nasadit nové nebo upravené zdroje, které jsou definované skupiny prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místní i založené na portálu, včetně:

   - Použití [vlastní šablonu z Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), který umožňuje vytvořit šablonu od začátku, nebo ji založit na existující běžné nebo [šablony rychlý Start](https://azure.microsoft.com/documentation/templates/).
   - Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo z [aktuální stav nasazení](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Pomocí místní [editor JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a pak nahrání a nasazení pomocí prostředí PowerShell nebo rozhraní příkazového řádku.
   - Pomocí sady Visual Studio [projekt skupiny prostředků Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu.  

Bez ohledu na to, kterou si zvolíte možnost syntaxe šablony je stejné během počátečního nasazení a opětovné nasazení. Povolení MSI na nový nebo existující virtuální počítač se provádí stejným způsobem. Také ve výchozím nastavení, nemá Azure Resource Manager [přírůstkové aktualizace](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) pro nasazení.

## <a name="system-assigned-identity"></a>Systém přiřazené identity

V této části bude povolit nebo zakázat systému přiřazené identity pomocí šablony Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Povolit systém přiřazené identity během vytváření služby Azure VMSS nebo existující VMSS Azure

1. Jestli přihlášení k Azure místně nebo prostřednictvím portálu Azure, pomocí účtu, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Po načtení šablony do editoru, vyhledejte `Microsoft.Compute/virtualMachineScaleSets` prostředků zájmu v rámci `resources` části. Váš může vypadat mírně lišit od následující snímek obrazovky, v závislosti na editoru, kterou používáte, a jestli upravujete šablonu pro nové nasazení nebo existující.
   
   ![Snímek obrazovky šablonu - najít virtuálního počítače](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. Chcete-li povolit identitě systému přiřazen, přidejte `"identity"` vlastnost na stejné úrovni jako `"type": "Microsoft.Compute/virtualMachineScaleSets"` vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Volitelné) Přidání rozšíření MSI jako sad škálování virtuálních počítačů `extensionsProfile` elementu. Tento krok je nepovinný, protože identitu Azure Instance Metadata služby (IMDS), můžete použít k načtení také tokeny.  Použijte následující syntaxi:

   >[!NOTE] 
   > Následující příklad předpokládá, že rozšíření sady škálování virtuálního počítače Windows (`ManagedIdentityExtensionForWindows`) se nasazuje. Můžete také nakonfigurovat pro Linux pomocí `ManagedIdentityExtensionForLinux` namísto toho `"name"` a `"type"` elementy.
   >

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

5. Když jste hotovi, vaše šablona by měla vypadat podobně jako následující:

   ![Snímek obrazovky šablony po aktualizaci](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázat identitu systému přiřazené z sady škálování virtuálního počítače Azure

> [!NOTE]
> Zakázání spravované identita služby z virtuálního počítače není aktuálně podporováno. Do té doby můžete přepnout mezi použitím přiřazené systému a přiřadit identity uživatelů.

Pokud máte škálování virtuálních počítačů, nastavte ho už vyžaduje systému přiřazené identity, ale stále potřebuje uživatel s přiřazenou identity:

1. Jestli přihlášení k Azure místně nebo prostřednictvím portálu Azure, pomocí účtu, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Změnit typ identity na `'UserAssigned'`

## <a name="user-assigned-identity"></a>Uživatel s přiřazenou identity

V této části vytvoříte uživatel přiřazený VMSS Azure pomocí šablony Azure Resource Manager a identit.

### <a name="create-and-assign-a-user-assigned-identity-to-an-azure-vmss"></a>Vytvořit a přiřadit uživatele přiřazené Azure VMSS identity

1. Provést první krok v části [povolit identita systémové přiřazené během vytváření služby Azure VMSS, na existující VMSS](qs-configure-template-windows-vmss.md#enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss).

2. V části proměnné, která obsahuje konfigurační proměnné pro vaše VMSS Azure přidejte záznam pro uživatelské jméno přiřazené identity podobný následujícímu.  To obsahuje hodnotu vaše uživatele přiřazené identity během procesu vytváření Azure VMSS:
    
    > [!IMPORTANT]
    > Vytvoření uživatele přiřazené identity s speciální znaky (tj. podtržítko) v názvu není aktuálně podporován. Použijte alfanumerické znaky. Vraťte se zpět pro aktualizace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

    ```json
    "variables": {
        "vmssPrefix": "vmss",
        "vmssName": "[concat(variables('vmssPrefix'), uniquestring(resourceGroup().id,deployment().name))]",
        //other vm configuration variables...
        "identityName": "[concat(variables('vmssName'), 'id')]"
    ```
3. V části `resources` element přidejte následující položku pro vytvoření identity uživatele přiřazené:

    ```json
    {
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
        "name": "[variables('identityName')]",
        "apiVersion": "2015-08-31-PREVIEW",
        "location": "[resourceGroup().location]"
    },
    ```
4. V části Další `resources` element přidejte následující položky lze přiřadit identitu uživatele přiřazené vaší VMSS:

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/, variables('identityName'))]"
            ]
        }

    }
    ```
5. (Volitelné) Přidejte následující položku v části `extensionProfile` elementu, který chcete přiřadit vaše VMSS rozšíření spravované identity. Tento krok je nepovinný, protože identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k načtení také tokeny. Použijte následující syntaxi:
   
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
6.  Až skončíte, vaše šablona by měla vypadat podobně jako následující:
    > [!NOTE]
    > Šablona neobsahuje seznam všech proměnných potřebné k vytvoření vašeho VMSS.  `//other configuration variables...` používá se místo všech potřebných konfiguračních proměnných kvůli stručnosti.

      ![Snímek obrazovky identitu uživatele přiřazené](../media/msi-qs-configure-template-windows-vmss/template-vmss-user-assigned-identity.png)

## <a name="next-steps"></a>Další postup

- Širší perspektivy o MSI, přečtěte si [identita spravované služby přehled](overview.md).

