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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008624"
---
V současnosti mají extrémně disky další omezení, jsou následující:

Jedinou možností redundance infrastruktury, která je aktuálně dostupná pro disky Ultra, jsou zóny dostupnosti. Virtuální počítače s využitím jakýchkoli dalších možností redundance nemůžou připojit Ultra disk.

V následující tabulce jsou popsány oblasti Ultra disks jsou k dispozici v nástroji a také jejich odpovídající možnosti dostupnosti:

> [!NOTE]
> Některé zóny dostupnosti v rámci těchto oblastí nenabízejí Ultra disks.

|Oblasti  |Žádná redundance infrastruktury  |Zóny dostupnosti  |
|---------|---------|---------|
|USA – západ     |Ano         |Ne         |
|USA – západ 2    |Ne         |Ano         |
|USA – východ     |Ne         |Ano         |
|USA – východ 2     |Ne         |Ano         |
|Jihovýchodní Asie     |Ne         |Ano         |
|Severní Evropa     |Ne         |Ano         |
|Západní Evropa     |Ne         |Ano         |
|Spojené království – jih     |Ne         |Ano         |

- Podporují se jenom na následujících řadách virtuálních počítačů:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Ne všechny velikosti virtuálních počítačů jsou dostupné v každé podporované oblasti s extrémně disky.
- Jsou k dispozici pouze jako datové disky a podporují pouze velikost fyzického sektoru 4k. Vzhledem k nativnímu sektoru 4K velikosti Ultra disku jsou některé aplikace nekompatibilní s disky Ultra. Příkladem může být Oracle Database, který vyžaduje vydání verze 12,2 nebo novější, aby se podporovaly Ultra disky.  
- Dá se vytvořit jenom jako prázdné disky.  
- V současné době nepodporuje snímky disků, image virtuálních počítačů, skupiny dostupnosti, vyhrazené hostitele Azure nebo Azure Disk Encryption.
- V současné době nepodporuje integraci s Azure Backup ani Azure Site Recovery
- Aktuální maximální limit pro IOPS na virtuálních počítačích GA je 80 000.

Azure Ultra disks nabízí ve výchozím nastavení až 16 TiB na každou oblast a předplatné, ale u Ultra disks podporuje vyšší kapacitu podle požadavků. Pokud chcete požádat o zvýšení kapacity, obraťte se na podporu Azure.