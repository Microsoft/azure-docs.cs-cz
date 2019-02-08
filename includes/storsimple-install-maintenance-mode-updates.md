---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889407"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Chcete-li nainstalovat aktualizace režimu údržby pomocí prostředí Windows PowerShell pro StorSimple
1. Pokud jste tak ještě neučinili, přístup ke konzole sériového portu zařízení a vyberte možnost 1, **přihlášení pomocí úplný přístup**. 
2. Zadejte heslo. Výchozí heslo je **Heslo1**.
3. Na příkazovém řádku zadejte:
   
     `Get-HcsUpdateAvailability` 
4. Pokud jsou k dispozici aktualizace, a zda jsou rušivé nebo nenarušující aktualizace, budete upozorněni. Použít narušující aktualizace, musíte převést zařízení do režimu údržby. Zobrazit [krok 2: Spustit režim údržby](../articles/storsimple/storsimple-update-device.md#step2) pokyny.
5. Když je zařízení v režimu údržby na příkazovém řádku zadejte: `Start-HcsUpdate`
6. Zobrazí se výzva k potvrzení. Jakmile potvrdíte aktualizace, budou nainstalovány na kontroler, který jste právě používají. Po instalaci aktualizace kontroleru se restartuje. 
7. Monitorujte stav aktualizace. Zadejte:
   
    `Get-HcsUpdateStatus`
   
    Pokud `RunInProgress` je `True`, aktualizaci stále probíhá. Pokud `RunInProgress` je `False`, znamená to, že se aktualizace dokončí.  
8. Když je aktualizace nainstalována na aktuální řadič a bylo znovu zahájeno, připojte se k jiné řadiče a proveďte kroky 1 až 6.
9. Jakmile jsou aktualizované oba kontrolery, ukončete režim údržby. Zobrazit [krok 4: Ukončete režim údržby](../articles/storsimple/storsimple-update-device.md#step4) pokyny.

