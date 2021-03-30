---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "67175203"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Nainstalovat aktualizace režimu údržby prostřednictvím Windows PowerShell pro StorSimple

Když aplikujete aktualizace režimu údržby na zařízení StorSimple, všechny vstupně-výstupní požadavky se pozastaví. Služby, jako je například nestálá paměť s náhodným přístupem (NVRAM) nebo služba clusteringu, jsou zastaveny. Oba řadiče se při zadání nebo ukončení tohoto režimu restartují. Po ukončení tohoto režimu budou všechny služby obnoveny a jsou v pořádku. (Může to trvat několik minut.)

> [!IMPORTANT]
> * Před přechodem do režimu údržby ověřte, že jsou oba řadiče zařízení v Azure Portal v pořádku. Pokud kontroler není v pořádku, [kontaktujte podpora Microsoftu](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) pro další kroky.
> * Když jste v režimu údržby, musíte nejdřív aktualizovat jeden kontroler a druhý kontroler.

1. Použijte k připojení ke konzole sériového výstupu výstup. Postupujte podle podrobných pokynů v článku [Připojení ke konzole sériového portu pomocí klienta PuTTy](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Na příkazovém řádku stiskněte **Enter**. Vyberte možnost 1, **Přihlaste se s úplným přístupem**.

2. Pokud chcete řadič umístit do režimu údržby, zadejte:
    
    `Enter-HcsMaintenanceMode`

    Oba řadiče se restartují do režimu údržby.

3. Nainstalujte aktualizace režimu údržby. Zadejte:

    `Start-HcsUpdate`

    Zobrazí se výzva k potvrzení. Po potvrzení aktualizací se tyto aktualizace nainstalují na kontrolér, ke kterému aktuálně přistupujete. Po instalaci aktualizací se kontroler restartuje.

4. Monitoruje stav aktualizací. Přihlaste se k řadiči partnerského zařízení, protože se aktualizuje aktuální kontroler a nemůže zpracovat žádné jiné příkazy. Zadejte:

    `Get-HcsUpdateStatus`

    Pokud `RunInProgress` je `True` , aktualizace stále probíhá. Pokud `RunInProgress` je `False` , znamená to, že se aktualizace dokončila.

5. Po úspěšném použití aktualizací firmwaru disku a restartování aktualizovaného kontroleru ověřte verzi firmwaru disku. Na aktualizovaném kontroleru zadejte:

    `Get-HcsFirmwareVersion`
   
    Očekávané verze firmwaru disku jsou:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Ukončete režim údržby. Pro každý řadič zařízení zadejte tento příkaz:

    `Exit-HcsMaintenanceMode`

    Kontrolery se restartují, jakmile ukončíte režim údržby.

7. Vraťte se na Azure Portal. Na portálu se nemusí ukázat, že jste nainstalovali aktualizace režimu údržby po dobu 24 hodin.