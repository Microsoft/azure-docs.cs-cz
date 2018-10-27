---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165937"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Nainstalovat aktualizace režimu údržby pomocí prostředí Windows PowerShell pro StorSimple

Při použití aktualizace režimu údržby na zařízení StorSimple jsou pozastaveny všechny vstupně-výstupní požadavky. Služby, jako je paměť s náhodným přístupem stálé (paměti NVRAM) nebo službu clusteringu se zastaví. Až zadáte nebo ukončit tento režim restartovat oba kontrolery. Při ukončení tento režim všech služeb pokračovat a jsou v pořádku. (To může trvat několik minut.)

> [!IMPORTANT]
> * Před přechod do režimu údržby, ověřte, že oba kontrolery zařízení jsou v dobrém stavu na webu Azure Portal. Pokud kontroler není v pořádku, [kontaktujte podporu Microsoftu](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) pro další kroky.
> * Když jste v režimu údržby, je potřeba nejdřív aktualizovat jeden kontroler a pak na druhý kontroler.

1. Použití klienta PuTTY k připojení ke konzole sériového portu. Postupujte podle podrobných pokynů v článku [Připojení ke konzole sériového portu pomocí klienta PuTTy](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Na příkazovém řádku stiskněte **Enter**. Vyberte možnost 1 **přihlášení pomocí úplný přístup**.

2. Chcete-li kontroler uvést do režimu údržby, zadejte:
    
    `Enter-HcsMaintenanceMode`

    Oba kontrolery se restartují do režimu údržby.

3. Nainstalujte aktualizace režimu údržby. Zadejte:

    `Start-HcsUpdate`

    Zobrazí se výzva k potvrzení. Po ověření aktualizace se instalují na kontroler, který jste právě používají. Po instalaci aktualizace kontroleru se restartuje.

4. Monitorujte stav aktualizace. Přihlaste se k řadiči peer jako aktuální řadič se aktualizuje a nemůže zpracovat žádné další příkazy. Zadejte:

    `Get-HcsUpdateStatus`

    Pokud `RunInProgress` je `True`, aktualizaci stále probíhá. Pokud `RunInProgress` je `False`, znamená to, že se aktualizace dokončí.

5. Po úspěšné instalaci aktualizací firmwaru disku a aktualizované kontroler restartoval, zkontrolujte verzi firmwaru disku. Na kontroler aktualizovaný zadejte:

    `Get-HcsFirmwareVersion`
   
    Očekávané verze firmwaru disku jsou:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Ukončete režim údržby. Zadejte následující příkaz pro každý kontroler zařízení:

    `Exit-HcsMaintenanceMode`

    Kontrolery se restartují, jakmile ukončíte režim údržby.

7. Vraťte se na Azure Portal. Na portálu se nemusí zobrazit nainstalované aktualizace režimu údržby po dobu 24 hodin.