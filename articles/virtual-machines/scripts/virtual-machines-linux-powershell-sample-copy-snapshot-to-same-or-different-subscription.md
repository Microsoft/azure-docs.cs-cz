---
title: Ukázkový skript Azure PowerShellu – Kopírování (přesun) snímku spravovaného disku do stejného nebo jiného předplatného | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Kopírování (přesun) snímku spravovaného disku do stejného nebo jiného předplatného
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: f7dbda75eaf403e9bd99dce63e5ed37118fd83ea
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081371"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopírování snímku spravovaného disku do stejného nebo jiného předplatného pomocí PowerShellu

Tento skript zkopíruje snímek spravovaného disku do stejného nebo jiného předplatného. Tento skript použijte pro následující scénáře:

1. Migrujte snímek ve službě Premium Storage (Premium_LRS) do úložiště úrovně Standard (Standard_LRS nebo Standard_ZRS), abyste snížili náklady.
1. Migrujte snímek z místně redundantního úložiště (Premium_LRS, Standard_LRS) do zóny redundantního úložiště (Standard_ZRS), abyste využili vyšší spolehlivosti úložiště ZRS.
1. Přesunutí snímku do jiného předplatného ve stejné oblasti pro delší dobu uchování.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření snímku v cílovém předplatném pomocí ID zdrojového snímku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Vytvoří konfiguraci snímku, která se použije při vytvoření snímku. Bude obsahovat ID prostředku nadřazeného snímku a stejné umístění, jako má nadřazený snímek.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Vytvoří snímek s použitím konfigurace snímku, názvu snímku a názvu skupiny prostředků, které se předají jako parametry. |

## <a name="next-steps"></a>Další postup

[Vytvoření virtuálního počítače ze snímku](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).