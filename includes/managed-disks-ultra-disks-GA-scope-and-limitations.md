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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935937"
---
Pro tuto chvíli, ultra disky mají další omezení, jsou následující:

- Jsou podporovány v následujících oblastech s různým počtem zón dostupnosti na oblast:
    - USA – východ 2
    - USA – východ
    - USA – západ 2
    - Jihovýchodní Asie
    - Severní Evropa
    - Západní Evropa
    - Spojené království – jih 
- Lze použít pouze s dostupností zón (dostupnost sady a nasazení jednoho virtuálního počítače mimo zóny nebude mít možnost připojit ultra disk)
- Jsou podporovány jenom na následující chod ů virtuálních počítačích:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Ne každá velikost virtuálního počítače je dostupná ve všech podporovaných oblastech s ultra disky
- Jsou k dispozici pouze jako datové disky a podporují pouze velikost fyzického sektoru 4k. Vzhledem k velikosti 4K nativního sektoru Ultra Disk, existují některé aplikace, které nebudou kompatibilní s ultra disky. Jedním z příkladů by byla databáze Oracle Database, která vyžaduje vydání 12.2 nebo novější pro podporu ultra disků.  
- Lze vytvořit pouze jako prázdné disky  
- Zatím nepodporujete snímky disku, image virtuálních počítačů, sady dostupnosti a šifrování disku Azure
- Ještě nepodporujte integraci s Azure Backup nebo Azure Site Recovery
- Aktuální maximální limit pro viopy na virtuálních počítačích GA je 80 000.
- Pokud se chcete zúčastnit omezeného náhledu virtuálního počítače, který může dosáhnout 160 000 UltraDiskFeedback@microsoft VOPS s ultra disky, pošlete e-mail .com