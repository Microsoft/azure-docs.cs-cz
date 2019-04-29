---
title: Ukázky Powershellu pro virtuální počítač Azure | Dokumentace Microsoftu
description: Ukázky Powershellu pro virtuální počítač Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 53784c3d74f9e6af5f1e84cc098194113e81333b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771256"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Ukázky Azure Powershellu pro virtuální počítač

Následující tabulka obsahuje odkazy na ukázkové skripty Powershellu vytvářet a spravovat virtuální počítače s Linuxem.

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří skupinu prostředků, virtuálního počítače a všechny související prostředky.|
| [Vytvoření Virtuálního počítače s povoleným Dockerem](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač, nakonfiguruje tento virtuální počítač jako hostitele Docker a spuštění kontejneru NGINX. |
| [Vytvoření virtuálního počítače a spusťte skript pro konfiguraci](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření vlastních skriptů Azure nainstaluje server NGINX. |
| [Vytvoření virtuálního počítače s Wordpressem nainstalovaná](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření vlastních skriptů Azure nainstaluje WordPress. |
| [Vytvoření virtuálního počítače ze spravovaného disku operačního systému](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač připojením existujícího spravovaného disku jako disku s operačním systémem. |
| [Vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač ze snímku prvním vytvoření spravovaného disku ze snímku a pak připojení nového spravovaného disku jako disku s operačním systémem. |
|**Správa úložiště**||
| [Vytvoření spravovaného disku z virtuálního pevného disku do stejného nebo jiného předplatného](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného VHD jako disku s operačním systémem nebo z datového VHD jako datového disku do stejného nebo jiného předplatného.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří spravovaný disk ze snímku. |
| [Exportovat snímku jako virtuálního pevného disku do účtu úložiště](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exportuje spravovaný snímek virtuálního pevného disku do účtu úložiště v jiné oblasti. |
| [Export virtuálního pevného disku tohoto spravovaného disku do účtu úložiště](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exportuje základního virtuálního pevného disku tohoto spravovaného disku do účtu úložiště v jiné oblasti. |
| [Vytvoření snímku ze souboru VHD](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří snímek ze souboru VHD a potom pomocí tohoto snímku rychle vytvořit několik identických spravovaných disků.  |
| [Zkopírování snímku do stejného nebo jiného předplatného](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Zkopíruje snímek do stejného nebo jiného předplatného, který je ve stejné oblasti jako nadřazený snímek. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálního počítače s protokoly Azure monitoru](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta Log Analytics a registraci virtuálního počítače v pracovním prostoru Log Analytics.  |
| [Zkopírujte spravovaného disku do stejného nebo jiného předplatného](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Zkopíruje spravovaný disk stejné nebo jiné předplatné, který je ve stejné oblasti jako nadřazeného spravovaného disku.
| | |
