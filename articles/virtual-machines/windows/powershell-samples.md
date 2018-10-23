---
title: Ukázky Powershellu pro virtuální počítač Azure | Dokumentace Microsoftu
description: Ukázky Powershellu pro virtuální počítač Azure
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
ms.date: 10/19/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3571ccfbb2f15738fd951c0fa55248745eac281a
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637429"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Ukázky Azure Powershellu pro virtuální počítač

Následující tabulka obsahuje odkazy na ukázkové skripty Powershellu pro vytváření a správu Windows virtuálních počítačů (VM).

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Rychle vytvořit virtuální počítač](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří skupinu prostředků, virtuálního počítače a všech souvisejících prostředků s nejméně výzvy.|
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří skupinu prostředků, virtuálního počítače a všechny související prostředky.|
| [Vytvoření virtuálních počítačů s vysokou dostupností](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří několik virtuálních počítačů v konfiguraci s vysokou dostupností a vyrovnáváním zatížení.|
| [Vytvoření virtuálního počítače a spusťte skript pro konfiguraci](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří virtuální počítač a používá rozšíření vlastních skriptů Azure k instalaci služby IIS. |
| [Vytvoření virtuálního počítače a používají konfiguraci DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření Azure Desired State Configuration (DSC) instalace služby IIS. |
| [Nahrání virtuálního pevného disku a vytvoření virtuálních počítačů](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Nahraje místní soubor virtuálního pevného disku do Azure, vytvoří image z virtuálního pevného disku a pak vytvoří virtuální počítač z této image. |
| [Vytvoření virtuálního počítače ze spravovaného disku operačního systému](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří virtuální počítač připojením existujícího spravovaného disku jako disku s operačním systémem. |
| [Vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří virtuální počítač ze snímku prvním vytvoření spravovaného disku ze snímku a pak připojení nového spravovaného disku jako disku s operačním systémem. |
|**Správa úložiště**||
| [Vytvoření spravovaného disku z virtuálního pevného disku do stejného nebo jiného předplatného](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného VHD jako disku s operačním systémem nebo z datového VHD jako datového disku do stejného nebo jiného předplatného.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří spravovaný disk ze snímku. |
| [Zkopírujte spravovaného disku do stejného nebo jiného předplatného](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Zkopíruje spravovaný disk stejné nebo jiné předplatné, který je ve stejné oblasti jako nadřazeného spravovaného disku. 
| [Exportovat snímku jako virtuálního pevného disku do účtu úložiště](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exportuje spravovaný snímek virtuálního pevného disku do účtu úložiště v jiné oblasti. |
| [Export virtuálního pevného disku tohoto spravovaného disku do účtu úložiště](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exportuje základního virtuálního pevného disku tohoto spravovaného disku do účtu úložiště v jiné oblasti. |
| [Vytvoření snímku ze souboru VHD](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří snímek ze souboru VHD a potom pomocí tohoto snímku rychle vytvořit několik identických spravovaných disků.  |
| [Zkopírování snímku do stejného nebo jiného předplatného](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Zkopíruje snímek do stejného nebo jiného předplatného, který je ve stejné oblasti jako nadřazený snímek. |
|**Zabezpečení virtuálních počítačů**||
| [Šifrování virtuálního počítače a její datové disky](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Vytvoří služby Azure key vault, šifrovací klíč a hlavního názvu služby a potom šifruje virtuálního počítače. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálních počítačů pomocí služby Log Analytics](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje se agent Azure Log Analytics a registraci virtuálního počítače v pracovním prostoru Log Analytics.  |
| | |
