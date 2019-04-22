---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: a8070d25e2606d8ad72ac231a0a208072c612c5c
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59736990"
---
Posledním krokem je připravit zařízení k odeslání. V tomto kroku se všechny sdílené složky zařízení převedou do režimu offline. Sdílené složky není přístupná po spuštění tohoto procesu.

> [!IMPORTANT]
> Příprava k odeslání není nutné, protože příznaky data, která není v souladu s Azure zásady vytváření názvů. Přeskočení tohoto kroku může vést dat. nahrát chyby způsobené nonkonformní data.

1. Přejděte do části **Připravit k odeslání** a klikněte na **Zahájit přípravu**. Kontrolní součty se standardně počítají na řádku během přípravě k odeslání. Výpočet kontrolního součtu může trvat hodiny dnů v závislosti na velikosti vaše data. 
   
    ![Příprava k odeslání 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Pokud z nějakého důvodu chcete zastavit přípravu zařízení, klikněte na tlačítko **zastavit přípravu**. Příprava k odeslání později můžete obnovit.
        
    ![Příprava k odeslání 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. Příprava k odeslání spustí a zařízení sdílené složky přejdou do režimu offline. Zobrazí se připomenutí stáhnout Expediční štítek, jakmile zařízení je připravené.

    ![Stáhnout Expediční štítek připomenutí](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. Stav zařízení aktualizuje *připravené k odeslání* a jakmile se dokončí Příprava zařízení je zařízení uzamčené.
        
    ![Příprava k odeslání 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Pokud chcete kopírovat další data zařízení, můžete odemknout zařízení, kopírovat další data, a spusťte přípravu k odeslání znovu.

    Pokud v tomto kroku nejsou chyby, je potřeba stáhnout v protokolu chyb a případné chyby opravte. Po vyřešení chyby spusťte **přípravu k odeslání**.

4. Po Příprava k odeslání se úspěšně dokončí (s bez chyb), stáhněte si seznam soubory (označované také jako manifest) zkopírovány v tomto procesu. Později můžete tento seznam použít k ověření nahrání souborů do Azure.
        
    ![Příprava k odeslání 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Vypněte zařízení. Přejděte na stránku **Vypnout nebo restartovat** a klikněte na **Vypnout**. Po zobrazení výzvy k potvrzení pokračujte kliknutím na **OK**.

6. Odpojte kabely. Dalším krokem je odeslat zařízení do Microsoftu.
