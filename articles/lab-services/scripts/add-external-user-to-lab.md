---
title: 'Skript prostředí PowerShell: Přidat externího uživatele do testovacího prostředí v Azure DevTest Labs | Microsoft Docs'
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
ms.openlocfilehash: bd86e44c21ca3c0b9c061f9a5c24bd19c4b207f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636439"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Přidat externího uživatele do testovacího prostředí v Azure DevTest Labs pomocí prostředí PowerShell

Tento ukázkový skript prostředí PowerShell přidá do testovacího prostředí v Azure DevTest Labs externího uživatele. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky
* **Testovacího prostředí**. Skript, musíte mít existující testovacího prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | Opakuje objekt uživatele ze služby Azure active directory. |
| [New-AzureRmRoleAssignment](/module/azurerm.resources/new-azurermroleassignment) | Zadaná role přiřadí objekt zadaný v zadaném oboru. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu prostředí PowerShell služby testovacího prostředí Azure lze nalézt v [Azure PowerShell služby Lab ukázky](../samples-powershell.md).