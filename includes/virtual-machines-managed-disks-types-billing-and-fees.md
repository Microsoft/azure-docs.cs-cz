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
ms.openlocfilehash: 82bfdfd7481b47c08f76d077585f85763e58b87b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331158"
---
**Přenosy odchozích dat**: [Přenosy odchozích dat](https://azure.microsoft.com/pricing/details/bandwidth/) (data odcházející z Datacenter Azure) jsou zpoplatněné využití šířky pásma.

**Transakce**: Počet transakcí, které provedete na standardní spravovaný disk vám budeme fakturovat. Azure účtuje 0.0036 na 100 000 transakcí pro standardní pevné disky. Transakcemi se rozumí operace čtení a zápisu u úložiště.

Standardní disky SSD pomocí jednotky v/v velikost 256 KB. Pokud přenosu dat je menší než 256 KB, se považuje za jednu jednotku vstupně-výstupních operací. Větší velikosti vstupně-výstupní operace se počítají jako více vstupně-výstupních operací velikost 256 KB. Například 1100 KB vstupně-výstupní operace se počítá jako pět jednotky vstupně-výstupních operací.

Se neúčtují žádné poplatky za transakce pro spravovaný disk úrovně premium.

Podrobné informace o cenách pro službu Managed Disks najdete v tématu [spravované disky ceny](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disks-vm-reservation-fee"></a>Poplatek za rezervaci Ultra disky virtuálního počítače

Virtuální počítače Azure mají možnost určit, pokud jsou kompatibilní s ultra disky. Disk s ultra kompatibilní virtuální počítače přidělí vyhrazenou kapacitu šířky pásma mezi výpočetní instance virtuálního počítače a blok jednotka škálování úložiště optimalizovat výkon a snižuje latenci. Přidání této funkce na výsledky do virtuálního počítače rezervace poplatek, která je uložena pouze tehdy, pokud jste povolili možnost ultra disk ve virtuálním počítači bez připojení k němu ultra disku. Když ultra disk je připojený k ultra disk kompatibilní virtuální počítač, tento poplatek se neuplatní. Tento poplatek se za vCPU zřízení na virtuálním počítači.

Odkazovat [stránce s cenami Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) ultra disku disky podrobnosti o cenách.