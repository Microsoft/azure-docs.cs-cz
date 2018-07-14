---
title: Konfigurace MSI se uživatelsky přiřazené pro virtuální počítač Azure pomocí šablony Azure
description: Projděte pokyny ke konfiguraci uživatelsky přiřazené Identity spravované služby (MSI) pro virtuální počítač Azure, pomocí šablony Azure Resource Manageru.
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
ms.openlocfilehash: 1af9770fa899a03e6e3514c539c511ba26fdced4
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036376"
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Konfigurace uživatelsky přiřazené Identity spravované služby (MSI) pro virtuální počítač pomocí šablony Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje služby Azure s využitím spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a odebrat instalační služby MSI uživatelsky přiřazené pro virtuální počítač Azure, pomocí šablony nasazení Azure Resource Manageru.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Povolení MSI během vytváření virtuálního počítače Azure nebo z existujícího virtuálního počítače

Jak s Azure portal a vytváření skriptů, šablon Azure Resource Manageru umožňují nasazovat nové nebo upravené zdroje, které jsou definované ve skupině prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místních i založené na portálu, včetně:

   - Použití [vlastní šablonu z Azure Marketplace](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), která umožňuje vytvoření zcela nové šablony, nebo ji založit na existující běžné nebo [šablonu pro rychlý Start](https://azure.microsoft.com/documentation/templates/).
   - Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo [aktuální stav nasazení](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Pomocí místní [editor JSON (například VS Code)](~/articles/azure-resource-manager/resource-manager-create-first-template.md)a nahrání a nasazení pomocí Powershellu nebo rozhraní příkazového řádku.
   - Pomocí sady Visual Studio [projekt skupiny prostředků Azure](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu.  

Bez ohledu na vámi zvolené možnosti syntaxe šablony je stejný během počátečního nasazení a opětovné nasazení. Vytvoření a přiřazení MSI se přiřadit uživatele k nové nebo existující virtuální počítač se provádí stejným způsobem. Také ve výchozím nastavení, provede Azure Resource Manageru [přírůstkové aktualizace](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) pro nasazení:

1. Ať už místně se přihlaste do Azure nebo prostřednictvím portálu Azure portal pomocí účtu, který je přidružený k předplatnému Azure, která obsahuje instalační služby MSI a virtuálních počítačů. Také se ujistěte, že váš účet patří do role, která poskytuje oprávnění k zápisu na prostředky (například role "vlastník") nebo předplatné.

2. Po načtení šablony do editoru, vyhledejte `Microsoft.Compute/virtualMachines` prostředků zájmu v rámci `resources` oddílu. Váš může vypadat trochu jinak než na následujícím snímku obrazovky, v závislosti na používaném editoru a určuje, zda jsou úpravy šablony pro existující nebo nové nasazení.

   >[!NOTE] 
   > Tento příklad předpokládá proměnných, například `vmName`, `storageAccountName`, a `nicName` byly definovány v šabloně.
   >

   ![Snímek obrazovky se šablona – vyhledejte virtuální počítač](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Přidat `"identity"` vlastnost na stejné úrovni jako `"type": "Microsoft.Compute/virtualMachines"` vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Potom přidejte rozšíření MSI virtuálního počítače jako `resources` elementu. Použijte následující syntaxi:

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

## <a name="remove-msi-from-an-azure-vm"></a>Odebrání MSI virtuálního počítače Azure

Pokud máte virtuální počítač, který už je Instalační služba MSI:

1. Ať už místně se přihlaste do Azure nebo prostřednictvím portálu Azure portal pomocí účtu, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také se ujistěte, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači (například role "Přispěvatel virtuálních počítačů").

2. Odeberte dva prvky, které byly přidány v předchozí části: Virtuálního počítače `"identity"` vlastnost a `"Microsoft.Compute/virtualMachines/extensions"` prostředků.

## <a name="related-content"></a>Související obsah

- Širší perspektivy o MSI najdete v článku [identita spravované služby přehled](msi-overview.md).

