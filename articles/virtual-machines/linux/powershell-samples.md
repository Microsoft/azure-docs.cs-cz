---
title: Ukázky PowerShellu pro virtuální počítače Azure | Microsoft Docs
description: Ukázky PowerShellu pro virtuální počítače Azure
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
ms.openlocfilehash: 3c897d4f8cef5fe45893b96a565ed9eff7ae0b11
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082391"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Ukázky PowerShellu pro virtuální počítače Azure

Následující tabulka obsahuje odkazy na ukázky skriptů PowerShellu pro vytváření a správu virtuálních počítačů se systémem Linux.

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří skupinu prostředků, virtuální počítač a všechny související prostředky.|
| [Vytvoření virtuálního počítače s povoleným Docker](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač, nakonfiguruje tento virtuální počítač jako hostitele Docker a spustí kontejner NGINX. |
| [Vytvoření virtuálního počítače a spuštění konfiguračního skriptu](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření vlastních skriptů Azure nainstaluje NGINX. |
| [Vytvoření virtuálního počítače s nainstalovaným WordPressem](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření Azure Custom Script Extension nainstaluje WordPress. |
| [Vytvoření virtuálního počítače ze spravovaného disku s operačním systémem](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač připojením existujícího spravovaného disku jako disku s operačním systémem. |
| [Vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač ze snímku vytvořením spravovaného disku ze snímku a následným připojením nového spravovaného disku jako disku s operačním systémem. |
|**Správa úložiště**||
| [Vytvoření spravovaného disku z VHD ve stejném nebo jiném předplatném](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného VHD jako disku s operačním systémem nebo z datového virtuálního pevného disku jako datový disk ve stejném nebo jiném předplatném.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří ze snímku spravovaný disk. |
| [Export snímku jako virtuálního pevného disku do účtu úložiště](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exportuje spravovaný snímek jako virtuální pevný disk do účtu úložiště v jiné oblasti. |
| [Export virtuálního pevného disku spravovaného disku do účtu úložiště](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exportuje základní virtuální pevný disk spravovaného disku do účtu úložiště v jiné oblasti. |
| [Vytvoření snímku z VHD](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří snímek z VHD a pak pomocí tohoto snímku rychle vytvoří několik identických spravovaných disků.  |
| [Zkopírování snímku do stejného nebo jiného předplatného](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Zkopíruje snímek do stejného nebo jiného předplatného, které je ve stejné oblasti jako nadřazený snímek. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálního počítače pomocí protokolů Azure Monitor](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta Log Analytics a zaregistruje virtuální počítač v pracovním prostoru Log Analytics.  |
| [Zkopírování spravovaného disku do stejného nebo jiného předplatného](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Zkopíruje spravovaný disk do stejného nebo jiného předplatného, které je ve stejné oblasti jako nadřazený spravovaný disk.
| [Shromažďování podrobností o všech virtuálních počítačích v rámci předplatného pomocí PowerShellu](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří sdílený svazek clusteru, který obsahuje název virtuálního počítače, název skupiny prostředků, oblast, Virtual Network, podsíť, privátní IP adresu, typ operačního systému a veřejnou IP adresu virtuálních počítačů v zadaném předplatném.
| | |
