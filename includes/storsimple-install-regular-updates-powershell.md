---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175040"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalace pravidelných aktualizací prostřednictvím prostředí Windows PowerShell pro StorSimple
1. Otevřete sériovou konzolu zařízení a vyberte možnost 1, **Přihlaste se s úplným přístupem**. Zadejte heslo. Výchozí heslo je *Password1*. 
2. Na příkazovém řádku zadejte:
   
     `Get-HcsUpdateAvailability`
   
    Budete upozorněni, pokud jsou k dispozici aktualizace a zda jsou rušivé nebo nerušivé.
3. Na příkazovém řádku zadejte:
   
     `Start-HcsUpdate`
   
    Spustí se proces aktualizace.

> [!IMPORTANT]
> * Tento příkaz se vztahuje pouze na pravidelné aktualizace. Tento příkaz spustíte pouze na jednom řadiči, ale oba řadiče budou aktualizovány. 
> * Během procesu aktualizace si můžete všimnout převzetí služeb při selhání řadiče. převzetí služeb při selhání však nebude mít vliv na dostupnost systému nebo provoz.
> 
> 

