---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e94b14584f8eece3d772f67b680df4a2ffb8b5cd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603300"
---
V současnosti mají extrémně disky další omezení, jsou následující:

Jedinou možností redundance infrastruktury, která je aktuálně dostupná pro disky Ultra, jsou zóny dostupnosti. Virtuální počítače s využitím jakýchkoli dalších možností redundance nemůžou připojit Ultra disk.

V následující tabulce jsou popsány oblasti Ultra disks jsou k dispozici v nástroji a také jejich odpovídající možnosti dostupnosti:

> [!NOTE]
> Pokud oblast v následujícím seznamu nemá žádné zóny dostupnosti Ultra disks, musí být virtuální počítače v této oblasti nasazené bez možností redundance infrastruktury, aby bylo možné připojit extrémně disk.

|Oblasti  |Možnosti redundance  |
|---------|---------|
|Brazílie – jih     |Pouze jeden virtuální počítač (skupiny dostupnosti a sady škálování virtuálních počítačů nejsou podporovány)|
|Indie – střed     |Pouze jeden virtuální počítač (skupiny dostupnosti a sady škálování virtuálních počítačů nejsou podporovány)|
|Východní Asie     |Pouze jeden virtuální počítač (skupiny dostupnosti a sady škálování virtuálních počítačů nejsou podporovány)|
|Německo – středozápad     |Pouze jeden virtuální počítač (skupiny dostupnosti a sady škálování virtuálních počítačů nejsou podporovány)|
|Jižní Korea – střed     |Pouze jeden virtuální počítač (skupiny dostupnosti a sady škálování virtuálních počítačů nejsou podporovány)|
|Středojižní USA    |Pouze jeden virtuální počítač (skupiny dostupnosti a sady škálování virtuálních počítačů nejsou podporovány)|
|USA (Gov) – Arizona     |Pouze jeden virtuální počítač (skupiny dostupnosti a sady škálování virtuálních počítačů nejsou podporovány)|
|USA (Gov) – Virginia     |Pouze jeden virtuální počítač (skupiny dostupnosti a sady škálování virtuálních počítačů nejsou podporovány)|
|USA (Gov) – Texas     |Pouze jeden virtuální počítač (skupiny dostupnosti a sady škálování virtuálních počítačů nejsou podporovány)|
|USA – západ     |Pouze jeden virtuální počítač (skupiny dostupnosti a sady škálování virtuálních počítačů nejsou podporovány)        |
|Austrálie – střed    |Pouze jeden virtuální počítač (skupiny dostupnosti a sady škálování virtuálních počítačů nejsou podporovány)|
|Austrálie – východ     |Tři zóny dostupnosti         |
|Southeast Asia    |Tři zóny dostupnosti        |
|Střední Kanada     |Tři zóny dostupnosti          |
|USA – střed     |Tři zóny dostupnosti          |
|East US     |Tři zóny dostupnosti          |
|USA – východ 2     |Tři zóny dostupnosti         |
|Francie – střed    |Dvě zóny dostupnosti        |
|Japonsko – východ    |Tři zóny dostupnosti        |
|Severní Evropa    |Tři zóny dostupnosti        |
|Spojené království – jih    |Tři zóny dostupnosti        |
|West Europe    | Tři zóny dostupnosti|
|Západní USA 2    |Tři zóny dostupnosti|

- Podporují se jenom na následujících řadách virtuálních počítačů:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [4m](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Ne všechny velikosti virtuálních počítačů jsou k dispozici v každé podporované oblasti s disky Ultra.
- Jsou k dispozici pouze jako datové disky. 
- Ve výchozím nastavení podporuje 4k velikost fyzického sektoru. velikost sektoru 512E je dostupná jako všeobecně dostupná nabídka (nevyžaduje se žádné přihlášení), ale teď je dostupná jenom pomocí rozhraní příkazového řádku nebo PowerShellu. Většina aplikací je kompatibilní s velikostmi sektorů 4k, ale některé velikosti sektorů vyžadují 512 bajtů. Příkladem může být Oracle Database, který vyžaduje vydání verze 12,2 nebo novější, aby bylo možné podporovat nativní disky 4k. Pro starší verze Oracle DB je požadována velikost bajtového sektoru 512.
- Dá se vytvořit jenom jako prázdné disky.
- V současné době nepodporuje snímky disků, image virtuálních počítačů, skupiny dostupnosti, vyhrazené hostitele Azure nebo Azure Disk Encryption.
- V současné době nepodporuje integraci s Azure Backup ani Azure Site Recovery.
- Podporuje pouze čtení z mezipaměti a zápisy v mezipaměti.
- Aktuální maximální limit pro IOPS na virtuálních počítačích GA je 80 000.

Azure Ultra disks nabízí ve výchozím nastavení až 16 TiB na každou oblast a předplatné, ale u Ultra disks podporuje vyšší kapacitu podle požadavků. Pokud chcete požádat o zvýšení kapacity, obraťte se na podporu Azure.
