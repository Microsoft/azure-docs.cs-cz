---
title: Dv4 a Dsv4-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače s dv4 a Dsv4-Series.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: cc6ce30855d17f45636e0df04978fed88dcecff7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549137"
---
# <a name="dv4-and-dsv4-series"></a>Řada Dv4 a Dsv4

Dv4 a Dsv4-Series běží na &reg; procesorech Intel Xeon &reg; Platinum 8272CL (Cascade Lake) v konfiguraci s vlákny typu Hyper a poskytují lepší polohu pro většinu úloh pro obecné účely. Nabízí veškerou základní rychlost Turbo 3,4 GHz. 

> [!NOTE]
> Nejčastější dotazy najdete v tématu  [velikosti virtuálních počítačů Azure bez místního dočasného disku](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>Dv4-Series

Velikosti řady dv4-Series běží na Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Velikosti řady dv4-Series nabízejí kombinaci možností vCPU, paměti a vzdáleného úložiště pro většinu produkčních úloh. Virtuální počítače dv4-Series [funkce &reg; technologie Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

Úložiště vzdálených datových disků se fakturuje odděleně od virtuálních počítačů. Pokud chcete použít disky Premium Storage, použijte velikosti Dsv4. Měřiče cen a účtování pro velikosti Dsv4 jsou stejné jako pro dv4-Series.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): nepodporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované (*vyžaduje minimálně 4 vCPU*)<br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): nepodporované <br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | Jenom vzdálené úložiště | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | Jenom vzdálené úložiště | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | Jenom vzdálené úložiště | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | Jenom vzdálené úložiště | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | Jenom vzdálené úložiště | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Jenom vzdálené úložiště | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | Jenom vzdálené úložiště | 32 | 8|30000 |

## <a name="dsv4-series"></a>Dsv4-Series

Velikosti řady Dsv4-Series běží na Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Velikosti řady dv4-Series nabízejí kombinaci možností vCPU, paměti a vzdáleného úložiště pro většinu produkčních úloh. Virtuální počítače Dsv4-Series [funkce &reg; technologie Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). Úložiště vzdálených datových disků se fakturuje odděleně od virtuálních počítačů.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované (*vyžaduje minimálně 4 vCPU*)<br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): nepodporované <br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Jenom vzdálené úložiště | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Jenom vzdálené úložiště | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Jenom vzdálené úložiště | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Jenom vzdálené úložiště | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Jenom vzdálené úložiště | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Jenom vzdálené úložiště | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Jenom vzdálené úložiště | 32 | 80000/1200 | 8|30000 |
