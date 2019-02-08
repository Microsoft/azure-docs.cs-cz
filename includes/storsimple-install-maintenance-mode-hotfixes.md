---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889376"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>K instalaci oprav hotfix režimu údržby pomocí prostředí Windows PowerShell pro StorSimple
> [!IMPORTANT]
> V režimu údržby musíte použít opravu hotfix nejprve na jeden kontroler a pak na druhý kontroler.
> 
> 

1. Umístíte zařízení do režimu údržby. Zobrazit [krok 2: Spustit režim údržby](../articles/storsimple/storsimple-update-device.md#step2) pokyny o tom, jak spustit režim údržby.
2. Pokud chcete použít opravu hotfix, zadejte:
   
     `Start-HcsHotfix` 
3. Po zobrazení výzvy zadejte cestu sdílené síťové složky, která obsahuje soubory oprav hotfix.
4. Zobrazí se výzva k potvrzení. Typ **Y** pokračovat v instalaci oprav hotfix.
5. Po použití opravu hotfix na jednom řadiči, přihlaste se na druhý kontroler. Použijte rychlou záplatu stejně jako u předchozí kontroler.
6. Po použití opravy hotfix, ukončete režim údržby. Zobrazit [krok 4: Ukončete režim údržby](../articles/storsimple/storsimple-update-device.md#step4) pokyny.

