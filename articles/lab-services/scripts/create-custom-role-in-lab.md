---
title: 'Skript prostředí PowerShell: Vytvořit vlastní roli v testovacím prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu'
description: Tento skript Powershellu přidá externího uživatele do testovacího prostředí ve službě Azure DevTest Labs.
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
ms.openlocfilehash: 4d0c795dffb40ab7efec9005660439f9baef9f3f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732428"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Použití Powershellu k vytvoření vlastní roli v testovacím prostředí ve službě Azure DevTest Labs

Tento ukázkový skript Powershellu vytvoří vlastní role pro použití v testovacím prostředí ve službě Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Požadavky
* **A lab**. Skript je potřeba mít existující testovací prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Získá operace pro poskytovatele prostředků Azure, které jsou zabezpečené pomocí Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Vypíše všechny role Azure RBAC, které jsou k dispozici pro přiřazení. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Vytvoří vlastní roli. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty Azure Lab Services Powershellu najdete v [ukázky Azure Lab Services Powershellu](../samples-powershell.md).
