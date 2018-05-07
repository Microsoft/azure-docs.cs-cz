---
title: Řešení potíží s disky připojené k virtuálním počítačům Azure | Microsoft Docs
description: Služba Azure Blob Storage je navržená k ukládání velkých objemů nestrukturovaných dat objektů, jako jsou textová nebo binární data. Vaše aplikace můžou k objektům ve službě Blob Storage přistupovat z PowerShellu nebo Azure CLI, z kódu prostřednictvím klientských knihoven pro Azure Storage nebo přes REST.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: tamram
ms.openlocfilehash: ae309efb95e2f633effcfb5723d8377f5e94d406
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Řešení potíží s disky připojené k virtuálním počítačům Azure 

Azure virtuální počítače (VM) využívají virtuální pevné disky (VHD) pro disk operačního systému a všechny připojené datových disků. Virtuální pevné disky jsou uložené jako objekty BLOB stránky v jeden nebo více účtů úložiště Azure. Tento článek ukazuje na obsah pro běžné problémy, které mohou nastat u virtuálních pevných disků pro řešení potíží. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Řešení chyb odstranění úložiště pro virtuální počítač

V některých případech může dojít k chybě při odstraňování prostředků úložiště, pokud obsahuje virtuální počítač v nasazení Resource Manager připojit virtuální pevné disky. Pomoc při řešení tohoto problému najdete v následujících článcích: 

  * Na virtuální počítače s Linuxem: [úložiště odstranění chyby v nasazení Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Na virtuálních počítačích Windows: [úložiště odstranění chyby v nasazení Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Řešení potíží s neočekávané restartování virtuálních počítačů s připojených virtuálních pevných disků

Pokud dojde k neočekávané restartování virtuálního počítače s velký počet virtuálních pevných disků připojených, najdete v jednom z následujících článků:

  * Na virtuální počítače s Linuxem: [neočekávaná restartování virtuálních počítačů s připojených virtuálních pevných disků](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Na virtuálních počítačích Windows: [neočekávaná restartování virtuálních počítačů s připojených virtuálních pevných disků](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
