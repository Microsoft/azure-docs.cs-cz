---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008624"
---
Pro tuto chvíli, ultra disky mají další omezení, jsou následující:

Jediné možnosti redundance infrastruktury, které jsou v současné době k dispozici pro ultra disky, jsou zóny dostupnosti. Virtuální počítače využívající jiné možnosti redundance nemohou připojit ultra disk.

V následující tabulce jsou uvedeny oblasti, ve kterých jsou ultra disky k dispozici, a také jejich odpovídající možnosti dostupnosti:

> [!NOTE]
> Některé zóny dostupnosti v těchto oblastech nenabízejí ultra disky.

|Oblasti  |Žádné redundance infrastruktury  |Zóny dostupnosti  |
|---------|---------|---------|
|USA – západ     |Ano         |Ne         |
|USA – západ 2    |Ne         |Ano         |
|USA – východ     |Ne         |Ano         |
|USA – východ 2     |Ne         |Ano         |
|Jihovýchodní Asie     |Ne         |Ano         |
|Severní Evropa     |Ne         |Ano         |
|Západní Evropa     |Ne         |Ano         |
|Spojené království – jih     |Ne         |Ano         |

- Jsou podporovány jenom na následující chod ů virtuálních počítačích:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Ne každá velikost virtuálního počítače je dostupná ve všech podporovaných oblastech s ultra disky
- Jsou k dispozici pouze jako datové disky a podporují pouze velikost fyzického sektoru 4k. Vzhledem k velikosti 4K nativního sektoru Ultra Disk, existují některé aplikace, které nebudou kompatibilní s ultra disky. Jedním z příkladů by byla databáze Oracle Database, která vyžaduje vydání 12.2 nebo novější pro podporu ultra disků.  
- Lze vytvořit pouze jako prázdné disky  
- Momentálně nepodporuje snímky disků, image virtuálních počítačů, sady dostupnosti, vyhrazené hostitele Azure nebo šifrování disku Azure.
- Momentálně nepodporuje integraci s Azure Backup nebo Azure Site Recovery.
- Aktuální maximální limit pro viopy na virtuálních počítačích GA je 80 000.

Azure ultra disky nabízejí až 16 TiB na oblast na předplatné ve výchozím nastavení, ale ultra disky podporují vyšší kapacitu na vyžádání. Chcete-li požádat o zvýšení kapacity, obraťte se na podporu Azure.