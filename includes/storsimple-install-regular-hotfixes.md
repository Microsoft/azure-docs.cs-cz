---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "67175039"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Instalace běžných oprav hotfix přes Windows PowerShell pro StorSimple
1. Připojte se ke konzole sériového portu zařízení. Další informace najdete v části [Krok 1: připojení ke konzole sériového portu](../articles/storsimple/storsimple-update-device.md#step1).
2. V nabídce Konzola sériového portu vyberte možnost 1, **Přihlaste se s úplným přístupem**. Zadejte heslo. Výchozí heslo je **Heslo1**.
3. Na příkazovém řádku zadejte:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Tento příkaz se vztahuje jenom na běžné opravy hotfix. Tento příkaz se spouští jenom na jednom řadiči, ale oba řadiče se aktualizují.
    > Během procesu aktualizace si můžete všimnout převzetí služeb při selhání řadiče. převzetí služeb při selhání však nebude mít vliv na dostupnost systému ani na operaci.

4. Po zobrazení výzvy zadejte cestu ke sdílené síťové složce, která obsahuje soubory oprav hotfix.
5. Zobrazí se výzva k potvrzení. Pokud chcete pokračovat v instalaci opravy hotfix, zadejte **Y** .

