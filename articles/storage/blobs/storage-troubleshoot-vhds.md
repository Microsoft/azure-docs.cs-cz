---
title: Řešení potíží s disky připojené k virtuálním počítačům Azure | Dokumentace Microsoftu
description: Obsahuje odkazy na materiály pro virtuální počítač Azure virtuálních pevných disků (VHD) pro řešení potíží.
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 10/31/2018
ms.date: 12/10/2018
ms.author: v-jay
ms.openlocfilehash: bf1aa75399f28b8dd1732bc9ec09c15e46132939
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098095"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Řešení potíží s disky připojené k virtuálním počítačům Azure 

Azure Virtual Machines (VM) využívají virtuální pevné disky (VHD) pro disk s operačním systémem a všechny připojené datové disky. Virtuální pevné disky jsou uložené jako objekty BLOB stránky v jedné nebo více účtů úložiště Azure. Tento článek odkazuje na obsah pro běžné problémy, které mohou vzniknout s virtuálními pevnými disky pro řešení potíží. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Řešení chyb při odstraňování úložiště pro virtuální počítač

V některých případech může dojít k chybě při odstraňování prostředků úložiště, když virtuální počítač v nasazení Resource Manager obsahuje připojené virtuální pevné disky. Pro pomoc při řešení tohoto problému naleznete v následujících článcích: 

  * Na virtuální počítače s Linuxem: [Chyb při odstraňování úložišť v nasazení podle modelu Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Na virtuálních počítačích s Windows: [Chyb při odstraňování úložišť v nasazení podle modelu Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Řešení potíží s neočekávaná restartování virtuálních počítačů s připojenými virtuálními pevnými disky

Pokud dojde k neočekávaným restartováním virtuálního počítače s velkým počtem připojenými virtuálními pevnými disky, najdete v následujících článcích:

  * Na virtuální počítače s Linuxem: [Neočekávaná restartování virtuálních počítačů s připojenými virtuálními pevnými disky](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Na virtuálních počítačích s Windows: [Neočekávaná restartování virtuálních počítačů s připojenými virtuálními pevnými disky](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
