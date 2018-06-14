---
title: Postup konfigurace MSI se přiřazené pro virtuální počítač Azure pomocí šablony Azure
description: Krok podle podrobné pokyny pro konfiguraci přiřazený uživatelem spravované služby Identity (MSI) pro virtuální počítač Azure, pomocí šablony Azure Resource Manager.
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e01e4c397e0d0a19280a32fc1e8341b57b47e4eb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
ms.locfileid: "28984035"
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Konfigurace přiřazený uživatelem spravované služby Identity (MSI) pro virtuální počítač pomocí šablony Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje Azure služby spravovanou identitu ve službě Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku zjistěte, jak povolit a odebrat MSI se přiřazené pro virtuální počítač Azure, pomocí nasazení šablony Azure Resource Manager.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Povolit MSI během vytváření virtuální počítač Azure nebo na existující virtuální počítač

S Azure portálu a skriptování, šablony Azure Resource Manager poskytuje schopnost nasadit nové nebo upravené zdroje, které jsou definované skupiny prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místní i založené na portálu, včetně:

   - Použití [vlastní šablonu z Azure Marketplace](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), který umožňuje vytvořit šablonu od začátku, nebo ji založit na existující běžné nebo [šablony rychlý Start](https://azure.microsoft.com/documentation/templates/).
   - Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo z [aktuální stav nasazení](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Pomocí místní [editor JSON (například VS Code)](~/articles/azure-resource-manager/resource-manager-create-first-template.md)a pak nahrání a nasazení pomocí prostředí PowerShell nebo rozhraní příkazového řádku.
   - Pomocí sady Visual Studio [projekt skupiny prostředků Azure](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu.  

Bez ohledu na to, kterou si zvolíte možnost syntaxe šablony je stejné během počátečního nasazení a opětovné nasazení. Vytvoření a přiřazení MSI se přiřadit uživatele k nové nebo existující virtuální počítač se provádí stejným způsobem. Také ve výchozím nastavení, nemá Azure Resource Manager [přírůstkové aktualizace](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) pro nasazení:

1. Ať už místně se přihlaste k Azure nebo prostřednictvím portálu Azure pomocí účtu, který je přidružený k předplatnému Azure, který obsahuje MSI a virtuálních počítačů. Ujistěte se také, že váš účet patří do role, která vám dává oprávnění k zápisu na předplatné nebo prostředky (například role "vlastník").

2. Po načtení šablony do editoru, vyhledejte `Microsoft.Compute/virtualMachines` prostředků zájmu v rámci `resources` části. Váš může vypadat mírně lišit od následující snímek obrazovky, v závislosti na editoru, kterou používáte, a jestli upravujete šablonu pro nové nasazení nebo existující.

   >[!NOTE] 
   > Tento příklad předpokládá proměnné, jako například `vmName`, `storageAccountName`, a `nicName` byla definována v šabloně.
   >

   ![Snímek obrazovky šablonu - najít virtuálního počítače](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Přidat `"identity"` vlastnost na stejné úrovni jako `"type": "Microsoft.Compute/virtualMachines"` vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Pak přidat rozšíření virtuálního počítače MSI jako `resources` elementu. Použijte následující syntaxi:

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

   ![Snímek obrazovky šablony po aktualizaci](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Odebrání virtuálního počítače Azure MSI

Pokud máte virtuální počítač, který už nepotřebuje MSI:

1. Ať už místně se přihlaste k Azure nebo prostřednictvím portálu Azure pomocí účtu, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Ujistěte se také, že váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače (například role "Přispěvatel virtuálních počítačů").

2. Odeberte dva elementy, které byly přidány v předchozí části: Virtuálního počítače `"identity"` vlastnost a `"Microsoft.Compute/virtualMachines/extensions"` prostředků.

## <a name="related-content"></a>Související obsah

- Širší perspektivy o MSI, přečtěte si [identita spravované služby přehled](msi-overview.md).

