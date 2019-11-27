---
title: Vytvoření & odstranění spravované identity přiřazené uživatelem pomocí Azure Resource Manager
description: Podrobné pokyny, jak vytvořit a odstranit spravované identity přiřazené uživatelem pomocí Azure Resource Manager.
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
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66a4415203871c8397af2b8f1f96ca4f08d6bfcc
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547439"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Vytvoření, vypsání a odstranění spravované identity přiřazené uživatelem pomocí Azure Resource Manager

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytují služby Azure se spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro služby, které podporují ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku vytvoříte spravovanou identitu přiřazenou uživatelem pomocí Azure Resource Manager.

Pomocí šablony Azure Resource Manager není možné vypsat a odstranit spravovanou identitu přiřazenou uživatelem.  Chcete-li vytvořit a uvést spravovanou identitu přiřazenou uživatelem, Projděte si následující články:

- [Výpis spravované identity přiřazené uživatelem](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Odstranit spravovanou identitu přiřazenou uživatelem](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="template-creation-and-editing"></a>Vytváření a úpravy šablon

Stejně jako u Azure Portal a skriptování poskytují Azure Resource Manager šablony možnost nasazení nových nebo upravených prostředků definovaných skupinou prostředků Azure. K dispozici je několik možností pro úpravu a nasazení šablony, včetně místních i na portálu, včetně:

- Použití [vlastní šablony z Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), což vám umožňuje vytvořit zcela novou šablonu, nebo ji založit na stávající společné nebo [rychlé šabloně](https://azure.microsoft.com/documentation/templates/).
- Odvození z existující skupiny prostředků exportováním šablony z [původního nasazení](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)nebo z [aktuálního stavu nasazení](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Použití místního [editoru JSON (například vs Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a následného nahrávání a nasazování pomocí PowerShellu nebo rozhraní příkazového řádku.
- Použití [projektu skupiny prostředků Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) sady Visual Studio k vytvoření a nasazení šablony. 

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

K vytvoření spravované identity přiřazené uživatelem potřebuje váš účet přiřazení role [Přispěvatel spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

K vytvoření spravované identity přiřazené uživatelem použijte následující šablonu. Hodnotu `<USER ASSIGNED IDENTITY NAME>` nahraďte vlastními hodnotami:

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

Informace o přiřazení spravované identity přiřazené uživatelem k virtuálnímu počítači Azure pomocí šablony Azure Resource Manager najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablon](qs-configure-template-windows-vm.md).


 
