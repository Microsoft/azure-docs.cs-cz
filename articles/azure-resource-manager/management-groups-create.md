---
title: Vytvoření skupin pro správu k uspořádání prostředků Azure | Dokumentace Microsoftu
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
ms.date: 07/09/2018
ms.author: rithorn
ms.openlocfilehash: f2b596b34aa18d20fa888ad40e82eccb90d5fd8c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38465767"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Vytvoření skupin pro správu pro organizaci poskytující prostředky a správu
Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů napříč několika předplatnými. Vytvoření těchto kontejnerů k sestavení účinný a efektivní hierarchie, který lze použít s [Azure Policy](../azure-policy/azure-policy-introduction.md) a [řízení přístupu na základě Role Azure](../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků se skupinami pro správu Azure ](management-groups-overview.md). 

Funkce skupiny správy je dostupná ve verzi public preview. Pokud chcete začít používat správu ve skupině, přihlaste se k [webu Azure portal](https://portal.azure.com) nebo můžete použít [prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available), nebo [rozhraní REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) do Vytvoření skupin pro správu.   

První skupiny pro správu, vytvoří se v adresáři může trvat až 15 minut. Existují procesy, které se při prvním přístupu k nastavení služby správy skupin v rámci Azure pro svůj adresář. Po dokončení procesu, dostanete oznámení.  

## <a name="how-to-create-a-management-group"></a>Jak vytvořit skupinu pro správu
Skupina pro správu můžete vytvořit pomocí portálu, Powershellu nebo rozhraní příkazového řádku Azure.

### <a name="create-in-portal"></a>Vytvořit na portálu

1. Přihlaste se na [Azure Portal](http://portal.azure.com).
2. Vyberte **všechny služby** > **skupin pro správu**.
3. Na hlavní stránce vyberte **skupiny pro správu nový.** 

    ![Hlavní skupiny](media/management-groups/main.png) 
4.  Vyplňte pole ID skupiny správy. 
    - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odeslání příkazů na tuto skupinu pro správu. Tento identifikátor není po vytvoření upravit, protože se používá v rámci systému Azure k identifikaci této skupiny. 
    - Pole zobrazovaného názvu je název, který se zobrazí na portálu Azure portal. Samostatné zobrazované jméno je volitelné pole při vytváření rozhraní pro správu skupin a můžete kdykoli změnit.  

    ![Vytvořit](media/management-groups/create_context_menu.png)  
5.  Vyberte **uložit**


### <a name="create-in-powershell"></a>Vytvořit v prostředí PowerShell
V rámci prostředí PowerShell pomocí rutiny Add-AzureRmManagementGroups.   

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso 
```
**GroupName** vytváří jedinečný identifikátor. Toto ID používá další příkazy odkazují na tuto skupinu a není možné později změnit.

Pokud jste chtěli zobrazit na webu Azure portal jiný název skupiny pro správu, měli byste přidat **DisplayName** parametr s řetězcem. Například Kdybyste chtěli vytvořit skupinu pro správu s GroupName Contoso a zobrazovaný název "Skupina společnosti Contoso", můžete využít následující rutinu: 

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
``` 
Použití **ParentId** vytvořit parametr má tuto skupinu pro správu v rámci různých pro správu.  

### <a name="create-in-azure-cli"></a>Vytvořit v Azure CLI
V Azure CLI, použijte az účet-skupiny pro správu, vytvořit příkaz. 

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
``` 

---

## <a name="next-steps"></a>Další postup 
Další informace o skupinách správy, naleznete v tématu: 
- [Uspořádání prostředků se skupinami pro správu Azure ](management-groups-overview.md)
- [Jak změnit, odstranit nebo Správa skupin pro správu](management-groups-manage.md)
- [Instalace modulu Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Projděte si specifikace rozhraní API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Nainstalujte rozšíření Azure CLI](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
