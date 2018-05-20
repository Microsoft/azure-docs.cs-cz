---
title: Ukázky rozhraní příkazového řádku Azure | Microsoft Docs
description: Ukázky Azure CLI
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5df71f1b662defa9103df4b10a86f67bf5ee6e4b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Ukázek Azure CLI pro virtuální počítače s Linuxem

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření virtuálního počítače](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač s Linuxem s minimální konfigurací. |
| [Vytvoření kompletně nakonfigurovaný virtuálního počítače](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří skupinu prostředků, virtuální počítač a všechny související prostředky.|
| [Vytvoření vysoce dostupné virtuální počítače](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří několik virtuálních počítačů v s vysokou dostupností a konfigurace skupinu s vyrovnáváním zatížení. |
| [Vytvořte virtuální počítač a spusťte skript konfigurace](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač a používá rozšíření Azure vlastní skript k instalaci NGINX. |
| [Vytvoření virtuálního počítače s WordPress nainstalován](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač a instalace aplikace WordPress pomocí rozšíření Azure vlastních skriptů. |
| [Vytvoření virtuálního počítače ze spravovaných disk operačního systému](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Vytvoří virtuální počítač pomocí stávající spravované disk jako disk s operačním systémem. |
| [Vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | Vytvoří virtuální počítač ze snímku nejprve vytvořením se spravovaným diskem ze snímku a potom nový spravované disk jako disk operačního systému se připojuje. |
|**Správa úložiště**||
| [Vytvoření spravovaného disku z virtuálního pevného disku](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného VHD jako disku s operačním systémem nebo z datového VHD jako datového disku.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | Vytvoří se spravovaným diskem ze snímku. |
| [Zkopírujte spravovaných disků na stejný nebo jiný odběr](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopie spravovat disk stejné nebo jiné předplatné, ale ve stejné oblasti jako nadřazená položka spravované disku. 
| [Export snímku jako virtuální pevný disk na účet úložiště](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fcli%2fmodule%2ftoc.json) | Exporty spravované snímek jako virtuální pevný disk na účet úložiště v jiné oblasti. |
| [Kopírování snímku do stejného nebo jiného předplatného](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopie snímků do stejného nebo jiného předplatného, ale ve stejné oblasti jako nadřazené snímku. |
|**Síť virtuálních počítačů**||
| [Zabezpečení síťového provozu mezi virtuálními počítači](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří dva virtuální počítače, všechny související prostředky a skupiny zabezpečení k interní a externí sítě (NSG). |
|**Zabezpečený virtuální počítače**||
| [Šifrování virtuálních počítačů a datovými disky](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří Azure Key Vault, šifrovací klíč a instanční objekt a potom šifruje virtuálního počítače. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálních počítačů pomocí služby Operations Management Suite](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta nástroje Operations Management Suite a zaregistruje virtuální počítač v pracovním prostoru OMS.  |
|**Řešení potíží s virtuální počítače**||
| [Řešení potíží s diskem operačního systému virtuálních počítačů](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fcli%2fazure%2ftoc.json) | Jako datový disk pro druhý virtuální počítač připojí disk operačního systému z jednoho virtuálního počítače. |
| | |
