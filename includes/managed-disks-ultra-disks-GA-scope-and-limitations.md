---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f66bb1c271418b02c2548de486793b4474f40c8b
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84337376"
---
V současnosti mají extrémně disky další omezení, jsou následující:

Jedinou možností redundance infrastruktury, která je aktuálně dostupná pro disky Ultra, jsou zóny dostupnosti. Virtuální počítače s využitím jakýchkoli dalších možností redundance nemůžou připojit Ultra disk.

V následující tabulce jsou popsány oblasti Ultra disks jsou k dispozici v nástroji a také jejich odpovídající možnosti dostupnosti:

> [!NOTE]
> Pokud oblast v následujícím seznamu nemá žádné zóny dostupnosti Ultra disks, musí být virtuální počítače v této oblasti nasazené bez možností redundance infrastruktury, aby bylo možné připojit extrémně disk.

|Oblasti  |Počet zón dostupnosti podporujících extrémně disky  |
|---------|---------|
|USA (Gov) – Virginia     |Žádné         |
|USA – středojih     |Žádné         |
|USA – střed     |Tři zóny         |
|USA – západ     |Žádné         |
|USA – západ 2    |Tři zóny         |
|USA – východ     |Tři zóny         |
|USA – východ 2     |Dvě zóny         |
|Jihovýchodní Asie     |Tři zóny         |
|Severní Evropa     |Tři zóny          |
|Západní Evropa     |Tři zóny          |
|Spojené království – jih     |Tři zóny          |
|Japonsko – východ     |Dvě zóny         |


- Podporují se jenom na následujících řadách virtuálních počítačů:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [4m](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Ne všechny velikosti virtuálních počítačů jsou dostupné v každé podporované oblasti s extrémně disky.
- Jsou k dispozici pouze jako datové disky a podporují pouze velikost fyzického sektoru 4k. Vzhledem k nativnímu sektoru 4K velikosti Ultra disku jsou některé aplikace nekompatibilní s disky Ultra. Příkladem může být Oracle Database, který vyžaduje vydání verze 12,2 nebo novější, aby se podporovaly Ultra disky.  
- Dá se vytvořit jenom jako prázdné disky.  
- V současné době nepodporuje snímky disků, image virtuálních počítačů, skupiny dostupnosti, vyhrazené hostitele Azure nebo Azure Disk Encryption.
- V současné době nepodporuje integraci s Azure Backup ani Azure Site Recovery
- Podporuje jenom čtení bez mezipaměti a zápisy v mezipaměti.
- Aktuální maximální limit pro IOPS na virtuálních počítačích GA je 80 000.

Azure Ultra disks nabízí ve výchozím nastavení až 16 TiB na každou oblast a předplatné, ale u Ultra disks podporuje vyšší kapacitu podle požadavků. Pokud chcete požádat o zvýšení kapacity, obraťte se na podporu Azure.