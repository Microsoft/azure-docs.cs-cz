---
title: Vytvoření skupiny pro správu k uspořádání prostředků Azure | Microsoft Docs
description: Zjistěte, jak vytvořit skupiny pro správu Azure ke správě více prostředků.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/1/2018
ms.author: rithorn
ms.openlocfilehash: 1592e47509f2537bef9cbcefd3cf49618561edcc
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Vytvoření skupiny pro správu pro organizaci poskytující prostředky a Správa
Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů v rámci více předplatných. Vytvoření těchto kontejnerů k vytvoření hierarchie efektivní a efektivní, který lze použít s [zásad Azure](../azure-policy/azure-policy-introduction.md) a [řízení přístupu na základě Role Azure](../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků se skupinami pro správu Azure ](management-groups-overview.md). 

Funkce skupiny správy je dostupná ve verzi public preview. Pokud chcete začít používat správu skupin, přihlášení k [portál Azure](https://portal.azure.com) nebo můžete použít [prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available), nebo [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) na Vytvoření skupiny pro správu.   

První skupiny pro správu vytvořené v adresáři může trvat až 15 minut. Existují procesy, které spustit při prvním nastavení služby správy skupin v rámci Azure pro váš adresář. Po dokončení procesu se zobrazí oznámení.  

## <a name="how-to-create-a-management-group"></a>Postup vytvoření skupiny pro správu
Skupina pro správu můžete vytvořit pomocí portálu, prostředí PowerShell nebo rozhraní příkazového řádku Azure.

### <a name="create-in-portal"></a>Vytvoření portálu

1. Přihlaste se [portál Azure](http://portal.azure.com).
2. Vyberte **všechny služby** > **skupin pro správu**.
3. Na hlavní stránce vyberte **skupiny pro správu nové.** 

    ![Hlavní skupiny](media/management-groups/main.png) 
4.  Vyplňte pole ID skupiny správy. 
    - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odeslání příkazů na tuto skupinu pro správu. Tento identifikátor se nedá upravovat po vytvoření, protože v celém systému Azure se používá k identifikaci této skupiny. 
    - Pole název zobrazení je název, který se zobrazí na portálu Azure. Samostatné zobrazovaný název je volitelné pole při vytváření správy skupiny a můžete kdykoli změnit.  

    ![Vytvořit](media/management-groups/create_context_menu.png)  
5.  Vyberte **uložit**


### <a name="create-in-powershell"></a>Vytvořit v prostředí PowerShell
Prostředí PowerShell pomocí rutiny Add-AzureRmManagementGroups.   

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso 
```
**GroupName** je jedinečný identifikátor vytváří. Toto ID používá jiné příkazy k odkazovat na tuto skupinu a není možné později změnit.

Pokud jste chtěli skupině pro správu zobrazíte jiný název na portálu Azure, měli byste přidat **DisplayName** parametr s řetězcem. Například pokud jste chtěli vytvořit skupinu pro správu s GroupName Contoso a zobrazovaný název "Skupina společnosti Contoso", by použijte následující rutinu: 

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
``` 
Použití **ParentId** vytvořit parametr tak, aby měl této skupiny pro správu v rámci různých pro správu.  

### <a name="create-in-azure-cli"></a>Vytvořte v Azure CLI
Do příkazového řádku Azure CLI, je použít az příkaz Vytvořit účet-skupina pro správu. 

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
``` 

---

## <a name="next-steps"></a>Další postup 
Další informace o skupinách správy najdete v tématu: 
- [Uspořádání prostředků se skupinami pro správu Azure ](management-groups-overview.md)
- [Jak změnit, odstranit nebo spravovat skupiny pro správu](management-groups-manage.md)
- [Instalace modulu Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Zkontrolujte specifikace rozhraní API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Nainstalujte rozšíření rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
