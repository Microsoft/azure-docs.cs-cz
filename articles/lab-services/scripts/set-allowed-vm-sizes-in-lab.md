---
title: 'Skript prostředí PowerShell: Sada povolené velikosti virtuálních počítačů v testovacím služeb Azure | Microsoft Docs'
description: Tento skript prostředí PowerShell nastaví povolené velikosti virtuálních počítačů v testovacím služeb Azure.
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
ms.openlocfilehash: 2fc7001e5594b1c37e6566cde1ee586194737e3c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Použití prostředí PowerShell k nastavení povolené velikosti virtuálních počítačů v testovacím služby Azure

Tento ukázkový skript prostředí PowerShell nastaví velikosti povolené virtuálních počítačů (VM) v prostředí služby Azure.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky
* **Vlastní testovacího prostředí**. Skript vyžaduje, abyste měli existující vlastní testovacího prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Najít AzureRmResource](/module/azurerm.resources/find-azurermresource) | Vyhledá prostředky v závislosti na zadaných parametrů. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Získá prostředky. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Upravuje prostředku. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Vytvořte prostředek. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu prostředí PowerShell služby testovacího prostředí Azure lze nalézt v [Azure PowerShell služby Lab ukázky](../samples-powershell.md).