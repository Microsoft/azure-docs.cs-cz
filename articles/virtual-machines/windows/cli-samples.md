---
title: Ukázky Azure CLI Windows | Dokumentace Microsoftu
description: Ukázky Azure CLI Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8bc151089f76e3f27ababb479f5e893ca9a99365
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844053"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Virtuální počítače Azure CLI ukázky pro Windows

Následující tabulka obsahuje odkazy na skripty bash vytvořené pomocí Azure CLI, které nasazení virtuálních počítačů s Windows.

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač Windows s minimální konfigurací. |
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří skupinu prostředků, virtuálního počítače a všechny související prostředky.|
| [Vytvoření virtuálních počítačů s vysokou dostupností](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří několik virtuálních počítačů v s vysokou dostupností a s vyrovnáváním zatížení konfigurace. |
| [Vytvoření virtuálního počítače a spusťte skript pro konfiguraci](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač a používá rozšíření vlastních skriptů Azure k instalaci služby IIS. |
| [Vytvoření virtuálního počítače a používají konfiguraci DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření Azure Desired State Configuration (DSC) instalace služby IIS. |
|**Správa úložiště**||
| [Vytvoření spravovaného disku z virtuálního pevného disku](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného VHD jako disku s operačním systémem nebo z datového VHD jako datového disku.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří spravovaný disk ze snímku. |
| [Kopie spravovaného disku do stejného nebo jiného předplatného](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Zkopíruje spravovaný disk do stejného nebo jiného předplatného, ale ve stejné oblasti jako nadřazený spravovaný disk. 
| [Exportovat snímku jako virtuálního pevného disku do účtu úložiště](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportuje spravovaný snímek do účtu úložiště v jiné oblasti jako virtuální pevný disk. |
| [Export virtuálního pevného disku tohoto spravovaného disku do účtu úložiště](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportuje základního virtuálního pevného disku tohoto spravovaného disku do účtu úložiště v jiné oblasti. |
| [Kopírování snímku do stejného nebo jiného předplatného](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Zkopíruje snímek do stejného nebo jiného předplatného, ale ve stejné oblasti jako nadřazený snímek. |
|**Sítě virtuálních počítačů**||
| [Zabezpečení provozu sítě mezi virtuálními počítači](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří dva virtuální počítače a všechny související prostředky, skupiny zabezpečení interní a externí sítě (NSG). |
|**Zabezpečení virtuálních počítačů**||
| [Šifrování disků virtuálních počítačů a dat](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří Azure Key Vault, šifrovací klíč a instanční objekt a potom šifruje virtuálního počítače. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálního počítače pomocí Azure monitoru](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta Log Analytics a registraci virtuálního počítače v pracovním prostoru Log Analytics.  |
| | |
