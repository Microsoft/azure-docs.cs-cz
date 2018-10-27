---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9bd2ee708f7d27cff5d07ca7f86d925ca6d2741d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164367"
---
<!--author=SharS last changed: 12/01/15-->

#### <a name="to-enter-maintenance-mode"></a>Chcete-li spustit režim údržby
1. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlášení pomocí úplný přístup**.
2. Zadejte heslo. Výchozí heslo je **Heslo1**.
3. Na příkazovém řádku zadejte
   
     `Enter-HcsMaintenanceMode`
4. Zobrazí se upozornění oznamující, že režimu údržby se přerušit všechny vstupně-výstupní požadavky a server připojení k portálu Azure classic a zobrazí se výzva k potvrzení. Typ **Y** do režimu údržby.
   
    Oba kontrolery se restartuje. Po dokončení restartování se zobrazí další zpráva označující, že je zařízení v režimu údržby.

