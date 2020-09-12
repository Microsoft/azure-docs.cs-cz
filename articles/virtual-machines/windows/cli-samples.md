---
title: Ukázky v Azure CLI – Windows
description: Ukázky v Azure CLI – Windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6c4b707a3cebc1bcdad7c9e14a96d82a8dda2371
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318790"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Ukázky Azure CLI pro virtuální počítače s Windows

Následující tabulka obsahuje odkazy na skripty bash vytvořené pomocí rozhraní příkazového řádku Azure CLI, které nasazují virtuální počítače s Windows.

| Skript | Popis |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač s Windows s minimální konfigurací. |
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří skupinu prostředků, virtuální počítač a všechny související prostředky.|
| [Vytvoření virtuálního počítače s vysokou dostupností](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří několik virtuálních počítačů s vysokou dostupností a konfigurací s vyrovnáváním zatížení. |
| [Vytvoření virtuálního počítače a spuštění konfiguračního skriptu](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření vlastních skriptů Azure nainstaluje službu IIS. |
| [Vytvoření virtuálního počítače a spuštění konfigurace DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření Azure požadovaná konfigurace stavu (DSC) nainstaluje službu IIS. |
|**Spravovat úložiště**||
| [Vytvoření spravovaného disku z VHD](../scripts/virtual-machines-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného VHD jako disku s operačním systémem nebo z datového virtuálního pevného disku jako datový disk.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří ze snímku spravovaný disk. |
| [Kopírování spravovaného disku do stejného nebo jiného předplatného](../scripts/virtual-machines-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Zkopíruje spravovaný disk do stejného nebo jiného předplatného, ale ve stejné oblasti jako nadřazený spravovaný disk. 
| [Export snímku jako VHD do účtu úložiště](../scripts/virtual-machines-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportuje spravovaný snímek jako virtuální pevný disk do účtu úložiště v jiné oblasti. |
| [Export VHD spravovaného disku do účtu úložiště](../scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportuje základní virtuální pevný disk spravovaného disku do účtu úložiště v jiné oblasti. |
| [Kopírování snímku do stejného nebo jiného předplatného](../scripts/virtual-machines-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Zkopíruje snímek do stejného nebo jiného předplatného, ale ve stejné oblasti jako nadřazený snímek. |
|**Síťové virtuální počítače**||
| [Zabezpečení provozu sítě mezi virtuálními počítači](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří dva virtuální počítače, všechny související prostředky a interní a externí skupiny zabezpečení sítě (NSG). |
|**Zabezpečené virtuální počítače**||
| [Šifrování virtuálního počítače a datových disků](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří Azure Key Vault, šifrovací klíč a instanční objekt a pak zašifruje virtuální počítač. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálního počítače pomocí Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta Log Analytics a zaregistruje virtuální počítač v pracovním prostoru Log Analytics.  |
| | |
