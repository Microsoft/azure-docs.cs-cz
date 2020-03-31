---
title: Výměna šasi na zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje, jak odebrat a vyměnit šasi pro primární skříň StorSimple nebo skříň EBOD.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 7dfc39f4d08c8a49d1564a0a5bd7e3ef4156e3fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61312441"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Výměna šasi na zařízení StorSimple
## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak odebrat a nahradit šasi v zařízení řady StorSimple 8000. Model StorSimple 8100 je jedno skříňové zařízení (jeden podvozek), zatímco 8600 je zařízení s duálnískříní (dva podvozky). U modelu 8600 existují potenciálně dva podvozky, které by mohly selhat v zařízení: podvozek pro primární skříň nebo podvozek pro skříň EBOD.

V obou případech je náhradní šasi dodávané společností Microsoft prázdné. Nebudou zahrnuty žádné napájecí a chladicí moduly (PCM), moduly řadiče, jednotky SSD, pevné disky (HDD) nebo moduly EBOD.

> [!IMPORTANT]
> Před vyjmutím a výměnou šasi si přečtěte bezpečnostní informace v [části Výměna hardwarové součásti StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Vyjměte podvozek
Chcete-li odebrat šasi na zařízení StorSimple, postupujte podle následujících kroků.

#### <a name="to-remove-a-chassis"></a>Odebrání šasi
1. Ujistěte se, že zařízení StorSimple je vypnuto a odpojeno od všech zdrojů napájení.
2. Pokud je k dispozici, odeberte všechny síťové kabely a kabely SAS.
3. Vyjměte jednotku ze stojanu.
4. Odstraňte všechny jednotky a poznamenejte si sloty, ze kterých jsou odstraněny. Další informace naleznete [v tématu Remove the disk drive](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. Ve skříni EBOD (pokud se jedná o šasi, které selhalo), odeberte moduly řadiče EBOD. Další informace naleznete [v tématu Remove an EBOD controller](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    V primární skříni (pokud se jedná o šasi, která selhala), odstraňte ovladače a poznamenejte si sloty, ze kterých jsou odstraněny. Další informace naleznete [v tématu Odebrání ovladače](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Instalace šasi
Provedením následujících kroků nainstalujte šasi do zařízení StorSimple.

#### <a name="to-install-a-chassis"></a>Instalace šasi
1. Namontujte podvozek do stojanu. Další informace naleznete v [tématu Rack-mount zařízení StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) nebo [Rack-mount zařízení StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Po montáži šasi do stojanu nainstalujte moduly regulátoru ve stejných polohách, ve kterých byly dříve nainstalovány.
3. Nainstalujte jednotky ve stejných pozicích a slotech, ve kterých byly dříve nainstalovány.
   
   > [!NOTE]
   > Doporučujeme nejprve nainstalovat disky SSD do slotů a potom nainstalovat pevné disky.
  
4. Pokud je zařízení namontováno ve stojanu a jsou nainstalovány součásti, připojte zařízení k příslušným zdrojům napájení a zařízení zapněte. Podrobnosti viz [Kabel zařízení StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) nebo [Kabel zařízení StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Další kroky
Další informace o [výměně hardwarových komponent StorSimple](storsimple-8000-hardware-component-replacement.md).

