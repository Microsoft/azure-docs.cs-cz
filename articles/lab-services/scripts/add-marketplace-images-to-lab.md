---
title: PowerShell – přidání image Marketplace do testovacího prostředí v Azure DevTest Labs
description: Tento skript PowerShellu přidá image Marketplace do testovacího prostředí v Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: d2f5d6b6b9500ccd90630e8920c09340658fb76f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100175"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Použití PowerShellu k přidání image Marketplace do testovacího prostředí v Azure DevTest Labs

Tento ukázkový skript PowerShellu přidá image Marketplace do testovacího prostředí v Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadované součásti
* **Testovací prostředí**. Tento skript vyžaduje, abyste měli existující testovací prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| Najít – AzResource | Vyhledá prostředky na základě zadaných parametrů. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Získá prostředky. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Upraví prostředek. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Vytvořit prostředek. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další Azure Lab Services ukázkových skriptech PowerShellu najdete v [ukázkách Azure Lab Services PowerShellu](../samples-powershell.md).
