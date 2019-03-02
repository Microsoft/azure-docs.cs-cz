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
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: 6164a92e19d8657525029bca9a749baadcb49362
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242883"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopírování snímku spravovaného disku do stejného nebo jiného předplatného pomocí PowerShellu

Tento skript zkopíruje snímek spravovaného disku do stejného nebo jiného předplatného. Tento skript použijte v následujících scénářích:

1. Migrace snímku ve službě storage úrovně Premium (Premium_LRS) do úložiště úrovně Standard (Standard_LRS nebo Standard_ZRS) sníží vaše náklady.
1. Migrace snímek z místně redundantní úložiště (Premium_LRS, Standard_LRS) do zónově redundantní úložiště (Standard_ZRS), abyste využili výhod vyšší spolehlivost úložiště ZRS.
1. Přesunete snímek do jiného předplatného ve stejné oblasti pro delší dobu uchování.

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

[Vytvoření virtuálního počítače ze snímku](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
