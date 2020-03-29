---
title: 'Skript PowerShellu: Nastavení povolených velikostí virtuálních počítačů ve službách Azure Lab Services | Dokumenty společnosti Microsoft'
description: Tento článek obsahuje ukázkový skript PowerShellu, který nastavuje povolené velikosti virtuálních počítačů (VM) ve službě Azure Lab Services.
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
ms.openlocfilehash: a1b0e9a4aed475f04ec8dcffa9bc95b7c7c713e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760442"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Nastavení povolených velikostí virtuálních počítačů ve službě Azure Lab Services pomocí PowerShellu

Tato ukázková sada skriptů PowerShellu povolila velikost virtuálních počítačů ve službě Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Požadavky
* **Laboratoř**. Skript vyžaduje, abyste měli existující testovací prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| Najít-AzResource | Vyhledá prostředky na základě zadaných parametrů. |
| [Get-AzZdroj](/powershell/module/az.resources/get-azresource) | Získá prostředky. |
| [Zdroj set-az](/powershell/module/az.resources/set-azresource) | Upraví prostředek. |
| [Nový-AzZdroj](/powershell/module/az.resources/new-azresource) | Vytvořit prostředek. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu PowerShellu azure lab services najdete ve [vzorcích prostředí PowerShell azure lab services](../samples-powershell.md).
