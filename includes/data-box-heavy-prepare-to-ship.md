---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419570"
---
Posledním krokem je připravit zařízení k odeslání. V tomto kroku se všechny sdílené složky zařízení převedou do režimu offline. Sdílené složky není přístupná po spuštění tohoto procesu.

> [!IMPORTANT]
> Příprava k odeslání není nutné, protože příznaky data, která není v souladu s Azure zásady vytváření názvů. Přeskočení tohoto kroku může vést dat. nahrát chyby způsobené nonkonformní data.

1. Přejděte do části **Připravit k odeslání** a klikněte na **Zahájit přípravu**. Ve výchozím nastavení se zpracovávají kontrolní součty, při kopírování dat. Příprava k odeslání dokončí výpočet kontrolního součtu a vytvoří seznam souborů (označované také jako *BOM soubory* nebo manifest). Výpočet kontrolního součtu může trvat hodiny dnů v závislosti na velikosti vaše data.
   
    ![Příprava k odeslání 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Pokud z nějakého důvodu chcete zastavit přípravu zařízení, klikněte na tlačítko **zastavit přípravu**. Příprava k odeslání později můžete obnovit.
        
    ![Příprava k odeslání 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. Příprava k odeslání spustí a zařízení sdílené složky přejdou do režimu offline. Zobrazí se připomenutí stáhnout Expediční štítek, jakmile zařízení je připravené.

    ![Stáhnout Expediční štítek připomenutí](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. Stav zařízení aktualizuje *připravené k odeslání* a jakmile se dokončí Příprava zařízení je zařízení uzamčené.
        
    ![Příprava k odeslání 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Pokud chcete kopírovat další data zařízení, můžete odemknout zařízení, kopírovat další data, a spusťte přípravu k odeslání znovu.

    Pokud v tomto kroku nejsou chyby, stáhněte si protokol chyb a případné chyby opravte. Po vyřešení chyby spusťte **přípravu k odeslání**.

4. Příprava k odeslání po stažení úspěšně dokončena (s bez chyb), seznam souborů (označované také jako *BOM soubory* nebo manifest) zkopírovány v tomto procesu. 

    ![Stáhněte si seznam souborů nebo BOM](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   Později můžete tento seznam použít k ověření nahrání souborů do Azure. Další informace najdete v tématu [BOM kontrolovat soubory během Příprava k odeslání](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Ukázkový soubor BOM](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Vypněte zařízení. Přejděte na stránku **Vypnout nebo restartovat** a klikněte na **Vypnout**. Po zobrazení výzvy k potvrzení pokračujte kliknutím na **OK**.

    ![Vypnout první uzel zařízení](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Všechny výše uvedené kroky zopakujte pro druhý uzel zařízení.
7. Jakmile se zařízení se úplně vypnout, by jste vypnuli indikátorů LED zádi zařízení. Dalším krokem je odebrat všechny kabely a odešlete zařízení do společnosti Microsoft.
