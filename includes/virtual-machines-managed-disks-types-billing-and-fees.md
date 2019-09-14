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
ms.openlocfilehash: a416d1c6e813be558f034e15576c57efa6073788
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968561"
---
**Přenosy odchozích dat**: [Přenosy odchozích dat](https://azure.microsoft.com/pricing/details/bandwidth/) (data odcházející z datových center Azure) se účtují za využití šířky pásma.

**Transakce**: Účtuje se vám počet transakcí, které provedete na standardním spravovaném disku. U standardních SSD se za jednu vstupně-výstupní operaci považuje každá vstupně-výstupní operace menší nebo rovna 256 KiB propustnosti. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více než více vstupně-výstupních operací o velikosti 256 KiB. U standardních HDD se každá vstupně-výstupní operace považuje za jedinou transakci, a to bez ohledu na velikost I/O.

Podrobné informace o cenách pro Managed Disks, včetně nákladů na transakce, najdete v článku [Managed disks ceny](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Poplatek za rezervaci virtuálních počítačů Ultra disk

Virtuální počítače Azure mají schopnost určit, jestli jsou kompatibilní s disky Ultra. Virtuální počítač kompatibilní s Ultra disk přiděluje vyhrazenou kapacitu šířky pásma mezi instancemi výpočetního virtuálního počítače a jednotkou škálování úložiště bloku, aby se optimalizoval výkon a snížila latence. Výsledkem přidání této možnosti na virtuální počítač je poplatek za rezervaci, který je uložený jenom v případě, že jste na virtuálním počítači povolili funkci Ultra disk, aniž byste k ní připojili disk Ultra. Pokud je Ultra disk připojený k virtuálnímu počítači kompatibilnímu s Ultra diskem, tento poplatek se nepoužije. Tento poplatek je vázaný na vCPU zřízený na virtuálním počítači. 

> [!Note]
> Pro [omezené velikosti virtuálních počítačů](../articles/virtual-machines/linux/constrained-vcpu.md)je poplatek za rezervaci založen na skutečném počtu vCPU, nikoli na omezeních jader. Pro Standard_E32-8s_v3 se poplatek za rezervaci vychází z 32 jader. 

Podrobnosti o cenách za Ultra disk najdete na [stránce s cenami za disky Azure](https://azure.microsoft.com/pricing/details/managed-disks/) .
