---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: a78bce9f6e714aa5564791f6bb63996554beabf1
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375813"
---
Posledním krokem je připravit zařízení k odeslání. V tomto kroku se všechny sdílené složky zařízení převedou do režimu offline. Jakmile tento proces zahájíte, ztratíte přístup ke sdíleným složkám.

> [!IMPORTANT]
> Příprava k odeslání je povinná, protože se během ní označí příznakem data, která neodpovídají zásadám vytváření názvů v Azure. Tento krok zabraňuje potenciálnímu selhání nahrávání z důvodu nevyhovujících dat.

1. Přejděte do části **Připravit k odeslání** a klikněte na **Zahájit přípravu**. Ve výchozím nastavení se během kopírování dat počítají kontrolní součty. Příprava k odeslání dokončí výpočet kontrolních součtů a vytvoří seznam souborů ( *soubor BOM* ). Výpočet kontrolního součtu může v závislosti na velikosti vašich dat trvat i několik hodin nebo dnů. 
   
    ![Příprava k odeslání 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Pokud z nějakého důvodu chcete přípravu zařízení zastavit, klikněte na **Zastavit přípravu**. Později můžete v přípravě k odeslání pokračovat.
        
    ![Příprava k odeslání 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. Spustí se příprava k odeslání a sdílené složky zařízení přejdou do režimu offline. <!--You see a reminder to download the shipping label once the device is ready.--> Po dokončení přípravy zařízení se stav zařízení změní na *Připraveno k odeslání* a zařízení se uzamkne.
        
    ![Příprava k odeslání 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Pokud chcete do zařízení zkopírovat více dat, můžete ho odemknout, zkopírovat další data a znovu spustit přípravu k odeslání.

    Pokud v tomto kroku došlo k nějakým chybám, stav se aktualizuje na *Kontrola dokončena s chybami*. Odemkněte zařízení, přejděte na stránku **Připojit a kopírovat** , stáhněte si seznam problémů a vyřešte příslušné chyby.

    ![Příprava k odeslání 4](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    Po vyřešení chyb spusťte **přípravu k odeslání**.

4. Po úspěšném dokončení přípravy k odeslání (bez chyb) použijte následující postup:

    1. Poznamenejte si referenční číslo dokončení. V závislosti na zemi, ve které se nacházíte, se toto číslo může vyžadovat pro různé operace.
    2. Stáhněte si seznam souborů, které se v tomto procesu zkopírovaly (označovaný také jako manifest). Později můžete tento seznam použít k ověření nahrání souborů do Azure. Další informace najdete v tématu popisujícím [zkoumání souborů BOM během přípravy k odeslání](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
        ![Příprava k odeslání 5](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. Vyberte a stáhněte pokyny k expedici pro příslušné zařízení. Pokyny k expedici se liší v závislosti na zemi, ve které se nacházíte.
    4. Pokud E-ink nezobrazuje expediční štítek, můžete si tento štítek stáhnout tady. 

5. Vypněte zařízení. Přejděte na stránku **Vypnout nebo restartovat** a klikněte na **Vypnout**. Po zobrazení výzvy k potvrzení pokračujte kliknutím na **OK**.

6. Odpojte kabely. Dalším krokem je odeslat zařízení do Microsoftu.
