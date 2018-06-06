---
title: Jak vytvářet a odstraňovat uživatele přiřazené identita spravované služby pomocí Azure Resource Manager
description: Podrobné pokyny, jak vytvářet a odstraňovat uživatele přiřazené identita spravované služby pomocí prostředků Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: ce8221cd7bf427084e63f8b13dcf6f0f1cc7a35e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698998"
---
# <a name="create-list-and-delete-a-user-assigned-identity-using-azure-resource-manager"></a>Vytvoření seznamu a odstraňte identitu uživatele přiřazené pomocí Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje Azure služby spravovanou identitu ve službě Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku vytvořit uživatele přiřazené spravované identity pomocí Azure Resource Manageru.

Není možné zobrazit seznam a odstraňovat uživatele přiřazené identity pomocí šablony Azure Resource Manager.  Najdete v následujících článcích a vytvořit seznam identitu uživatele přiřazené:

- [Seznam přiřazeného identity uživatele](how-to-manage-ua-identity-cli.md#list-user-assigned-identities)
- [Odstranit identitu uživatele přiřazené](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-identity)
## <a name="prerequisites"></a>Požadavky

- Pokud jste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systémem přiřazený a uživatel s přiřazenou identity](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.

Ať už místně se přihlaste k Azure nebo prostřednictvím portálu Azure pomocí účtu, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Ujistěte se také, že váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače (například role "Přispěvatel virtuálních počítačů").

## <a name="template-creation-and-editing"></a>Vytvoření šablony a úpravy

S Azure portálu a skriptování, šablony Azure Resource Manager poskytuje schopnost nasadit nové nebo upravené zdroje, které jsou definované skupiny prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místní i založené na portálu, včetně:

- Použití [vlastní šablonu z Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), který umožňuje vytvořit šablonu od začátku, nebo ji založit na existující běžné nebo [šablony rychlý Start](https://azure.microsoft.com/documentation/templates/).
- Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo z [aktuální stav nasazení](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Pomocí místní [editor JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a pak nahrání a nasazení pomocí prostředí PowerShell nebo rozhraní příkazového řádku.
- Pomocí sady Visual Studio [projekt skupiny prostředků Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu. 

## <a name="create-a-user-assigned-identity"></a>Vytvoření uživatele přiřazené identity 

Vytvoření uživatele přiřazené identity, použijte následující šablonu. Nahraďte `<USER ASSIGNED IDENTITY NAME>` hodnotu s vlastními hodnotami:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

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
      "name": "[parameters('<USER ASSIGNED IDENTITY NAME>')]",
      "apiVersion": "2015-08-31-PREVIEW",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('<USER ASSIGNED IDENTITY NAME>')]"
      }
  }
}
```
## <a name="related-content"></a>Související obsah

Informace o tom, jak přiřadit identitu uživatele přiřazené na virtuální počítač Azure pomocí šablony k Azure Resource Manageru naleznete [konfigurace Identity služby virtuálních počítačů spravovaných pomocí šablony](qs-configure-template-windows-vm.md).


 
