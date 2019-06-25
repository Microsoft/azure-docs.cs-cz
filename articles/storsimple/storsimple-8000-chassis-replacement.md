---
title: Výměna skříně na zařízení StorSimple řady 8000 | Dokumentace Microsoftu
description: Popisuje postup odstranění a výměna skříně pro primární zařízení StorSimple nebo EBOD skříň.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61312441"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Výměna skříně zařízení StorSimple
## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak odebrat a výměna skříně v zařízení řady StorSimple 8000. StorSimple 8100 modelu je jeden skříň zařízení (jednom šasi), vzhledem k tomu, 8600 je duální skříň zařízení (dvě skříně). Model 8600, jsou potenciálně dvě skříně, které by mohlo selhat v zařízení: Skříň pro primární skříň nebo skříň pro EBOD skříň.

V obou případech skříně nahrazení, který je dodáván společností Microsoft je prázdný. Žádné napájení a chlazení moduly (PCMs), řadiče moduly diskové jednotky SSD (Solid-State Drive), pevných disků (HDD) nebo modulů EBOD budou zahrnuty.

> [!IMPORTANT]
> Před odebírání a nahrazování skříni, přečtěte si informace bezpečnost v [StorSimple hardwarové komponenty nahrazení](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Odebrat skříň.
Proveďte následující kroky k odebrání skříň zařízení StorSimple.

#### <a name="to-remove-a-chassis"></a>Chcete-li odebrat skříň
1. Ujistěte se, že zařízení StorSimple je vypnout a odpojí ze všech zdrojů energie.
2. Odeberte všechny sítě a SAS kabely, pokud je k dispozici.
3. Odebrání jednotka racku.
4. Odebrat všechny jednotky a poznamenejte si sloty, ze kterých se odeberou. Další informace najdete v tématu [odeberte diskové jednotce](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. Na EBOD skříň (Pokud se jedná skříň, do které se nezdařilo) odeberte moduly řadiče EBOD. Další informace najdete v tématu [odebrat řadič EBOD](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    Na primární zařízení (Jestliže jde skříň, do které se nezdařilo) odeberte řadiče a poznamenejte si sloty, ze kterých se odeberou. Další informace najdete v tématu [odebrat řadič](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Nainstalujte skříň.
Proveďte následující kroky k instalaci skříň zařízení StorSimple.

#### <a name="to-install-a-chassis"></a>Chcete-li nainstalovat skříň
1. Připojte skříně v racku. Další informace najdete v tématu [zařízení StorSimple 8100 namontovat](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) nebo [zařízení StorSimple 8600 namontovat](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Po skříni je namontování do racku, nainstalujte na stejném místě, které byly dříve nainstalovány v moduly kontroleru.
3. Nainstalujte jednotky ve stejném umístění a slotů, které byly dříve nainstalovány v.
   
   > [!NOTE]
   > Doporučujeme nejprve nainstalovat disky SSD v přihrádkách a potom nainstalujte HDD.
  
4. Se zařízením připojit v racku a aby byly nainstalovány součásti připojení zařízení k příslušné power zdrojů a zapněte zařízení. Podrobnosti najdete v tématu [zapojení kabeláže zařízení StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) nebo [zapojení kabeláže zařízení StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Další postup
Další informace o [StorSimple hardwarové komponenty nahrazení](storsimple-8000-hardware-component-replacement.md).

