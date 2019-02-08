---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5c62c67cd401c043352b06e6e6070a7fc0f1296
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888583"
---
#### <a name="to-connect-through-the-serial-console"></a>Připojení přes sériovou konzolu
1. Připojte k zařízení sériový kabel (přímo nebo prostřednictvím sériového adaptéru USB).
2. Otevřete **Ovládací panely** a potom **Správce zařízení**.
3. Najděte port COM, jak znázorňuje následující obrázek.
   
     ![Připojení prostřednictvím sériové konzoly](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. Spusťte PuTTY. 
5. V pravém podokně změňte **Typ připojení** na **Sériové**.
6. V pravém podokně zadejte odpovídající port COM. Ujistěte se, že jsou sériové konfigurační parametry nastavené takto:
   
   * Rychlost: 115,200
   * Datové bity: 8
   * Stop-bity: 1
   * Parita: Žádný
   * Řízení toku: Žádný
     
     Tato nastavení znázorňuje následující obrázek.
     
     ![Nastavení PuTTY](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > Pokud výchozí nastavení řízení toku nefunguje, zkuste nastavit řízení toku XON/XOFF.
     > 
     > 
7. Kliknutím na **Otevřít** spustíte sériovou relaci.

