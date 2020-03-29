---
title: PowerShell – přidání image marketplace do testovacího prostředí v laboratořích Azure DevTest Labs
description: Tento skript prostředí PowerShell přidá image marketplace do testovacího prostředí v Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d699a22ed06022c6d9df12ade7f202fb304648c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166452"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Přidání image marketplace do testovacího prostředí v Azure DevTest Labs pomocí PowerShellu

Tato ukázková skript prostředí PowerShell přidá image marketplace do testovacího prostředí v azure devtest labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky
* **Laboratoř**. Skript vyžaduje, abyste měli existující testovací prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

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
