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
ms.openlocfilehash: 268467796e67caf2d20fedb44d83fd455a09b83e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174994"
---
**Přenosy odchozích dat**: [Přenosy odchozích dat](https://azure.microsoft.com/pricing/details/bandwidth/) (data odcházející z Datacenter Azure) jsou zpoplatněné využití šířky pásma.

**Transakce**: Počet transakcí, které provedete na standardní spravovaný disk vám budeme fakturovat. Pro standardní jednotky SSD každý vstupně-výstupní operace menší než nebo rovna až 256 KiB propustnosti se považuje za jednu vstupně-výstupní operace. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více vstupně-výstupních operací, o velikosti 256 KiB. Standardní pevné disky se nepovažuje za každé vstupně-výstupní operace, jako jedna transakce, bez ohledu na velikost vstupně-výstupních operací.

Podrobné informace o cenách pro službu Managed Disks, včetně náklady za transakce, naleznete v tématu [spravované disky ceny](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra poplatku za rezervaci SSD virtuálního počítače

Virtuální počítače Azure mají možnost určit, pokud jsou kompatibilní s ultra SSD. Disk s ultra kompatibilní virtuální počítače přidělí vyhrazenou kapacitu šířky pásma mezi výpočetní instance virtuálního počítače a blok jednotka škálování úložiště optimalizovat výkon a snižuje latenci. Přidání této funkce na výsledky do virtuálního počítače rezervace poplatek, která je uložena pouze tehdy, pokud jste povolili možnost ultra disk ve virtuálním počítači bez připojení k němu ultra disku. Když ultra disk je připojený k ultra disk kompatibilní virtuální počítač, tento poplatek se neuplatní. Tento poplatek se za vCPU zřízení na virtuálním počítači.

Odkazovat [stránce s cenami Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) ultra disku disky podrobnosti o cenách.