---
title: Ukázkový skript Azure PowerShellu – Kopírování (přesun) snímku spravovaného disku do stejného nebo jiného předplatného | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Kopírování (přesun) snímku spravovaného disku do stejného nebo jiného předplatného
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
ms.openlocfilehash: 8447ffc27068fbbdf5793acdc51bb9724ee41cb8
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976720"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopírování snímku spravovaného disku do stejného nebo jiného předplatného pomocí PowerShellu

Tento skript vytvoří kopii snímku ve stejném nebo jiném předplatném. Pomocí tohoto skriptu můžete přesunout snímek do jiného předplatného pro zajištění uchovávání dat. Ukládání snímků v jiném předplatném chrání před náhodným odstraněním snímků v hlavním předplatném. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření snímku v cílovém předplatném pomocí ID zdrojového snímku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Vytvoří konfiguraci snímku, která se použije při vytvoření snímku. Bude obsahovat ID prostředku nadřazeného snímku a stejné umístění, jako má nadřazený snímek.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Vytvoří snímek s použitím konfigurace snímku, názvu snímku a názvu skupiny prostředků, které se předají jako parametry. |


## <a name="next-steps"></a>Další postup

[Vytvoření virtuálního počítače ze snímku](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).