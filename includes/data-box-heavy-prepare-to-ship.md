---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "66419570"
---
Posledním krokem je připravit zařízení k odeslání. V tomto kroku se všechny sdílené složky zařízení převedou do režimu offline. Jakmile tento proces zahájíte, ztratíte přístup ke sdíleným složkám.

> [!IMPORTANT]
> Příprava k odeslání je povinná, protože se během ní označí příznakem data, která neodpovídají zásadám vytváření názvů v Azure. Přeskočení tohoto kroku může vést k selháním nahrávání dat z důvodu nevyhovujících dat.

1. Přejděte do části **Připravit k odeslání** a klikněte na **Zahájit přípravu**. Ve výchozím nastavení se během kopírování dat počítají kontrolní součty. Příprava k odeslání dokončí výpočet kontrolních součtů a vytvoří seznam souborů (označovaný také jako *soubor BOM* nebo manifest). Výpočet kontrolního součtu může v závislosti na velikosti vašich dat trvat i několik hodin nebo dnů.
   
    ![Příprava k odeslání 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Pokud z nějakého důvodu chcete přípravu zařízení zastavit, klikněte na **Zastavit přípravu**. Později můžete v přípravě k odeslání pokračovat.
        
    ![Příprava k odeslání 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. Spustí se příprava k odeslání a sdílené složky zařízení přejdou do režimu offline. Jakmile bude zařízení připravené, zobrazí se připomenutí, že si máte stáhnout expediční štítek.

    ![Připomenutí stažení expedičního štítku](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. Po dokončení přípravy zařízení se stav zařízení změní na *Připraveno k odeslání* a zařízení se uzamkne.
        
    ![Příprava k odeslání 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Pokud chcete do zařízení zkopírovat více dat, můžete ho odemknout, zkopírovat další data a znovu spustit přípravu k odeslání.

    Pokud v tomto kroku dojde k nějakým chybám, stáhněte si protokol chyb a vyřešte je. Po vyřešení chyb spusťte **přípravu k odeslání**.

4. Po úspěšném dokončení přípravy k odeslání (bez chyb) si stáhněte seznam souborů (označovaný také jako *soubor BOM* nebo manifest) zkopírovaných v rámci tohoto procesu. 

    ![Stažení seznamu souborů neboli souboru BOM](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   Později můžete tento seznam použít k ověření nahrání souborů do Azure. Další informace najdete v tématu popisujícím [zkoumání souborů BOM během přípravy k odeslání](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Ukázkový soubor BOM](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Vypněte zařízení. Přejděte na stránku **Vypnout nebo restartovat** a klikněte na **Vypnout**. Po zobrazení výzvy k potvrzení pokračujte kliknutím na **OK**.

    ![Vypnutí prvního uzlu zařízení](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Zopakujte všechny výše uvedené kroky u druhého uzlu zařízení.
7. Jakmile bude zařízení zcela vypnuté, všechny indikátory LED na zadní straně zařízení budou zhasnuté. Dalším krokem je odpojit všechny kabely a odeslat zařízení do Microsoftu.
