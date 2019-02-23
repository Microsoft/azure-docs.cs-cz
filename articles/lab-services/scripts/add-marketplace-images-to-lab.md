---
title: 'Skript prostředí PowerShell: Přidání marketplace image do testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu'
description: Tento skript Powershellu přidá marketplace image do testovacího prostředí ve službě Azure DevTest Labs.
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
ms.openlocfilehash: 22c30e1c3b19e62489a08a702d8b35fbc4862e13
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728212"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Použití Powershellu k přidání do testovacího prostředí ve službě Azure DevTest Labs imagí marketplace

Tento ukázkový skript Powershellu přidá marketplace image do testovacího prostředí ve službě Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky
* **A lab**. Skript je potřeba mít existující testovací prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Find-AzResource](/powershell/module/az.resources/find-azresource) | Vyhledá prostředky podle zadaných parametrů. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Získá prostředky. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Upraví prostředek. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Vytvořte prostředek. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty Azure Lab Services Powershellu najdete v [ukázky Azure Lab Services Powershellu](../samples-powershell.md).
