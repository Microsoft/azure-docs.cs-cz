---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175037"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Instalace oprav hotfix v režimu údržby prostřednictvím prostředí Windows PowerShell for StorSimple
> [!IMPORTANT]
> V režimu údržby je třeba nejprve použít opravu hotfix na jeden řadič a poté na druhý ovladač.
> 
> 

1. Umístěte zařízení do režimu údržby. Pokyny, jak vstoupit do režimu údržby, naleznete [v kroku 2: Zadejte režim údržby.](../articles/storsimple/storsimple-update-device.md#step2)
2. Chcete-li použít opravu hotfix, zadejte:
   
     `Start-HcsHotfix` 
3. Po zobrazení výzvy zadejcestu ke sdílené síťové složce, která obsahuje soubory oprav hotfix.
4. Zobrazí se výzva k potvrzení. Chcete-li pokračovat v instalaci opravy hotfix, zadejte **zadejte Y.**
5. Po použití opravy hotfix na jednom řadiči se přihlaste k druhému řadiči. Použijte opravu hotfix stejně jako u předchozího ovladače.
6. Po použití oprav hotfix ukončete režim údržby. Pokyny naleznete [v kroku 4: Ukončete režim údržby.](../articles/storsimple/storsimple-update-device.md#step4)

