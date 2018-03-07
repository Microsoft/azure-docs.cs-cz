---
title: "Konfigurace MSI ve virtuálním počítači Azure měřítku nastavit pomocí šablony"
description: "Podrobné pokyny ke konfiguraci a spravovaná služba Identity (MSI) na VMSS Azure pomocí šablony Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 669348213e00cb338c69156f37779767c681a227
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Konfigurace služby Identita spravované virtuálního počítače pomocí šablony

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby (MSI) poskytuje Azure služby automaticky spravované identity v Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, povolení a odebrat MSI pro sadu škálování virtuálního počítače Azure pomocí šablony Azure Resource Manager nasazení.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set-or-an-existing-azure-virtual-machine-scale-set"></a>Povolit MSI během vytváření sadu škálování virtuálního počítače Azure nebo existující sady škálování virtuálního počítače Azure

S Azure portálu a skriptování, šablony Azure Resource Manager poskytuje schopnost nasadit nové nebo upravené zdroje, které jsou definované skupiny prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místní i založené na portálu, včetně:

   - Použití [vlastní šablonu z Azure Marketplace](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), který umožňuje vytvořit šablonu od začátku, nebo ji založit na existující běžné nebo [šablony rychlý Start](https://azure.microsoft.com/documentation/templates/).
   - Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo z [aktuální stav nasazení](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Pomocí místní [editor JSON (například VS Code)](../azure-resource-manager/resource-manager-create-first-template.md)a pak nahrání a nasazení pomocí prostředí PowerShell nebo rozhraní příkazového řádku.
   - Pomocí sady Visual Studio [projekt skupiny prostředků Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu.  

Bez ohledu na to, kterou si zvolíte možnost syntaxe šablony je stejné během počátečního nasazení a opětovné nasazení. Povolení MSI pro sadu škálování nový nebo existující virtuální počítač Azure se provádí stejným způsobem. Také ve výchozím nastavení, nemá Azure Resource Manager [přírůstkové aktualizace](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) pro nasazení:

1. Jestli přihlášení k Azure místně nebo prostřednictvím portálu Azure, pomocí účtu, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Po načtení šablony do editoru, vyhledejte `Microsoft.Compute/virtualMachineScaleSets` prostředků zájmu v rámci `resources` části. Váš může vypadat mírně lišit od následující snímek obrazovky, v závislosti na editoru, kterou používáte, a jestli upravujete šablonu pro nové nasazení nebo existující.
   
   ![Snímek obrazovky šablonu - najít virtuálního počítače](./media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. Přidat `"identity"` vlastnost na stejné úrovni jako `"type": "Microsoft.Compute/virtualMachineScaleSets"` vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Pak přidejte škálování virtuálního počítače nastavit rozšíření MSI jako `extensionsProfile` elementu. Použijte následující syntaxi:

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

   ![Snímek obrazovky šablony po aktualizaci](./media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Odebrání MSI sadu škálování virtuálního počítače Azure

Pokud máte škálovací sadu virtuálních počítačů, které už nepotřebuje MSI:

1. Jestli přihlášení k Azure místně nebo prostřednictvím portálu Azure, pomocí účtu, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Odeberte dva elementy, které byly přidány v předchozí části: škálovací sadu virtuálních počítačů `"identity"` a `"extensionsProfile"` vlastnosti.

## <a name="next-steps"></a>Další postup

- Širší perspektivy o MSI, přečtěte si [identita spravované služby přehled](msi-overview.md).

