---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1f094cd78e6708fec4bda1e5510379b11df14dcd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299153"
---
Přírůstkové snímky jsou zálohy v čase pro spravované disky, které při pořízení sestávají pouze ze všech změn od posledního snímku. Při pokusu o stažení nebo jiné použití přírůstkového snímku se použije úplný virtuální pevný disk. Tato nová funkce pro snímky spravovaných disků potenciálně umožňuje získat cenově výhodnější, protože pokud se nerozhodnete, nebudete muset celý disk ukládat na jednotlivé snímky. Stejně jako běžné snímky lze přírůstkové snímky použít buď k vytvoření úplného spravovaného disku, nebo k vytvoření normálního snímku.

Existuje několik rozdílů mezi přírůstkovým snímkem a běžným snímkem. Přírůstkové snímky budou vždy používat standardní úložiště HDD bez ohledu na typ úložiště disku, zatímco běžné snímky mohou používat Premium SSD. Pokud používáte běžné snímky na Premium Storage k horizontálnímu navýšení kapacity nasazení virtuálních počítačů, doporučujeme používat vlastní image ve službě Storage úrovně Standard v [galerii sdílených imagí](../articles/virtual-machines/linux/shared-image-galleries.md). Pomůže vám dosáhnout větší obrovské škály s nižšími náklady. Navíc přírůstkové snímky potenciálně nabízejí lepší spolehlivost pomocí [redundantního úložiště](../articles/storage/common/storage-redundancy-zrs.md) (ZRS) zóny. Pokud je ve vybrané oblasti k dispozici ZRS, přírůstkový snímek bude automaticky používat ZRS. Pokud ZRS není v této oblasti k dispozici, bude mít tento snímek výchozí [místně redundantní úložiště](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Toto chování můžete přepsat a vybrat ho ručně, ale nedoporučujeme to.

Přírůstkové snímky také nabízí rozdílovou možnost, která je k dispozici pouze pro spravované disky. Umožňují získat změny dvou přírůstkových snímků stejných spravovaných disků na úrovni bloku. Tuto možnost můžete použít ke snížení objemu dat při kopírování snímků napříč oblastmi.
