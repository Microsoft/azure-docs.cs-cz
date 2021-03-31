---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "67175037"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Instalace oprav hotfix režimu údržby přes Windows PowerShell pro StorSimple
> [!IMPORTANT]
> V režimu údržby musíte nejprve použít opravu hotfix na jednom řadiči a pak na druhém řadiči.
> 
> 

1. Umístěte zařízení do režimu údržby. Pokyny, jak zadat režim údržby, najdete v části [Krok 2: Zadejte režim údržby](../articles/storsimple/storsimple-update-device.md#step2) .
2. Chcete-li použít opravu hotfix, zadejte:
   
     `Start-HcsHotfix` 
3. Po zobrazení výzvy zadejte cestu ke sdílené síťové složce, která obsahuje soubory oprav hotfix.
4. Zobrazí se výzva k potvrzení. Pokud chcete pokračovat v instalaci opravy hotfix, zadejte **Y** .
5. Po použití opravy hotfix na jednom řadiči se přihlaste k druhému kontroleru. Použijte opravu hotfix jako u předchozího kontroleru.
6. Po použití oprav hotfix Ukončete režim údržby. Pokyny najdete v části [Krok 4: ukončení režimu údržby](../articles/storsimple/storsimple-update-device.md#step4) .

