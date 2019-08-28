---
title: Ukázky Azure CLI Windows | Microsoft Docs
description: Ukázky v Azure CLI – Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e8d4cfbb103313d1d13ec0e08a3be82b6a75b331
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089394"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Ukázky v Azure CLI pro virtuální počítače s Windows

Následující tabulka obsahuje odkazy na skripty bash vytvořené pomocí rozhraní příkazového řádku Azure CLI, které nasazují virtuální počítače s Windows.

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač s Windows s minimální konfigurací. |
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří skupinu prostředků, virtuální počítač a všechny související prostředky.|
| [Vytvoření vysoce dostupných virtuálních počítačů](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří několik virtuálních počítačů v s vysokou dostupností a s vyrovnáváním zatížení konfigurace. |
| [Vytvoření virtuálního počítače a spuštění konfiguračního skriptu](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření vlastních skriptů Azure nainstaluje službu IIS. |
| [Vytvoření virtuálního počítače a spuštění konfigurace DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření Azure požadovaná konfigurace stavu (DSC) nainstaluje službu IIS. |
|**Správa úložiště**||
| [Vytvoření spravovaného disku z VHD](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného VHD jako disku s operačním systémem nebo z datového virtuálního pevného disku jako datový disk.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří ze snímku spravovaný disk. |
| [Kopírování spravovaného disku do stejného nebo jiného předplatného](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Zkopíruje spravovaný disk do stejného nebo jiného předplatného, ale ve stejné oblasti jako nadřazený spravovaný disk. 
| [Export snímku jako virtuálního pevného disku do účtu úložiště](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportuje spravovaný snímek jako virtuální pevný disk do účtu úložiště v jiné oblasti. |
| [Export virtuálního pevného disku spravovaného disku do účtu úložiště](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportuje základní virtuální pevný disk spravovaného disku do účtu úložiště v jiné oblasti. |
| [Kopírovat snímek do stejného nebo jiného předplatného](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Zkopíruje snímek do stejného nebo jiného předplatného, ale ve stejné oblasti jako nadřazený snímek. |
|**Síťové virtuální počítače**||
| [Zabezpečený síťový provoz mezi virtuálními počítači](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří dva virtuální počítače, všechny související prostředky a interní a externí skupiny zabezpečení sítě (NSG). |
|**Zabezpečení virtuálních počítačů**||
| [Šifrování virtuálních počítačů a datových disků](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří Azure Key Vault, šifrovací klíč a instanční objekt a pak zašifruje virtuální počítač. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálního počítače pomocí Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta Log Analytics a zaregistruje virtuální počítač v pracovním prostoru Log Analytics.  |
| | |
