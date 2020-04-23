---
title: 'PowerShellový skript: nastavte povolené velikosti virtuálních počítačů v Azure Lab Services | Microsoft Docs'
description: Tento článek obsahuje ukázkový skript PowerShellu, který nastavuje povolené velikosti virtuálních počítačů v Azure Lab Services.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 5b3dbee7d0ac928c4f18f25348e714aba9c1cd13
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100766"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Nastavení povolených velikostí virtuálních počítačů v Azure Lab Services pomocí PowerShellu

Tento ukázkový skript PowerShellu nastavuje povolené velikosti virtuálních počítačů v Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Požadované součásti
* **Testovací prostředí**. Tento skript vyžaduje, abyste měli existující testovací prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

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
