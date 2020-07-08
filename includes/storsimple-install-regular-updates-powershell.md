---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175040"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalace pravidelných aktualizací prostřednictvím Windows PowerShell pro StorSimple
1. Otevřete konzolu sériového portu zařízení a vyberte možnost 1, **Přihlaste se s úplným přístupem**. Zadejte heslo. Výchozí heslo je *Heslo1*. 
2. Na příkazovém řádku zadejte:
   
     `Get-HcsUpdateAvailability`
   
    Budete upozorněni, zda jsou k dispozici aktualizace a zda jsou aktualizace přerušeny nebo nejsou rušivé.
3. Na příkazovém řádku zadejte:
   
     `Start-HcsUpdate`
   
    Spustí se proces aktualizace.

> [!IMPORTANT]
> * Tento příkaz se vztahuje pouze na běžné aktualizace. Tento příkaz se spouští jenom na jednom řadiči, ale oba řadiče se aktualizují. 
> * Během procesu aktualizace si můžete všimnout převzetí služeb při selhání řadiče. převzetí služeb při selhání však nebude mít vliv na dostupnost systému ani na operaci.
> 
> 

