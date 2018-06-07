---
title: 'Skript prostředí PowerShell: vytvoření vlastní role v testovacím prostředí v Azure DevTest Labs | Microsoft Docs'
description: Tento skript prostředí PowerShell přidá do testovacího prostředí v Azure DevTest Labs externího uživatele.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 295f742342fba7d77b556724c8005f3ac4816482
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636711"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Pomocí prostředí PowerShell vytvořit vlastní roli v testovacím prostředí v Azure DevTest Labs

Tento ukázkový skript prostředí PowerShell vytvoří vlastní role pro použití v testovacím prostředí v Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky
* **Testovacího prostředí**. Skript, musíte mít existující testovacího prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) | Získá operace pro poskytovatele prostředků Azure, které jsou zabezpečené pomocí Azure RBAC. |
| [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) | Vypíše všechny role Azure RBAC, které jsou k dispozici pro přiřazení. |
| [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) | Vytvoří vlastní roli. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu prostředí PowerShell služby testovacího prostředí Azure lze nalézt v [Azure PowerShell služby Lab ukázky](../samples-powershell.md).