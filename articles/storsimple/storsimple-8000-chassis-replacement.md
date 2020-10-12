---
title: Výměna skříně na zařízení řady StorSimple 8000 | Microsoft Docs
description: Popisuje, jak odebrat a nahradit skříň pro StorSimple primární skříň nebo skříň EBOD.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: de0f6299f35f9d76fdade976bf70456426e5ec51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85513427"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Výměna skříně na zařízení StorSimple
## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak odebrat a nahradit skříň v zařízení řady StorSimple 8000. Model StorSimple 8100 je jedno skříňové zařízení (jeden skříň), zatímco 8600 je zařízení se dvěma skříněmi (dva skříně). V případě modelu 8600 existuje potenciálně dva skříně, které by mohly selhat v zařízení: skříň pro primární skříň nebo skříň pro EBOD skříň.

Náhradní skříň, která je dodávána společností Microsoft, je v obou případech prázdná. Pro PCMs nejsou k dispozici žádné moduly napájení, moduly ovladačů, diskové jednotky Solid State (SSD), jednotky pevného disku (HDD) nebo moduly EBOD.

> [!IMPORTANT]
> Než odeberete a nahradíte skříň, přečtěte si bezpečnostní informace v části [StorSimple hardwarové součásti výměna](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Odebrat skříň
Pomocí následujících kroků odeberte skříň na zařízení StorSimple.

#### <a name="to-remove-a-chassis"></a>Odebrání skříně
1. Ujistěte se, že je zařízení StorSimple vypnuté a odpojené ze všech zdrojů napájení.
2. Odeberte všechny kabely sítě a SAS, pokud jsou k dispozici.
3. Odeberte jednotku ze stojanu.
4. Odeberte jednotlivé jednotky a poznamenejte si sloty, ze kterých byly odebrány. Další informace najdete v tématu [Odebrání diskové jednotky](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. V EBOD skříni (Pokud se jedná o skříň, která selhala) odeberte moduly řadiče EBOD. Další informace najdete v tématu [Odebrání eBOD kontroleru](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    V primární skříni (Pokud se jedná o skříň, která selhala), odeberte řadiče a poznamenejte si sloty, ze kterých jsou odebrané. Další informace najdete v tématu [Odebrání kontroleru](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Nainstalovat skříň
Pomocí následujících kroků nainstalujte skříň na zařízení StorSimple.

#### <a name="to-install-a-chassis"></a>Instalace skříně
1. Připojte skříň k stojanu. Další informace najdete v části [rack – připojení zařízení StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) nebo [racku připojit zařízení StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Až se skříň připojí k stojanu, nainstalujte moduly kontroléru do stejných umístění, v nichž byly dříve nainstalovány.
3. Nainstalujte jednotky na stejné pozice a sloty, v nichž byly dříve nainstalovány.
   
   > [!NOTE]
   > Doporučujeme nejdřív nainstalovat SSD do slotů a pak nainstalovat HDD.
  
4. Když je zařízení připojené k racku a nainstalované komponenty, připojte zařízení k příslušným zdrojům napájení a zapněte ho. Podrobnosti najdete v článku [kabel zařízení s StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) nebo [kabelem zařízení StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [nahrazení StorSimple hardwarové součásti](storsimple-8000-hardware-component-replacement.md).

