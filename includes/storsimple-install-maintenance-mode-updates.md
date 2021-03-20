---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "67175042"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalace aktualizací režimu údržby prostřednictvím Windows PowerShell pro StorSimple
1. Pokud jste to ještě neudělali, přejděte do konzoly sériového portu zařízení a vyberte možnost 1, **Přihlaste se s úplným přístupem**. 
2. Zadejte heslo. Výchozí heslo je **Heslo1**.
3. Na příkazovém řádku zadejte:
   
     `Get-HcsUpdateAvailability` 
4. Budete upozorněni, zda jsou k dispozici aktualizace a zda jsou aktualizace přerušeny nebo nejsou rušivé. Chcete-li použít narušující aktualizace, je třeba umístit zařízení do režimu údržby. Pokyny najdete v části [Krok 2: zadání režimu údržby](../articles/storsimple/storsimple-update-device.md#step2) .
5. Když je zařízení v režimu údržby, zadejte na příkazovém řádku: `Start-HcsUpdate`
6. Zobrazí se výzva k potvrzení. Po potvrzení aktualizací se tyto aktualizace nainstalují do kontroleru, ke kterému aktuálně přistupujete. Po instalaci aktualizací se kontroler restartuje. 
7. Monitoruje stav aktualizací. Zadejte:
   
    `Get-HcsUpdateStatus`
   
    Pokud `RunInProgress` je `True` , aktualizace stále probíhá. Pokud `RunInProgress` je `False` , znamená to, že se aktualizace dokončila.  
8. Když je aktualizace na aktuálním řadiči nainstalována a je restartována, připojte se k druhému kontroleru a proveďte kroky 1 až 6.
9. Po aktualizaci obou řadičů se režim údržby ukončí. Pokyny najdete v části [Krok 4: ukončení režimu údržby](../articles/storsimple/storsimple-update-device.md#step4) .

