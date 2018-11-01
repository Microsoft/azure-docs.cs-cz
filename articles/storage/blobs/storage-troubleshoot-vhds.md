---
title: Řešení potíží s disky připojené k virtuálním počítačům Azure | Dokumentace Microsoftu
description: Obsahuje odkazy na materiály pro virtuální počítač Azure virtuálních pevných disků (VHD) pro řešení potíží.
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: bf1aa75399f28b8dd1732bc9ec09c15e46132939
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414162"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Řešení potíží s disky připojené k virtuálním počítačům Azure 

Azure Virtual Machines (VM) využívají virtuální pevné disky (VHD) pro disk s operačním systémem a všechny připojené datové disky. Virtuální pevné disky jsou uložené jako objekty BLOB stránky v jedné nebo více účtů úložiště Azure. Tento článek odkazuje na obsah pro běžné problémy, které mohou vzniknout s virtuálními pevnými disky pro řešení potíží. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Řešení chyb při odstraňování úložiště pro virtuální počítač

V některých případech může dojít k chybě při odstraňování prostředků úložiště, když virtuální počítač v nasazení Resource Manager obsahuje připojené virtuální pevné disky. Pro pomoc při řešení tohoto problému naleznete v následujících článcích: 

  * Na virtuální počítače s Linuxem: [chyb při odstraňování úložišť v nasazení podle modelu Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Na virtuální počítače s Windows: [chyb při odstraňování úložišť v nasazení podle modelu Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Řešení potíží s neočekávaná restartování virtuálních počítačů s připojenými virtuálními pevnými disky

Pokud dojde k neočekávaným restartováním virtuálního počítače s velkým počtem připojenými virtuálními pevnými disky, najdete v následujících článcích:

  * Na virtuální počítače s Linuxem: [neočekávaná restartování virtuálních počítačů s připojenými virtuálními pevnými disky](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Na virtuální počítače s Windows: [neočekávaná restartování virtuálních počítačů s připojenými virtuálními pevnými disky](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
