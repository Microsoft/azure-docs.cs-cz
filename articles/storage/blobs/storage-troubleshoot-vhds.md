---
title: Poradce při potížích s disky připojenými k virtuálním počítačům Azure | Dokumenty společnosti Microsoft
description: Obsahuje odkazy na řešení potíží s prostředky virtuálních pevných disků virtuálních počítačů (VD).
services: storage
author: roygara
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: b81c0947327dc8e84c5d3fbbecde3aa31313cc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061188"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Poradce při potížích s disky připojenými k virtuálním počítačům Azure 

Virtuální počítače Azure (VM) spoléhají na virtuální pevné disky (VND) pro disk operačního systému a všechny připojené datové disky. Virtuální počítače se ukládají jako objekty BLOB stránky v jednom nebo více účtech Azure Storage. Tento článek odkazuje na řešení potíží s obsahem běžných problémů, které mohou nastat s virtuálními edy. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Poradce při potížích s chybami odstranění úložiště pro virtuální hosti

V některých případech může dojít k chybě při odstraňování prostředku úložiště, když virtuální počítače v nasazení Správce prostředků obsahuje připojené virtuální počítače. Nápovědu k řešení tohoto problému naleznete v jednom z následujících článků: 

  * Na virtuálních počítačích SIP: [Chyby odstranění úložiště v nasazení Správce prostředků](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Na virtuálních počítačích s Windows: [Chyby odstranění úložiště v nasazení Správce prostředků](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Poradce při potížích s neočekávanými restartováními virtuálních počítačů s připojenými virtuálními počítači

Pokud narazíte na neočekávané restartování virtuálního počítače s velkým počtem připojených virtuálních discích, podívejte se na jeden z následujících článků:

  * Na virtuálních počítačích s Linuxem: [Neočekávané restartování virtuálních počítačí s připojenými virtuálními počítači](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Na virtuálních počítačích se systémem Windows: [Neočekávané restartování virtuálních počítačí s připojenými virtuálními počítači](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
