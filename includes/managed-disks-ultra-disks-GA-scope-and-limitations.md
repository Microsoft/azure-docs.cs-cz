---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935937"
---
V současnosti mají extrémně disky další omezení, jsou následující:

- Jsou podporovány v následujících oblastech s proměnlivým počtem zón dostupnosti na oblast:
    - Východ USA 2
    - USA – východ
    - Západní USA 2
    - Jihovýchodní Asie
    - Severní Evropa
    - Západní Evropa
    - Velká Británie – jih 
- Dá se použít jenom se zónami dostupnosti (skupinami dostupnosti a nasazeními s jedním virtuálním počítačem mimo zóny nebude mít možnost připojit Ultra disk).
- Podporují se jenom na následujících řadách virtuálních počítačů:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Ne všechny velikosti virtuálních počítačů jsou dostupné v každé podporované oblasti s extrémně disky.
- Jsou k dispozici pouze jako datové disky a podporují pouze velikost fyzického sektoru 4k. Vzhledem k nativnímu sektoru 4K velikosti Ultra disku jsou některé aplikace nekompatibilní s disky Ultra. Příkladem může být Oracle Database, který vyžaduje vydání verze 12,2 nebo novější, aby se podporovaly Ultra disky.  
- Dá se vytvořit jenom jako prázdné disky.  
- Ještě nepodporují snímky disků, image virtuálních počítačů, skupiny dostupnosti a Azure Disk Encryption.
- Ještě nepodporují integraci s Azure Backup ani Azure Site Recovery
- Aktuální maximální limit pro IOPS na virtuálních počítačích GA je 80 000.
- Pokud se chcete zapojit do omezené verze Preview virtuálního počítače, který může dosahovat 160 000 vstupně-výstupních operací s disky Ultra, pošlete prosím e-mail UltraDiskFeedback@microsoft. com.