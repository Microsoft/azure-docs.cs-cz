---
title: Vytvoření & odstranění spravované identity přiřazené uživateli pomocí Správce prostředků Azure
description: Krok za krokem pokyny, jak vytvořit a odstranit uživatelem přiřazené spravované identity pomocí Správce prostředků Azure.
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
ms.date: 12/10/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 244965da4e22c0808fd1ea9088aa182b27eaf484
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253374"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Vytvoření, vypsat a odstranit spravovanou identitu přiřazenou uživatelem pomocí Správce prostředků Azure


Spravované identity pro prostředky Azure poskytují služby Azure se spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření služeb, které podporují ověřování Azure AD, bez nutnosti pověření ve vašem kódu. 

V tomto článku vytvoříte uživatelem přiřazenou spravovanou identitu pomocí Správce prostředků Azure.

Není možné vypsat a odstranit uživatelem přiřazenou spravovanou identitu pomocí šablony Azure Resource Manager.  V následujících článcích můžete vytvořit a uvést spravovanou identitu přiřazenou uživatelem:

- [Seznam spravované identity přiřazené uživateli](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Odstranění spravované identity přiřazené uživateli](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="template-creation-and-editing"></a>Vytváření a úpravy šablon

Stejně jako u webu Azure Portal a skriptování poskytují šablony Azure Resource Manageru možnost nasazovat nové nebo upravené prostředky definované skupinou prostředků Azure. Pro úpravy a nasazení šablon je k dispozici několik možností, a to jak místní, tak na portálu, včetně:

- Použití [vlastní šablony z Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), která umožňuje vytvořit šablonu od začátku nebo ji založit na existující společné šabloně nebo [šabloně QuickStart](https://azure.microsoft.com/documentation/templates/).
- Odvození z existující skupiny prostředků exportem šablony z [původního nasazení](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)nebo z [aktuálního stavu nasazení](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Pomocí místního [editoru JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a potom nahrávání a nasazování pomocí prostředí PowerShell nebo CLI.
- Použití projektu Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) k vytvoření a nasazení šablony. 

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

Chcete-li vytvořit spravovanou identitu přiřazenou uživateli, potřebuje váš účet přiřazení role [Přispěvatel spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Chcete-li vytvořit spravovanou identitu přiřazenou uživateli, použijte následující šablonu. Nahraďte hodnotu `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Další kroky

Informace o tom, jak přiřadit uživatelem přiřazenou spravovanou identitu k virtuálnímu počítači Azure pomocí šablony Azure Resource Manageru, najdete v [tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablon](qs-configure-template-windows-vm.md).


 
