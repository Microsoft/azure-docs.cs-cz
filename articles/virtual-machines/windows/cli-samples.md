---
title: Ukázky Azure CLI windows
description: Ukázky Azure CLI windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4868254ffc0448562ff6115a4e6bb030e4353ee4
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083305"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Ukázky Azure CLI pro virtuální počítače s Windows

Následující tabulka obsahuje odkazy na bash skripty vytvořené pomocí Azure CLI, které nasazují virtuální počítače s Windows.

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač se systémem Windows s minimální konfigurací. |
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří skupinu prostředků, virtuální počítač a všechny související prostředky.|
| [Vytváření vysoce dostupných virtuálních počítačů](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří několik virtuálních počítačů ve vysoce dostupné konfiguraci s vyrovnáváním zatížení. |
| [Vytvoření virtuálního počítače a spuštění konfiguračního skriptu](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač a k instalaci služby IIS použije rozšíření Azure Custom Script. |
| [Vytvoření virtuálního počítače a spuštění konfigurace DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač a k instalaci služby IIS použije rozšíření Konfigurace požadovaného stavu Azure (DSC). |
|**Spravovat úložiště**||
| [Vytvoření spravovaného disku z VHD](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného virtuálního pevného disku jako disk operačního systému nebo z datového virtuálního pevného disku jako datového disku.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří spravovaný disk ze snímku. |
| [Kopírování spravovaného disku na stejné nebo jiné předplatné](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Zkopíruje spravovaný disk do stejného nebo jiného předplatného, ale ve stejné oblasti jako nadřazený spravovaný disk. 
| [Export snímku jako VHD do účtu úložiště](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportuje spravovaný snímek jako virtuální pevný disk do účtu úložiště v jiné oblasti. |
| [Export VHD spravovaného disku do účtu úložiště](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportuje základní virtuální pevný disk spravovaného disku do účtu úložiště v jiné oblasti. |
| [Kopírování snímku do stejného nebo jiného předplatného](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Zkopíruje snímek na stejné nebo jiné předplatné, ale ve stejné oblasti jako nadřazený snímek. |
|**Síťové virtuální počítače**||
| [Zabezpečení provozu sítě mezi virtuálními počítači](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří dva virtuální počítače, všechny související prostředky a skupiny zabezpečení interní a externí sítě (NSG). |
|**Zabezpečení virtuálních počítačů**||
| [Šifrování virtuálního počítače a datových disků](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří trezor klíčů Azure, šifrovací klíč a instanční objekt a pak zašifruje virtuální počítač. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálního počítače pomocí Azure Monitoru](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta Analýzy protokolů a zapíše virtuální počítač do pracovního prostoru Analýzy protokolů.  |
| | |
