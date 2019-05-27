---
title: 'Skript prostředí PowerShell: Přidání externího uživatele do testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu'
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
ms.openlocfilehash: 042fa1e24ebadfb00a2d55cc97d742f198cb5662
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160610"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Použití Powershellu k přidání externího uživatele do testovacího prostředí ve službě Azure DevTest Labs

Tento ukázkový skript Powershellu přidá externího uživatele do testovacího prostředí ve službě Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky
* **A lab**. Skript je potřeba mít existující testovací prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Počet opakování objekt uživatele ze služby Azure active directory. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Přiřadí zadané role zadaného objektu zabezpečení v zadaném oboru. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty Azure Lab Services Powershellu najdete v [ukázky Azure Lab Services Powershellu](../samples-powershell.md).
