---
title: PowerShell – vytvoření vlastní role v testovacím prostředí v Azure DevTest Labs
description: Tento článek poskytuje skript Azure PowerShell, který přidá externího uživatele do testovacího prostředí v Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 9605b3c63877335b8f180ae5fd2b06edc1601096
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760441"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Použití PowerShellu k vytvoření vlastní role v testovacím prostředí v Azure DevTest Labs

Tento ukázkový skript PowerShellu vytvoří vlastní roli pro použití v testovacím prostředí v Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Požadavky
* **A lab**. Tento skript vyžaduje, abyste měli existující testovací prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Načte operace pro poskytovatele prostředků Azure, které jsou zabezpečené pomocí služby Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Zobrazí seznam všech rolí Azure RBAC, které jsou k dispozici pro přiřazení. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Vytvoří vlastní roli. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další Azure Lab Services ukázkových skriptech PowerShellu najdete v [ukázkách Azure Lab Services PowerShellu](../samples-powershell.md).
