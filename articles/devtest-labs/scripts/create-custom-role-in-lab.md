---
title: PowerShell – vytvoření vlastní role v testovacím prostředí v Azure DevTest Labs
description: Tento článek poskytuje skript Azure PowerShell, který přidá externího uživatele do testovacího prostředí v Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 9b6e4d47babd17916e31ab2762ec87f6aa433da6
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136197"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Použití PowerShellu k vytvoření vlastní role v testovacím prostředí v Azure DevTest Labs

Tento ukázkový skript PowerShellu vytvoří vlastní roli pro použití v testovacím prostředí v Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Požadavky
* **Testovací prostředí**. Tento skript vyžaduje, abyste měli existující testovací prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Načte operace pro poskytovatele prostředků Azure, které jsou zabezpečené pomocí služby Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Zobrazí seznam všech rolí Azure, které jsou k dispozici pro přiřazení. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Vytvoří vlastní roli. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).

Další Azure Lab Services ukázkových skriptech PowerShellu najdete v [ukázkách Azure Lab Services PowerShellu](../samples-powershell.md).
