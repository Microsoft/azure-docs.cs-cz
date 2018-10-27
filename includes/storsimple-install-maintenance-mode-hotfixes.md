---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 5e4921be3116754f146ed0845513010f2642c97b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165317"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>K instalaci oprav hotfix režimu údržby pomocí prostředí Windows PowerShell pro StorSimple
> [!IMPORTANT]
> V režimu údržby musíte použít opravu hotfix nejprve na jeden kontroler a pak na druhý kontroler.
> 
> 

1. Umístíte zařízení do režimu údržby. Zobrazit [krok 2: Zadejte údržbu](../articles/storsimple/storsimple-update-device.md#step2) pokyny o tom, jak spustit režim údržby.
2. Pokud chcete použít opravu hotfix, zadejte:
   
     `Start-HcsHotfix` 
3. Po zobrazení výzvy zadejte cestu sdílené síťové složky, která obsahuje soubory oprav hotfix.
4. Zobrazí se výzva k potvrzení. Typ **Y** pokračovat v instalaci oprav hotfix.
5. Po použití opravu hotfix na jednom řadiči, přihlaste se na druhý kontroler. Použijte rychlou záplatu stejně jako u předchozí kontroler.
6. Po použití opravy hotfix, ukončete režim údržby. V tématu [krok 4: režim údržby ukončovací](../articles/storsimple/storsimple-update-device.md#step4) pokyny.

