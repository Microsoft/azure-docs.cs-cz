---
title: Jak vytvářet a odstraňovat uživatelsky přiřazené spravovanou identitu pomocí Azure Resource Manageru
description: Podrobné pokyny o tom, jak vytvářet a odstraňovat uživatelsky přiřazené spravované identit pomocí Azure Resource Manageru.
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
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 9e1ea4e35c1d8b90aa3d0fdf5e619f7b7f7db400
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106781"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Vytváření, výpisu a odstranění uživatelsky přiřazené spravovanou identitu pomocí Azure Resource Manageru

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure s využitím spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku vytvoříte uživatelsky přiřazené spravovanou identitu pomocí Azure Resource Manageru.

Není možné vypsat a odstranit spravované identity přiřazené uživateli pomocí šablony Azure Resource Manageru.  Viz následující články k vytvoření a seznam uživatel přiřazenou se identita spravované:

- [Seznam uživatelsky přiřazené identity spravované](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Odstranit uživatelsky přiřazené identity spravované](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací v tomto článku, potřebuje následující přiřazení role:
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření, čtení (list), aktualizovat a odstranit spravované identity přiřazené uživateli.

## <a name="template-creation-and-editing"></a>Vytváření šablon a úpravy

Jak s Azure portal a vytváření skriptů, šablon Azure Resource Manageru umožňují nasazovat nové nebo upravené zdroje, které jsou definované ve skupině prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místních i založené na portálu, včetně:

- Použití [vlastní šablonu z Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), která umožňuje vytvoření zcela nové šablony, nebo ji založit na existující běžné nebo [šablonu pro rychlý Start](https://azure.microsoft.com/documentation/templates/).
- Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo [aktuální stav nasazení](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Pomocí místní [editor JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a nahrání a nasazení pomocí Powershellu nebo rozhraní příkazového řádku.
- Pomocí sady Visual Studio [projekt skupiny prostředků Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu. 

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

Pro vytvoření uživatelsky přiřazené identity spravované, pomocí následující šablony. Nahradit `<USER ASSIGNED IDENTITY NAME>` hodnoty vlastními hodnotami:

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
      "apiVersion": "2015-08-31-PREVIEW",
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
## <a name="next-steps"></a>Další postup

Informace o tom, jak přiřadit uživateli přiřazena identita na Virtuálním počítači Azure pomocí Azure Resource Manageru šablony naleznete v tématu, spravované [konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí šablony](qs-configure-template-windows-vm.md).


 
