---
title: Rozhraní příkazového řádku Azure ukázky Windows | Microsoft Docs
description: Rozhraní příkazového řádku Azure ukázky Windows
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
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4068bee0db04207c90344f3588c4e117b650a2f4
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Virtuální počítače Azure CLI ukázky pro Windows

Následující tabulka obsahuje odkazy na bash skripty vytvořené pomocí rozhraní příkazového řádku Azure, které nasadit virtuální počítače s Windows.

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač Windows s minimální konfigurací. |
| [Vytvoření kompletně nakonfigurovaný virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří skupinu prostředků, virtuální počítač a všechny související prostředky.|
| [Vytvoření vysoce dostupné virtuální počítače](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří několik virtuálních počítačů v s vysokou dostupností a konfigurace skupinu s vyrovnáváním zatížení. |
| [Vytvořte virtuální počítač a spusťte skript konfigurace](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač a používá rozšíření Azure vlastní skript k instalaci služby IIS. |
| [Vytvořte virtuální počítač a spusťte konfigurace DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač a rozšíření Azure požadovaného stavu konfigurace (DSC) používá k instalaci služby IIS. |
|**Síť virtuálních počítačů**||
| [Zabezpečení síťového provozu mezi virtuálními počítači](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří dva virtuální počítače, všechny související prostředky a skupiny zabezpečení k interní a externí sítě (NSG). |
|**Zabezpečený virtuální počítače**||
| [Šifrování virtuálních počítačů a datovými disky](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří Azure Key Vault, šifrovací klíč a instanční objekt a potom šifruje virtuálního počítače. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálních počítačů pomocí služby Operations Management Suite](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta nástroje Operations Management Suite a zaregistruje virtuální počítač v pracovním prostoru OMS.  |
| | |
