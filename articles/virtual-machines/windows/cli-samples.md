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
ms.date: 06/01/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6861399b63b7f06bac7599704a6dd1aa87800ebf
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403334"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Virtuální počítače Azure CLI ukázky pro Windows

Následující tabulka obsahuje odkazy na skripty bash vytvořené pomocí Azure CLI, které nasazení virtuálních počítačů s Windows.

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač Windows s minimální konfigurací. |
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří skupinu prostředků, virtuálního počítače a všechny související prostředky.|
| [Vytvoření virtuálních počítačů s vysokou dostupností](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří několik virtuálních počítačů v s vysokou dostupností a s vyrovnáváním zatížení konfigurace. |
| [Vytvoření virtuálního počítače a spusťte skript pro konfiguraci](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač a používá rozšíření vlastních skriptů Azure k instalaci služby IIS. |
| [Vytvoření virtuálního počítače a používají konfiguraci DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač a pomocí rozšíření Azure Desired State Configuration (DSC) instalace služby IIS. |
|**Sítě virtuálních počítačů**||
| [Zabezpečení provozu sítě mezi virtuálními počítači](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří dva virtuální počítače a všechny související prostředky, skupiny zabezpečení interní a externí sítě (NSG). |
|**Zabezpečení virtuálních počítačů**||
| [Šifrování disků virtuálních počítačů a dat](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří Azure Key Vault, šifrovací klíč a instanční objekt a potom šifruje virtuálního počítače. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálních počítačů pomocí služby Log Analytics](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta Log Analytics a registraci virtuálního počítače v pracovním prostoru Log Analytics.  |
| | |
