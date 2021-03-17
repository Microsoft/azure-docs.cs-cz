---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c0627dd0833e3b20468eb5f50fbeb9fd9d9ae2b3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864694"
---
**Přenosy odchozích dat**: [odchozí přenosy dat](https://azure.microsoft.com/pricing/details/bandwidth/) (data odcházející z datových center Azure) se účtují podle využití šířky pásma.

**Transakce**: účtuje se počet transakcí, které provedete na standardním spravovaném disku. U standardních SSD se za jednu vstupně-výstupní operaci považuje každá vstupně-výstupní operace menší nebo rovna 256 KiB propustnosti. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více než více vstupně-výstupních operací o velikosti 256 KiB. U standardních HDD se každá vstupně-výstupní operace považuje za jedinou transakci, a to bez ohledu na velikost I/O.

Podrobné informace o cenách pro Managed Disks, včetně nákladů na transakce, najdete v článku [Managed disks ceny](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Poplatek za rezervaci virtuálních počítačů Ultra disk

Virtuální počítače Azure mají schopnost určit, jestli jsou kompatibilní s disky Ultra. Virtuální počítač kompatibilní s Ultra disk přiděluje vyhrazenou kapacitu šířky pásma mezi instancemi výpočetního virtuálního počítače a jednotkou škálování úložiště bloku, aby se optimalizoval výkon a snížila latence. Výsledkem přidání této možnosti na virtuální počítač je poplatek za rezervaci, který je uložený jenom v případě, že jste na virtuálním počítači povolili funkci Ultra disk, aniž byste k ní připojili disk Ultra. Pokud je Ultra disk připojený k virtuálnímu počítači kompatibilnímu s Ultra diskem, tento poplatek se nepoužije. Tento poplatek je vázaný na vCPU zřízený na virtuálním počítači. 

> [!Note]
> Pro [omezené velikosti virtuálních počítačů](../articles/virtual-machines/constrained-vcpu.md)je poplatek za rezervaci založen na skutečném počtu vCPU, nikoli na omezeních jader. U Standard_E32-8s_v3 bude poplatek za rezervaci vycházet z 32 jader. 

Podrobnosti o cenách za Ultra disk najdete na [stránce s cenami za disky Azure](https://azure.microsoft.com/pricing/details/managed-disks/) .

### <a name="azure-disk-reservation"></a>Rezervace disku Azure

Rezervace disku je možnost koupit jeden rok diskového úložiště předem při slevě a snížit tak celkové náklady. Při nákupu rezervovaného disku vyberete určitou SKU disku v cílové oblasti, například 10 P30 (1TiB) Premium SSD v oblasti Východní USA 2 oblast po dobu jednoho roku. Prostředí rezervace se podobá rezervovaným instancím virtuálních počítačů. Můžete začlenit rezervace virtuálních počítačů a disků a maximalizovat tak úspory. V současné době rezervace Azure disks nabízí plán závazku pro SKU úrovně Premium SSD z P30 (1TiB) na P80 (32 TiB) ve všech produkčních oblastech. Další podrobnosti o cenách rezervovaných disků najdete na [stránce s cenami za Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/).