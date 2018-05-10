---
title: 'Skript prostředí PowerShell: Přidat do vlastní testovacího prostředí v prostředí služby Azure image pořízenou prostřednictvím marketplace | Microsoft Docs'
description: Tento skript prostředí PowerShell přidá image pořízenou prostřednictvím marketplace vlastní testovacího prostředí v prostředí služby Azure.
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
ms.openlocfilehash: 64d168c132edce4ecd128b795fbfa5ab2607cb19
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-custom-lab"></a>Přidání image pořízenou prostřednictvím marketplace do vlastní testovacím prostředí pomocí prostředí PowerShell

Tento ukázkový skript prostředí PowerShell přidá image pořízenou prostřednictvím marketplace vlastní testovacího prostředí v prostředí služby Azure.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky
* **Vlastní testovacího prostředí**. Skript vyžaduje, abyste měli existující vlastní testovacího prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a custom lab")]

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