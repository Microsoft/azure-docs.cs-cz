---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175034"
---
#### <a name="to-enter-maintenance-mode"></a>Přechod do režimu údržby
1. V nabídce Konzola sériového portu klikněte na možnost 1, **Přihlaste se s úplným přístupem**.
2. Zadejte heslo. Výchozí heslo je **Heslo1**.
3. Do příkazového řádku zadejte
   
     `Enter-HcsMaintenanceMode`
4. Zobrazí se zpráva s upozorněním, že v režimu údržby dojde k přerušení všech vstupně-výstupních požadavků a navázání připojení k portálu Azure Classic a budete vyzváni k potvrzení. Zadáním **Y** zadejte režim údržby.
   
    Oba řadiče se restartují. Po dokončení restartování se zobrazí další zpráva oznamující, že zařízení je v režimu údržby.

