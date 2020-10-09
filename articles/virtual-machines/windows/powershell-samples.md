---
title: Ukázky PowerShellu pro virtuální počítače Azure
description: Ukázky PowerShellu pro virtuální počítače Azure
author: cynthn
ms.service: virtual-machines-windows
ms.topic: sample
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e8489ecb9343cf70bd8523aa45f04a6bde171b98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89321748"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Ukázky PowerShellu pro virtuální počítače Azure

Následující tabulka obsahuje odkazy na ukázky skriptů PowerShellu pro vytváření a správu virtuálních počítačů (VM) Windows.

| Skript | Popis |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Rychlé vytvoření virtuálního počítače](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří skupinu prostředků, virtuální počítač a všechny související prostředky s minimálními výzvami.|
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří skupinu prostředků, virtuální počítač a všechny související prostředky.|
| [Vytvoření virtuálního počítače s vysokou dostupností](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří několik virtuálních počítačů v konfiguraci s vysokou dostupností a s vyrovnáváním zatížení.|
| [Vytvoření virtuálního počítače a spuštění konfiguračního skriptu](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření vlastních skriptů Azure nainstaluje službu IIS. |
| [Vytvoření virtuálního počítače a spuštění konfigurace DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření Azure požadovaná konfigurace stavu (DSC) nainstaluje službu IIS. |
| [Nahrání virtuálního pevného disku a vytvoření virtuálních počítačů](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Nahraje místní soubor VHD do Azure, vytvoří z virtuálního pevného disku image a pak z této image vytvoří virtuální počítač. |
| [Vytvoření virtuálního počítače ze spravovaného disku s operačním systémem](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač připojením existujícího spravovaného disku jako disku s operačním systémem. |
| [Vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač ze snímku vytvořením spravovaného disku ze snímku a následným připojením nového spravovaného disku jako disku s operačním systémem. |
|**Spravovat úložiště**||
| [Vytvoření spravovaného disku z VHD ve stejném nebo jiném předplatném](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného VHD jako disku s operačním systémem nebo z datového virtuálního pevného disku jako datový disk ve stejném nebo jiném předplatném.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří ze snímku spravovaný disk. |
| [Kopírování spravovaného disku do stejného nebo jiného předplatného](../scripts/virtual-machines-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Zkopíruje spravovaný disk do stejného nebo jiného předplatného, které je ve stejné oblasti jako nadřazený spravovaný disk.
| [Export snímku jako VHD do účtu úložiště](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportuje spravovaný snímek jako virtuální pevný disk do účtu úložiště v jiné oblasti. |
| [Export VHD spravovaného disku do účtu úložiště](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportuje základní virtuální pevný disk spravovaného disku do účtu úložiště v jiné oblasti. |
| [Vytvoření snímku z VHD](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří snímek z VHD a pak pomocí tohoto snímku rychle vytvoří několik identických spravovaných disků.  |
| [Kopírování snímku do stejného nebo jiného předplatného](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Zkopíruje snímek do stejného nebo jiného předplatného, které je ve stejné oblasti jako nadřazený snímek. |
|**Zabezpečené virtuální počítače**||
| [Šifrování virtuálního počítače a jeho datových disků](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Vytvoří Trezor klíčů Azure, šifrovací klíč a instanční objekt a potom zašifruje virtuální počítač. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálního počítače pomocí Azure Monitor](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta Azure Log Analytics a zaregistruje virtuální počítač v pracovním prostoru Log Analytics.  |
| [Shromažďování podrobností o všech virtuálních počítačích v rámci předplatného s využitím PowerShellu](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří sdílený svazek clusteru, který obsahuje název virtuálního počítače, název skupiny prostředků, oblast, Virtual Network, podsíť, privátní IP adresu, typ operačního systému a veřejnou IP adresu virtuálních počítačů v zadaném předplatném.
| | |
