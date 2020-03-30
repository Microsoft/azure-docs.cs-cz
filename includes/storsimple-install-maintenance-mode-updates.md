---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175042"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalace aktualizací režimu údržby prostřednictvím prostředí Windows PowerShell pro službu StorSimple
1. Pokud jste tak ještě neučinili, přezižte sériovou konzolu zařízení a vyberte možnost **1, Přihlaste se s úplným přístupem**. 
2. Zadejte heslo. Výchozí heslo je **Password1**.
3. Na příkazovém řádku zadejte:
   
     `Get-HcsUpdateAvailability` 
4. Budete upozorněni, pokud jsou k dispozici aktualizace a zda jsou rušivé nebo nerušivé. Chcete-li použít rušivé aktualizace, je třeba zařízení převést do režimu údržby. Pokyny naleznete [v kroku 2: Vstupte do režimu údržby.](../articles/storsimple/storsimple-update-device.md#step2)
5. Když je zařízení v režimu údržby, zadejte na příkazovém řádku:`Start-HcsUpdate`
6. Zobrazí se výzva k potvrzení. Po potvrzení aktualizací budou nainstalovány na řadiči, ke kterému právě přistupujete. Po instalaci aktualizací se řadič restartuje. 
7. Sledujte stav aktualizací. Zadejte:
   
    `Get-HcsUpdateStatus`
   
    Pokud `RunInProgress` je `True`, aktualizace stále probíhá. Pokud `RunInProgress` `False`je , znamená to, že aktualizace byla dokončena.  
8. Pokud je aktualizace nainstalována na aktuálním řadiči a byla restartována, připojte se k jinému řadiči a proveďte kroky 1 až 6.
9. Po aktualizaci obou řadičů ukončete režim údržby. Pokyny naleznete [v kroku 4: Ukončete režim údržby.](../articles/storsimple/storsimple-update-device.md#step4)

