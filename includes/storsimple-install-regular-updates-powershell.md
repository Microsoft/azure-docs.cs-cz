---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175040"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Chcete-li nainstalovat pravidelné aktualizace přes Windows PowerShell pro StorSimple
1. Otevřete konzoly sériového portu zařízení a vyberte možnost 1, **přihlášení pomocí úplný přístup**. Zadejte heslo. Výchozí heslo je *Heslo1*. 
2. Na příkazovém řádku zadejte:
   
     `Get-HcsUpdateAvailability`
   
    Pokud jsou k dispozici aktualizace, a zda jsou rušivé nebo nenarušující aktualizace, budete upozorněni.
3. Na příkazovém řádku zadejte:
   
     `Start-HcsUpdate`
   
    Spustí se proces aktualizace.

> [!IMPORTANT]
> * Tento příkaz se vztahuje pouze na pravidelné aktualizace. Tento příkaz spustit na jenom jeden řadič, ale oba kontrolery se pak zaktualizuje. 
> * Můžete si všimnout převzetí služeb kontroleru při během procesu aktualizace. převzetí služeb při selhání ale neovlivní dostupnost systému nebo operace.
> 
> 

