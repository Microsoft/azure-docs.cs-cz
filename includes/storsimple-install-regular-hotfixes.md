---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888763"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Pro instalaci regulární oprav hotfix přes Windows PowerShell pro StorSimple
1. Připojte se ke konzole sériového portu zařízení. Další informace najdete v tématu [krok 1: Připojte se ke konzole sériového portu](../articles/storsimple/storsimple-update-device.md#step1).
2. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlášení pomocí úplný přístup**. Zadejte heslo. Výchozí heslo je **Heslo1**.
3. Na příkazovém řádku zadejte:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Tento příkaz se vztahuje pouze na regulární opravy hotfix. Tento příkaz spustit na jenom jeden řadič, ale oba kontrolery se pak zaktualizuje.
    > Můžete si všimnout převzetí služeb kontroleru při během procesu aktualizace. převzetí služeb při selhání ale neovlivní dostupnost systému nebo operace.

4. Po zobrazení výzvy zadejte cestu sdílené síťové složky, která obsahuje soubory oprav hotfix.
5. Zobrazí se výzva k potvrzení. Typ **Y** pokračovat v instalaci oprav hotfix.

