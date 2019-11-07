---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600979"
---
V současnosti mají extrémně disky další omezení, jsou následující:

- Jsou podporovány v následujících oblastech s proměnlivým počtem zón dostupnosti na oblast:
    - Východ USA 2
    - Východ USA
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