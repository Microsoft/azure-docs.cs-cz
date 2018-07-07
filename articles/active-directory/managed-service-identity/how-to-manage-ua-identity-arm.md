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
ms.openlocfilehash: 74e4c28130fb49a3cb81420913f71dfa3a7c00bf
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903965"
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
- Pokud ještě nemáte účet Azure [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.

Ať už místně se přihlaste do Azure nebo prostřednictvím portálu Azure portal pomocí účtu, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také se ujistěte, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači (například role "Přispěvatel virtuálních počítačů").

## <a name="template-creation-and-editing"></a>Vytváření šablon a úpravy

Jak s Azure portal a vytváření skriptů, šablon Azure Resource Manageru umožňují nasazovat nové nebo upravené zdroje, které jsou definované ve skupině prostředků Azure. Několik možností, jak jsou k dispozici pro úpravy šablony a nasazení, místních i založené na portálu, včetně:

- Použití [vlastní šablonu z Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), která umožňuje vytvoření zcela nové šablony, nebo ji založit na existující běžné nebo [šablonu pro rychlý Start](https://azure.microsoft.com/documentation/templates/).
- Odvozování z existující skupinu prostředků, tak, že vyexportujete šablonu buď z [původního nasazení](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), nebo [aktuální stav nasazení](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Pomocí místní [editor JSON (například VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)a nahrání a nasazení pomocí Powershellu nebo rozhraní příkazového řádku.
- Pomocí sady Visual Studio [projekt skupiny prostředků Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) jak vytvořit a nasadit šablonu. 

## <a name="create-a-user-assigned-identity"></a>Vytvoření identity přiřazené uživateli 

Pokud chcete vytvořit identity přiřazené uživateli, pomocí následující šablony. Nahradit `<USER ASSIGNED IDENTITY NAME>` hodnoty vlastními hodnotami:

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

Informace o tom, jak přiřadit identity přiřazené uživateli na Virtuálním počítači Azure pomocí Azure Resource Manageru šablony naleznete v tématu [konfigurace virtuálních počítačů spravovaná identita služby s použitím šablony](qs-configure-template-windows-vm.md).


 
