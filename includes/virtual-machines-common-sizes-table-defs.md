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
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74279174"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definice tabulky velikostí

- Kapacita úložiště je v jednotkách GiB, tj. 1024^3 bajtů. Při porovnání disků měřených v GB (1000 ^3 bajtů) s disky měřenými v GiB (1024^3) nezapomeňte, že počet kapacit uvedených v GiB se může jevit menší. Například 1023 GiB = 1098.4 GB.
- Propustnost disku se měří v počtu V/V operací za sekundu (IOPS) a v MB/s, kde 1 MB/s = 10^6 bajtů/s.
- Disky pro ukládání dat můžou fungovat v režimu s mezipamětí, nebo bez ní. Pro diskové operace s mezipamětí je možné nastavit mezipaměť na hostiteli **jen na čtení** nebo **na čtení i zápis**.  Pro diskové operace bez mezipaměti je mezipaměť na hostiteli nastavená na **žádná**.
- Pokud chcete získat nejlepší výkon pro vaše virtuální počítače, měli byste omezit počet datových disků na dva disky na virtuální procesor.
- **Očekávaná šířka pásma sítě** je maximální agregovaná šířka pásma přidělená na typ virtuálního počítače ve všech síťových počítačích pro všechny cíle. Další informace naleznete v tématu [Šířka pásma sítě virtuálních strojů](../articles/virtual-network/virtual-machine-network-throughput.md).

  Horní hranice nejsou zaručeny. Limity nabízejí pokyny pro výběr správného typu virtuálního aplikace pro zamýšlenou aplikaci. Skutečný výkon sítě bude záviset na několika faktorech, včetně zahlcení sítě, zatížení aplikací a nastavení sítě. Informace o optimalizaci propustnostsítě najdete [v tématu Optimalizace propustnost sítě pro virtuální počítače Azure](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Chcete-li dosáhnout očekávaného výkonu sítě v Systému Linux nebo Windows, budete muset vybrat konkrétní verzi nebo optimalizovat virtuální počítač. Další informace naleznete v [tématu Testování šířky pásma/propustnost (NTTTCP).](../articles/virtual-network/virtual-network-bandwidth-testing.md)



