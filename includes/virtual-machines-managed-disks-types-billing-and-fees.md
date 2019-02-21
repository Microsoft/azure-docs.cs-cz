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
ms.openlocfilehash: 0d771f03f9f71151ef25140148d4dd4daf3d46ec
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443390"
---
**Přenosy odchozích dat**: [Přenosy odchozích dat](https://azure.microsoft.com/pricing/details/bandwidth/) (data odcházející z Datacenter Azure) jsou zpoplatněné využití šířky pásma.

**Transakce**: Počet transakcí, které provedete na standardní spravovaný disk vám budeme fakturovat. Azure účtuje 0.0036 na 100 000 transakcí pro standardní pevné disky. Transakcemi se rozumí operace čtení a zápisu u úložiště.

Standardní disky SSD pomocí jednotky v/v velikost 256 KB. Pokud přenosu dat je menší než 256 KB, se považuje za jednu jednotku vstupně-výstupních operací. Větší velikosti vstupně-výstupní operace se počítají jako více vstupně-výstupních operací velikost 256 KB. Například 1100 KB vstupně-výstupní operace se počítá jako pět jednotky vstupně-výstupních operací.

Se neúčtují žádné poplatky za transakce pro spravovaný disk úrovně premium.

Podrobné informace o cenách pro službu Managed Disks najdete v tématu [spravované disky ceny](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra poplatku za rezervaci SSD virtuálního počítače

Virtuální počítače Azure mají možnost určit, pokud jsou kompatibilní s ultra SSD. Disk s ultra kompatibilní virtuální počítače přidělí vyhrazenou kapacitu šířky pásma mezi výpočetní instance virtuálního počítače a blok jednotka škálování úložiště optimalizovat výkon a snižuje latenci. Přidání této funkce na výsledky do virtuálního počítače rezervace poplatek, která je uložena pouze tehdy, pokud jste povolili možnost ultra disk ve virtuálním počítači bez připojení k němu ultra disku. Když ultra disk je připojený k ultra disk kompatibilní virtuální počítač, tento poplatek se neuplatní. Tento poplatek se za vCPU zřízení na virtuálním počítači.

Odkazovat [stránce s cenami Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) ultra disku disky podrobnosti o cenách.