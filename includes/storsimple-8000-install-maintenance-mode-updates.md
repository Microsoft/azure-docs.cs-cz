---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175203"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalace aktualizací režimu údržby prostřednictvím prostředí Windows PowerShell pro StorSimple

Při použití aktualizace režimu údržby zařízení StorSimple jsou pozastaveny všechny vstupně-va. Služby, jako je například energeticky nezávislá paměť nvram (random access) nebo clusteringová služba, jsou zastaveny. Oba řadiče se restartují při vstupu nebo ukončení tohoto režimu. Po ukončení tohoto režimu všechny služby pokračovat a jsou v pořádku. (To může trvat několik minut.)

> [!IMPORTANT]
> * Před vstupem do režimu údržby ověřte, že oba řadiče zařízení jsou v pořádku na webu Azure Portal. Pokud řadič není v pořádku, [obraťte se](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) na podporu společnosti Microsoft pro další kroky.
> * Když jste v režimu údržby, musíte nejprve aktualizovat jeden řadič a pak druhý řadič.

1. PuTTY se připojte k sériové konzoli. Postupujte podle podrobných pokynů v článku [Připojení ke konzole sériového portu pomocí klienta PuTTy](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Na příkazovém řádku stiskněte **Enter**. Vyberte možnost 1, **Přihlaste se s úplným přístupem**.

2. Chcete-li regulátor umístit do režimu údržby, zadejte:
    
    `Enter-HcsMaintenanceMode`

    Oba řadiče se restartují do režimu údržby.

3. Nainstalujte aktualizace režimu údržby. Zadejte:

    `Start-HcsUpdate`

    Budete vyzváni k potvrzení. Po potvrzení aktualizací jsou nainstalovány na řadiči, ke kterému právě přistupujete. Po instalaci aktualizací se řadič restartuje.

4. Sledujte stav aktualizací. Přihlaste se k peer controller jako aktuální řadič se aktualizuje a není schopen zpracovat žádné další příkazy. Zadejte:

    `Get-HcsUpdateStatus`

    Pokud `RunInProgress` je `True`, aktualizace stále probíhá. Pokud `RunInProgress` `False`je , znamená to, že aktualizace byla dokončena.

5. Po úspěšném použití aktualizací firmwaru disku a restartování aktualizovaného řadiče ověřte verzi firmwaru disku. Na aktualizovaném řadiči zadejte:

    `Get-HcsFirmwareVersion`
   
    Očekávané verze firmwaru disku jsou:`XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Ukončete režim údržby. Pro každý řadič zařízení zadejte následující příkaz:

    `Exit-HcsMaintenanceMode`

    Kontrolery se restartují, jakmile ukončíte režim údržby.

7. Vraťte se na Azure Portal. Portál nemusí zobrazovat, že jste nainstalovali aktualizace režimu údržby po dobu 24 hodin.