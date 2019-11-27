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
ms.openlocfilehash: df488c2a3033015175943f7d092beaaa41e34046
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260693"
---
V současnosti mají extrémně disky další omezení, jsou následující:

- Jsou podporovány v následujících oblastech s proměnlivým počtem zón dostupnosti na oblast:
    - Východ USA 2
    - Východní USA
    - Západní USA 2
    - Jihovýchodní Asie
    - Severní Evropa
    - Západní Evropa
    - Spojené království – jih 
- Dá se použít jenom se zónami dostupnosti (skupinami dostupnosti a nasazeními s jedním virtuálním počítačem mimo zóny nebude mít možnost připojit Ultra disk).
- Podporují se jenom na následujících řadách virtuálních počítačů:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Ne všechny velikosti virtuálních počítačů jsou k dispozici v každé podporované oblasti s disky Ultra.
- K dispozici pouze jako datové disky a podporují pouze velikost fyzického sektoru 4k  
- Dá se vytvořit jenom jako prázdné disky.  
- Ještě nepodporují snímky disků, image virtuálních počítačů, skupiny dostupnosti a Azure Disk Encryption.
- Ještě nepodporují integraci s Azure Backup ani Azure Site Recovery
- Aktuální maximální limit pro IOPS na virtuálních počítačích GA je 80 000.
- Pokud se chcete zapojit do omezené verze Preview virtuálního počítače, který může dosahovat 160 000 vstupně-výstupních operací s disky Ultra, pošlete prosím e-mail UltraDiskFeedback@microsoft. com.