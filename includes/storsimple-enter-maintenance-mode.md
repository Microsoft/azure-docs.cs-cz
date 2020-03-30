---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175034"
---
#### <a name="to-enter-maintenance-mode"></a>Přepne se do režimu údržby
1. V nabídce sériové konzole zvolte možnost 1, **Přihlaste se s úplným přístupem**.
2. Zadejte heslo. Výchozí heslo je **Password1**.
3. Na příkazovém řádku zadejte
   
     `Enter-HcsMaintenanceMode`
4. Zobrazí se varovná zpráva s informací, že režim údržby naruší všechny vstupně-va požadavky a přeruší připojení k klasickému portálu Azure a budete vyzváni k potvrzení. Zadejte **Y,** abyste přepnou režim údržby.
   
    Oba řadiče se restartují. Po dokončení restartování se zobrazí další zpráva oznamující, že zařízení je v režimu údržby.

