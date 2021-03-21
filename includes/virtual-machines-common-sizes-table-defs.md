---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d610193783d44b86d48cd5ceaa91377b32b7061b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99580041"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definice tabulky velikostí

- Kapacita úložiště je v jednotkách GiB, tj. 1024^3 bajtů. Když porovnáte disky měřené v GB (1000 ^ 3 bajty) na disky měřené v GiB (1024 ^ 3), zapamatujte si, že čísla kapacit uvedená v GiB mohou být menší. Například 1023 GiB = 1098,4 GB.
- Propustnost disku se měří v počtu V/V operací za sekundu (IOPS) a v MB/s, kde 1 MB/s = 10^6 bajtů/s.
- Disky pro ukládání dat můžou fungovat v režimu s mezipamětí, nebo bez ní. Pro diskové operace s mezipamětí je možné nastavit mezipaměť na hostiteli **jen na čtení** nebo **na čtení i zápis**.  Pro diskové operace bez mezipaměti je mezipaměť na hostiteli nastavená na **žádná**.
- Další informace o tom, jak dosáhnout nejlepšího výkonu úložiště pro virtuální počítače, najdete v tématu [výkon virtuálního počítače a disku](../articles/virtual-machines/disks-performance.md).
- **Očekávaná šířka pásma sítě** je maximální agregovaná šířka pásma přidělená pro každý typ virtuálního počítače v rámci všech síťových adaptérů pro všechna umístění. Další informace najdete v tématu [Šířka pásma sítě virtuálních počítačů](../articles/virtual-network/virtual-machine-network-throughput.md).

  Horní limity nejsou zaručené. Omezení nabízí pokyny pro výběr správného typu virtuálního počítače pro zamýšlenou aplikaci. Skutečný výkon sítě bude záviset na několika faktorech, včetně zahlcení sítě, zatížení aplikací a nastavení sítě. Informace o optimalizaci propustnosti sítě najdete v tématu [optimalizace propustnosti sítě pro virtuální počítače Azure](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Chcete-li dosáhnout očekávaného výkonu sítě v systému Linux nebo Windows, bude pravděpodobně nutné vybrat konkrétní verzi nebo optimalizovat virtuální počítač. Další informace najdete v tématu [testování šířky pásma a propustnosti (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



