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
ms.openlocfilehash: 42ab8be45d4086589f0793531003700e7552a440
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744013"
---
**Přenosy odchozích dat**: [Přenosy odchozích dat](https://azure.microsoft.com/pricing/details/bandwidth/) (data odcházející z Datacenter Azure) jsou zpoplatněné využití šířky pásma.

**Transakce**: Počet transakcí, které provedete na standardní spravovaný disk vám budeme fakturovat. Pro standardní jednotky SSD velikost jednotky vstupně-výstupních operací 256 KiB slouží pro účetní oddělení počtu transakcí. Větší velikosti vstupně-výstupní operace se počítají jako více vstupně-výstupních operací, o velikosti 256 KiB. Standardní pevné disky se nepovažuje za každé vstupně-výstupní operace, jako jedna transakce, bez ohledu na velikost vstupně-výstupních operací.

Podrobné informace o cenách pro službu Managed Disks, včetně náklady za transakce, naleznete v tématu [spravované disky ceny](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra poplatku za rezervaci SSD virtuálního počítače

Virtuální počítače Azure mají možnost určit, pokud jsou kompatibilní s ultra SSD. Disk s ultra kompatibilní virtuální počítače přidělí vyhrazenou kapacitu šířky pásma mezi výpočetní instance virtuálního počítače a blok jednotka škálování úložiště optimalizovat výkon a snižuje latenci. Přidání této funkce na výsledky do virtuálního počítače rezervace poplatek, která je uložena pouze tehdy, pokud jste povolili možnost ultra disk ve virtuálním počítači bez připojení k němu ultra disku. Když ultra disk je připojený k ultra disk kompatibilní virtuální počítač, tento poplatek se neuplatní. Tento poplatek se za vCPU zřízení na virtuálním počítači.

Odkazovat [stránce s cenami Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) ultra disku disky podrobnosti o cenách.