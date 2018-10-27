---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 36a014fbff30c32a7149a83907c9586bc3b2f01a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164930"
---
<!--author=SharS last changed: 11/18/16-->

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

