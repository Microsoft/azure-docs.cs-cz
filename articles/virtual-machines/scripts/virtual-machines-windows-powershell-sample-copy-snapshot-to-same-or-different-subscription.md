---
title: Azure ukázkový skript prostředí PowerShell - kopírování (přesunout) snímek spravovaných disků na stejný nebo jiný předplatné | Microsoft Docs
description: Azure ukázkový skript prostředí PowerShell - kopírování (přesunout) snímek spravovaných disků na stejný nebo jiný odběr
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 064b5355da10fe683563fa078cfafc65080f7ea2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23879656"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopírování snímku spravovaného disku v rámci stejného předplatného nebo jiného předplatného pomocí prostředí PowerShell

Tento skript vytvoří kopii snímku ve stejné stejného předplatného nebo jiné předplatné. Pomocí tohoto skriptu přesunout do jiného předplatného pro uchovávání dat snímku. Ukládání snímků v jiném předplatném. můžete chránit před nechtěným odstraněním snímků ve vašem předplatném hlavní. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy pro vytvoření snímku v cílové předplatné pomocí Id zdrojové snímku. Každý příkaz v tabulce odkazy na dokumentaci konkrétní příkaz.

| Příkaz | Poznámky |
|---|---|
| [Nové AzureRmSnapshotConfig](/powershell/module/azurerm.compute/New-AzureRmSnapshotConfig) | Vytvoří snímek konfigurace, který se používá pro vytvoření snímku. Obsahuje Id nadřazené snímku a umístění, která je stejná jako nadřazená snímku prostředku.  |
| [Nové AzureRmSnapshot](/powershell/module/azurerm.compute/New-AzureRmDisk) | Vytvoří snímek pomocí snímek konfigurace, název snímku a předány jako parametry název skupiny prostředků. |


## <a name="next-steps"></a>Další kroky

[Vytvoření virtuálního počítače ze snímku](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Další informace o modulu Azure PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/overview).

Ukázky skriptu PowerShell další virtuální počítač nachází v [virtuálního počítače Windows Azure dokumentaci](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).