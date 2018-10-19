---
title: Ukázky Azure CLI | Microsoft Docs
description: Ukázky Azure CLI
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
ms.date: 03/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8d573b5a0dafaab5eea98f05163ff99e7ccd3760
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409080"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Ukázky v Azure CLI pro virtuální počítače s Linuxem

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření virtuálního počítače](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač s Linuxem s minimální konfigurací. |
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří skupinu prostředků, virtuálního počítače a všechny související prostředky.|
| [Vytvoření virtuálních počítačů s vysokou dostupností](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří několik virtuálních počítačů v s vysokou dostupností a s vyrovnáváním zatížení konfigurace. |
| [Vytvoření virtuálního počítače a spusťte skript pro konfiguraci](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření vlastních skriptů Azure nainstaluje server NGINX. |
| [Vytvoření virtuálního počítače s Wordpressem nainstalovaná](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření vlastních skriptů Azure nainstaluje WordPress. |
| [Vytvoření virtuálního počítače ze spravovaného disku operačního systému](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Vytvoří virtuální počítač připojením existujícího spravovaného disku jako disku s operačním systémem. |
| [Vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | Vytvoří virtuální počítač ze snímku prvním vytvoření spravovaného disku ze snímku a pak připojení nového spravovaného disku jako disku s operačním systémem. |
|**Správa úložiště**||
| [Vytvoření spravovaného disku z virtuálního pevného disku](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného VHD jako disku s operačním systémem nebo z datového VHD jako datového disku.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | Vytvoří spravovaný disk ze snímku. |
| [Kopie spravovaného disku do stejného nebo jiného předplatného](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Zkopíruje spravovaný disk do stejného nebo jiného předplatného, ale ve stejné oblasti jako nadřazený spravovaný disk. 
| [Exportovat snímku jako virtuálního pevného disku do účtu úložiště](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fcli%2fmodule%2ftoc.json) | Exportuje spravovaný snímek do účtu úložiště v jiné oblasti jako virtuální pevný disk. |
| [Export virtuálního pevného disku tohoto spravovaného disku do účtu úložiště](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fcli%2fmodule%2ftoc.json) | Exportuje základního virtuálního pevného disku tohoto spravovaného disku do účtu úložiště v jiné oblasti. |
| [Kopírování snímku do stejného nebo jiného předplatného](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Zkopíruje snímek do stejného nebo jiného předplatného, ale ve stejné oblasti jako nadřazený snímek. |
|**Sítě virtuálních počítačů**||
| [Zabezpečení provozu sítě mezi virtuálními počítači](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří dva virtuální počítače a všechny související prostředky, skupiny zabezpečení interní a externí sítě (NSG). |
|**Zabezpečení virtuálních počítačů**||
| [Šifrování disků virtuálních počítačů a dat](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří Azure Key Vault, šifrovací klíč a instanční objekt a potom šifruje virtuálního počítače. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálních počítačů pomocí Azure Log Analytics](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta Log Analytics a registraci virtuálního počítače v pracovním prostoru Log Analytics.  |
|**Řešení potíží s virtuálními počítači**||
| [Řešení potíží s diskem operačního systému virtuálních počítačů](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fcli%2fazure%2ftoc.json) | Připojí disk s operačním systémem z jednoho virtuálního počítače jako datový disk na druhý virtuální počítač. |
| | |
