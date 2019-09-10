---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 1d52117440028c75b249f469f2b3576c2ab1c5c5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "67175671"
---
Posledním krokem je připravit zařízení k odeslání. V tomto kroku se všechny sdílené složky zařízení převedou do režimu offline. Jakmile tento proces zahájíte, ztratíte přístup ke sdíleným složkám.

> [!IMPORTANT]
> Příprava k odeslání je povinná, protože se během ní označí příznakem data, která neodpovídají zásadám vytváření názvů v Azure. Přeskočení tohoto kroku může vést k selháním nahrávání dat z důvodu nevyhovujících dat.

1. Přejděte do části **Připravit k odeslání** a klikněte na **Zahájit přípravu**. Ve výchozím nastavení se během kopírování dat počítají kontrolní součty. Příprava k odeslání dokončí výpočet kontrolních součtů a vytvoří seznam souborů (*soubor BOM*). Výpočet kontrolního součtu může v závislosti na velikosti vašich dat trvat i několik hodin nebo dnů. 
   
    ![Příprava k odeslání 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Pokud z nějakého důvodu chcete přípravu zařízení zastavit, klikněte na **Zastavit přípravu**. Později můžete v přípravě k odeslání pokračovat.
        
    ![Příprava k odeslání 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. Spustí se příprava k odeslání a sdílené složky zařízení přejdou do režimu offline. Jakmile bude zařízení připravené, zobrazí se připomenutí, že si máte stáhnout expediční štítek.

    ![Připomenutí stažení expedičního štítku](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. Po dokončení přípravy zařízení se stav zařízení změní na *Připraveno k odeslání* a zařízení se uzamkne.
        
    ![Příprava k odeslání 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Pokud chcete do zařízení zkopírovat více dat, můžete ho odemknout, zkopírovat další data a znovu spustit přípravu k odeslání.

    Pokud v tomto kroku dojde k nějakým chybám, budete si muset stáhnout protokol chyb a vyřešit je. Po vyřešení chyb spusťte **přípravu k odeslání**.

4. Po úspěšném dokončení přípravy k odeslání (bez chyb) si stáhněte seznam souborů (označovaný také jako manifest) zkopírovaných v rámci tohoto procesu. Později můžete tento seznam použít k ověření nahrání souborů do Azure. Další informace najdete v tématu popisujícím [zkoumání souborů BOM během přípravy k odeslání](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Příprava k odeslání 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Vypněte zařízení. Přejděte na stránku **Vypnout nebo restartovat** a klikněte na **Vypnout**. Po zobrazení výzvy k potvrzení pokračujte kliknutím na **OK**.

6. Odpojte kabely. Dalším krokem je odeslat zařízení do Microsoftu.
