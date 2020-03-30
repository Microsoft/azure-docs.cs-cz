---
title: Ukázky prostředí Azure Virtual Machine PowerShell
description: Ukázky prostředí Azure Virtual Machine PowerShell
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 903860dfd1e30f7941770efd759227a1349d41ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035256"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Ukázky prostředí PowerShell virtuálního počítače Azure

Následující tabulka obsahuje odkazy na ukázky skriptů prostředí PowerShell, které vytvářejí a spravují virtuální počítače Linuxu.

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří skupinu prostředků, virtuální počítač a všechny související prostředky.|
| [Vytvoření virtuálního virtuálního připojení s povoleným Dockerem](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač, nakonfiguruje tento virtuální počítač jako hostitele Dockeru a spustí kontejner NGINX. |
| [Vytvoření virtuálního počítače a spuštění konfiguračního skriptu](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač a k instalaci NGINX uvede rozšíření Azure Custom Script. |
| [Vytvoření virtuálního virtuálního zařízení s nainstalovaným WordPressem](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač a k instalaci WordPressu použije rozšíření Azure Custom Script. |
| [Vytvoření virtuálního počítače ze spravovaného disku operačního systému](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač připojením existujícího spravovaného disku jako disku operačního systému. |
| [Vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač ze snímku tak, že nejprve vytvoří spravovaný disk ze snímku a potom připojí nový spravovaný disk jako disk operačního systému. |
|**Spravovat úložiště**||
| [Vytvoření spravovaného disku z virtuálního pevného disku ve stejném nebo jiném předplatném](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného virtuálního pevného disku jako disk operačního systému nebo z datového virtuálního pevného disku jako datový disk ve stejném nebo jiném předplatném.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří spravovaný disk ze snímku. |
| [Export snímku jako VHD do účtu úložiště](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exportuje spravovaný snímek jako virtuální pevný disk do účtu úložiště v jiné oblasti. |
| [Export VHD spravovaného disku do účtu úložiště](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exportuje základní virtuální pevný disk spravovaného disku do účtu úložiště v jiné oblasti. |
| [Vytvoření snímku z VHD](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří snímek z virtuálního pevného disku a pak tento snímek použije k rychlému vytvoření více identických spravovaných disků.  |
| [Kopírování snímku do stejného nebo jiného předplatného](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Zkopíruje snímek na stejné nebo jiné předplatné, které je ve stejné oblasti jako nadřazený snímek. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálního počítače pomocí protokolů Azure Monitoru](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta Analýzy protokolů a zapíše virtuální počítač do pracovního prostoru Analýzy protokolů.  |
| [Kopírování spravovaného disku do stejného nebo jiného předplatného](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Zkopíruje spravovaný disk na stejné nebo jiné předplatné, které je ve stejné oblasti jako nadřazený spravovaný disk.
| [Shromažďování podrobností o všech virtuálních aplikacích v předplatném pomocí PowerShellu](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří csv, který obsahuje název virtuálního počítače, název skupiny prostředků, oblast, virtuální síť, podsíť, privátní IP adresu, typ operačního serveru a veřejnou IP adresu virtuálních počítačů v zadaném předplatném.
| | |
