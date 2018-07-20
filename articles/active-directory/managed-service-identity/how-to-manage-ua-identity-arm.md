---
title: Jak vytvářet a odstraňovat uživatele přiřazeny identita spravované služby pomocí Azure Resource Manageru
description: Pokyny, jak vytvářet a odstraňovat uživatele krok za krokem přiřazené Identity spravované služby pomocí Azure Resource.
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
ms.openlocfilehash: ced2a8354e63288ad9957b6a177b43c97b58698c
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160531"
---
# <a name="create-list-and-delete-a-user-assigned-identity-using-azure-resource-manager"></a>Vytváření, výpisu a odstranění identity přiřazené uživateli, pomocí Azure Resource Manageru

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje služby Azure s využitím spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku vytvoříte uživatele přiřazeny spravovanou identitu pomocí Azure Resource Manageru.

Není možné vypsat a odstranit uživatele přiřazeny identity pomocí šablony Azure Resource Manageru.  Naleznete v následujících článcích a vytvoření seznamu identity přiřazené uživateli:

- [Seznam identity přiřazené uživateli](how-to-manage-ua-identity-cli.md#list-user-assigned-identities)
- [Odstranit identity přiřazené uživateli](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-identity)
## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

Ať už místně se přihlaste do Azure nebo prostřednictvím portálu Azure portal pomocí účtu, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také se ujistěte, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači (například role "Přispěvatel virtuálních počítačů").

## <a name="template-creation-and-editing"></a>Vytváření šablon a úpravy

Jak s Azure portal a vytváření skriptů, šablon Azure Resource Manageru umožňují nasazovat nové nebo upravené zdroje, které jsou definované ve skupině prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místních i založené na portálu, včetně:

- Použití [vlastní šablonu z Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), která umožňuje vytvoření zcela nové šablony, nebo ji založit na existující běžné nebo [šablonu pro rychlý Start](https://azure.microsoft.com/documentation/templates/).
- Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo [aktuální stav nasazení](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Pomocí místní [editor JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a nahrání a nasazení pomocí Powershellu nebo rozhraní příkazového řádku.
- Pomocí sady Visual Studio [projekt skupiny prostředků Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu. 

## <a name="create-a-user-assigned-identity"></a>Vytvoření identity přiřazené uživatelem 

Pokud chcete vytvořit identity přiřazené uživateli, pomocí následující šablony. Minimálně je třeba přiřadit svůj účet [Přispěvatel spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role pro vytvoření identity přiřazené uživateli. Nahradit `<USER ASSIGNED IDENTITY NAME>` hodnoty vlastními hodnotami:

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
## <a name="related-content"></a>Související obsah

Informace o tom, jak přiřadit identity přiřazené uživateli na Virtuálním počítači Azure pomocí Azure Resource Manageru šablony naleznete v tématu [konfigurace virtuálních počítačů spravovaná identita služby s použitím šablony](qs-configure-template-windows-vm.md).


 
