---
title: 'Skript prostředí PowerShell: Sada povolené velikosti virtuálního počítače v Azure Lab Services | Dokumentace Microsoftu'
description: Tento skript Powershellu nastaví povolené velikosti virtuálních počítačů v Azure Lab Services.
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
ms.openlocfilehash: 559e74675a5d113584dca21979c20462c9cdf19c
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054702"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Použití Powershellu k nastavení povolené velikosti virtuálního počítače v Azure Lab Services

Tento ukázkový skript Powershellu nastaví povolené velikosti virtuálního počítače (VM) v Azure Lab Services.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky
* **Testovací prostředí**. Skript je potřeba mít existující testovací prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) | Vyhledá prostředky podle zadaných parametrů. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Získá prostředky. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Upraví prostředek. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Vytvořte prostředek. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty Azure Lab Services Powershellu najdete v [ukázky Azure Lab Services Powershellu](../samples-powershell.md).
