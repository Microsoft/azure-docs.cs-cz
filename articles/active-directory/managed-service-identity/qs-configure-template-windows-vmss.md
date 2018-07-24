---
title: Konfigurace MSI na škálovací sadu pomocí šablony Azure virtuálních počítačů
description: Podrobné pokyny ke konfiguraci Identity spravované služby (MSI) na VMSS Azure pomocí šablony Azure Resource Manageru.
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
ms.openlocfilehash: babeb0eb930d865cf519ddb45c651a3d77265665
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215789"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Konfigurace VMSS identita spravované služby pomocí šablony

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak provádět následující operace Identity spravované služby ve VMSS Azure pomocí šablony nasazení Azure Resource Manageru:
- Povolit nebo zakázat identitu na Azure VMSS přiřazenou systémem
- Přidávat a odebírat uživatele přiřazeny identitu na Azure VMSS

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

Bez ohledu na vámi zvolené možnosti syntaxe šablony je stejný během počátečního nasazení a opětovné nasazení. Povolení MSI nového nebo existujícího virtuálního počítače se provádí stejným způsobem. Také ve výchozím nastavení, provede Azure Resource Manageru [přírůstkové aktualizace](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) až po nasazení.

## <a name="system-assigned-identity"></a>Identitu přiřazenou systémem

V této části se povolí a zakáže systém přiřadil identity pomocí šablony Azure Resource Manageru.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Povolit během vytváření Azure VMSS nebo existující VMSS Azure identitu přiřazenou systémem

1. Načíst šablonu do editoru, vyhledejte `Microsoft.Compute/virtualMachineScaleSets` prostředků zájmu v rámci `resources` oddílu. Váš může vypadat trochu jinak než na následujícím snímku obrazovky, v závislosti na používaném editoru a určuje, zda jsou úpravy šablony pro existující nebo nové nasazení.
   
   ![Snímek obrazovky se šablona – vyhledejte virtuální počítač](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. Chcete-li povolit identitu přiřazenou systémem, přidejte `"identity"` vlastnost na stejné úrovni jako `"type": "Microsoft.Compute/virtualMachineScaleSets"` vlastnost. Použijte následující syntaxi:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (Volitelné) Přidat škálovací sady virtuálních počítačů rozšíření MSI jako `extensionsProfile` elementu. Tento krok je volitelný, i identitu služby Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také.  Použijte následující syntaxi:

   >[!NOTE] 
   > V následujícím příkladu se předpokládá škálovací sadu virtuálních počítačů Windows nastavit rozšíření (`ManagedIdentityExtensionForWindows`) se nasazuje. Můžete také nakonfigurovat pro Linux s použitím `ManagedIdentityExtensionForLinux` namísto toho `"name"` a `"type"` elementy.
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

4. Až budete hotovi, vaše šablona by měl vypadat nějak takto:

   ![Snímek obrazovky po aktualizaci šablony](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázat identitu přiřazenou systémem ze škálovací sady virtuálních počítačů Azure

> [!NOTE]
> Zakáže identita spravované služby z virtuálního počítače není aktuálně podporováno. Do té doby můžete přepínat mezi použitím systém přiřadil a přiřazené identity uživatele.

Pokud máte virtuální počítač škálovací sadu, která už potřebuje identitu přiřazenou systémem, ale stále potřebuje identity přiřazené uživateli:

- Načíst šablonu do editoru a změňte typ identity na `'UserAssigned'`

## <a name="user-assigned-identity"></a>Identity přiřazené uživateli

V této části můžete přiřadit identity přiřazené uživateli VMSS Azure pomocí šablony Azure Resource Manageru.

> [!Note]
> Vytvoření identity přiřazené uživateli pomocí šablony Azure Resource Manageru, najdete v článku [vytvoření identity přiřazené uživateli](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Přiřadit uživatele identity přiřazené do Azure VMSS

1. V části `resources` prvku, přidejte následující položku identity přiřazené uživateli přiřadit vaše VMSS.  Nezapomeňte nahradit `<USERASSIGNEDIDENTITY>` s názvem identity přiřazené uživateli jste vytvořili.

   > [!Important]
   > `<USERASSIGNEDIDENTITYNAME>` Hodnoty zobrazené v následujícím příkladu musí být uložen v proměnné.  Aktuálně se podporují provádění přiřazení uživatelsky přiřazených identit k virtuálnímu počítači v šabloně Resource Manageru také verze rozhraní api musí odpovídat verzi v následujícím příkladu. 

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
3.  Jakmile budete hotovi, vaše šablona by měl vypadat nějak takto:
   
      ![Snímek obrazovky identity přiřazené uživateli](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>Další postup

- Širší perspektivy o MSI najdete v článku [identita spravované služby přehled](overview.md).

