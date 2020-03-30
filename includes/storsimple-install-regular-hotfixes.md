---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175039"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Instalace běžných oprav hotfix prostřednictvím prostředí Windows PowerShell pro službu StorSimple
1. Připojte se k sériové konzoli zařízení. Další informace naleznete [v tématu Krok 1: Připojení ke konzole sériového zařízení](../articles/storsimple/storsimple-update-device.md#step1).
2. V nabídce sériové konzole vyberte možnost 1, **Přihlaste se s úplným přístupem**. Zadejte heslo. Výchozí heslo je **Password1**.
3. Na příkazovém řádku zadejte:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Tento příkaz se vztahuje pouze na běžné opravy hotfix. Tento příkaz spustíte pouze na jednom řadiči, ale oba řadiče budou aktualizovány.
    > Během procesu aktualizace si můžete všimnout převzetí služeb při selhání řadiče. převzetí služeb při selhání však nebude mít vliv na dostupnost systému nebo provoz.

4. Po zobrazení výzvy zadejcestu ke sdílené síťové složce, která obsahuje soubory oprav hotfix.
5. Zobrazí se výzva k potvrzení. Chcete-li pokračovat v instalaci opravy hotfix, zadejte **zadejte Y.**

