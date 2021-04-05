---
title: Vytvoření & odstranění spravované identity přiřazené uživatelem pomocí Azure Resource Manager
description: Podrobné pokyny, jak vytvořit a odstranit spravované identity přiřazené uživatelem pomocí Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: acbeb8e0fc638955995cc5aaf00f58d40adc69a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184871"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Vytvoření, vypsání a odstranění spravované identity přiřazené uživatelem pomocí Azure Resource Manager


Spravované identity pro prostředky Azure poskytují služby Azure se spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro služby, které podporují ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku vytvoříte spravovanou identitu přiřazenou uživatelem pomocí Azure Resource Manager.

Pomocí šablony Azure Resource Manager není možné vypsat a odstranit spravovanou identitu přiřazenou uživatelem.  Chcete-li vytvořit a uvést spravovanou identitu přiřazenou uživatelem, Projděte si následující články:

- [Výpis spravované identity přiřazené uživatelem](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Odstranit spravovanou identitu přiřazenou uživatelem](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#managed-identity-types)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="template-creation-and-editing"></a>Vytváření a úpravy šablon

Stejně jako u Azure Portal a skriptování poskytují Azure Resource Manager šablony možnost nasazení nových nebo upravených prostředků definovaných skupinou prostředků Azure. K dispozici je několik možností pro úpravu a nasazení šablony, včetně místních i na portálu, včetně:

- Použití [vlastní šablony z Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), což vám umožňuje vytvořit zcela novou šablonu, nebo ji založit na stávající společné nebo [rychlé šabloně](https://azure.microsoft.com/documentation/templates/).
- Odvození z existující skupiny prostředků exportováním šablony z [původního nasazení](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)nebo z [aktuálního stavu nasazení](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Použití místního [editoru JSON (například vs Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)a následného nahrávání a nasazování pomocí PowerShellu nebo rozhraní příkazového řádku.
- Použití [projektu skupiny prostředků Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) sady Visual Studio k vytvoření a nasazení šablony. 

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

K vytvoření spravované identity přiřazené uživatelem potřebuje váš účet přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

K vytvoření spravované identity přiřazené uživatelem použijte následující šablonu. Nahraďte `<USER ASSIGNED IDENTITY NAME>` hodnotu vlastními hodnotami:

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


