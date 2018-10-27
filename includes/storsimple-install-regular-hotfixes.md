---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 77fc95c53ba89c3905ebd1aec785e22f42339369
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165562"
---
<!--author=SharS last changed: 9/17/15-->

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

