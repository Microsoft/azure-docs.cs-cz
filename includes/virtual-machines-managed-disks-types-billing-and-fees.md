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
ms.openlocfilehash: 6f6d373d6dd21f3fd04ca434678ec798146a0ffa
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512654"
---
**Přenosy odchozích dat**: [Přenosy odchozích dat](https://azure.microsoft.com/pricing/details/bandwidth/) (data odcházející z datových center Azure) se účtují za využití šířky pásma.

**Transakce**: Účtuje se vám počet transakcí, které provedete na standardním spravovaném disku. U standardních SSD se za jednu vstupně-výstupní operaci považuje každá vstupně-výstupní operace menší nebo rovna 256 KiB propustnosti. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více než více vstupně-výstupních operací o velikosti 256 KiB. U standardních HDD se každá vstupně-výstupní operace považuje za jedinou transakci, a to bez ohledu na velikost I/O.

Podrobné informace o cenách pro Managed Disks, včetně nákladů na transakce, najdete v článku [Managed disks ceny](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Poplatek za rezervaci virtuálních počítačů Ultra disk

Virtuální počítače Azure mají schopnost určit, jestli jsou kompatibilní s disky Ultra. Virtuální počítač kompatibilní s Ultra disk přiděluje vyhrazenou kapacitu šířky pásma mezi instancemi výpočetního virtuálního počítače a jednotkou škálování úložiště bloku, aby se optimalizoval výkon a snížila latence. Výsledkem přidání této možnosti na virtuální počítač je poplatek za rezervaci, který je uložený jenom v případě, že jste na virtuálním počítači povolili funkci Ultra disk, aniž byste k ní připojili disk Ultra. Pokud je Ultra disk připojený k virtuálnímu počítači kompatibilnímu s Ultra diskem, tento poplatek se nepoužije. Tento poplatek je vázaný na vCPU zřízený na virtuálním počítači.

Podrobnosti o cenách za Ultra disk najdete na [stránce s cenami za disky Azure](https://azure.microsoft.com/pricing/details/managed-disks/) .