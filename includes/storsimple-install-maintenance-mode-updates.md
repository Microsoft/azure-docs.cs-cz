---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 01612b32e6c1b363df8a5c70405d0c709210328e
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165804"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Chcete-li nainstalovat aktualizace režimu údržby pomocí prostředí Windows PowerShell pro StorSimple
1. Pokud jste tak ještě neučinili, přístup ke konzole sériového portu zařízení a vyberte možnost 1, **přihlášení pomocí úplný přístup**. 
2. Zadejte heslo. Výchozí heslo je **Heslo1**.
3. Na příkazovém řádku zadejte:
   
     `Get-HcsUpdateAvailability` 
4. Pokud jsou k dispozici aktualizace, a zda jsou rušivé nebo nenarušující aktualizace, budete upozorněni. Použít narušující aktualizace, musíte převést zařízení do režimu údržby. Zobrazit [krok 2: Zadejte údržbu](../articles/storsimple/storsimple-update-device.md#step2) pokyny.
5. Když je zařízení v režimu údržby na příkazovém řádku zadejte: `Start-HcsUpdate`
6. Zobrazí se výzva k potvrzení. Jakmile potvrdíte aktualizace, budou nainstalovány na kontroler, který jste právě používají. Po instalaci aktualizace kontroleru se restartuje. 
7. Monitorujte stav aktualizace. Zadejte:
   
    `Get-HcsUpdateStatus`
   
    Pokud `RunInProgress` je `True`, aktualizaci stále probíhá. Pokud `RunInProgress` je `False`, znamená to, že se aktualizace dokončí.  
8. Když je aktualizace nainstalována na aktuální řadič a bylo znovu zahájeno, připojte se k jiné řadiče a proveďte kroky 1 až 6.
9. Jakmile jsou aktualizované oba kontrolery, ukončete režim údržby. V tématu [krok 4: režim údržby ukončovací](../articles/storsimple/storsimple-update-device.md#step4) pokyny.

