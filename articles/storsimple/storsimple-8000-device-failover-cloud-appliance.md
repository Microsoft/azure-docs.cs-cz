---
title: StorSimple převzetí služeb při selhání, zotavení po havárii do řešení StorSimple Cloud Appliance | Dokumentace Microsoftu
description: Zjistěte, jak převzít služby při selhání fyzického zařízení StorSimple 8000 series do cloudového zařízení.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 45c521fd044fa258b8052a3f0de48784cf4160e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584326"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Převzetí služeb při selhání do vašeho řešení StorSimple Cloud Appliance

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky potřebné k převzetí služeb při selhání fyzického zařízení StorSimple 8000 series pro řešení StorSimple Cloud Appliance, pokud nedojde k havárii. StorSimple využívá funkce převzetí služeb při selhání zařízení pro migraci dat ze zdrojové fyzické zařízení v datovém centru do cloudového zařízení běží v Azure. Pokyny v tomto kurzu platí pro fyzická zařízení řady StorSimple 8000 a cloud Appliance s software verze Update 3 a novější.

Další informace o převzetí služeb při selhání zařízení a jak se používá k zotavení po havárii, přejděte na [převzetí služeb při selhání a zotavení po havárii pro zařízení StorSimple 8000 series](storsimple-8000-device-failover-disaster-recovery.md).

Převzetí služeb při selhání fyzického zařízení StorSimple do jiného fyzického zařízení, přejděte na [převzetí služeb při selhání do fyzického zařízení StorSimple](storsimple-8000-device-failover-physical-device.md). Převzetí služeb při selhání zařízení na sebe sama, přejděte na [převzetí služeb při selhání do stejného fyzického zařízení StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste si prohlédli důležité informace týkající se převzetí služeb při selhání zařízení. Další informace najdete v části [častá rozhodnutí při převzetí služeb při selhání zařízení](storsimple-8000-device-failover-disaster-recovery.md).

- Potřebujete řešení StorSimple Cloud Appliance vytvořený a nakonfigurovaný před spuštěním této procedury. Pokud spuštění aktualizace 3 verze softwaru nebo novější, zvažte použití cloudového zařízení 8020 zotavení po Havárii. 8020 model má 64 TB a používá službu Premium Storage. Další informace najdete v části [nasadit a spravovat řešení StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Postup převzetí služeb při selhání do cloudového zařízení

Následující kroky obnovit zařízení do cílového řešení StorSimple Cloud Appliance.

1.  Ověřte, že má kontejner svazků, které chcete převzít služby při selhání přidružené cloudové snímky. Další informace najdete v části [služby Správce zařízení StorSimple používá k vytvoření zálohy](storsimple-8000-manage-backup-policies-u2.md).
2. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V **zařízení** okno, přejděte k seznamu zařízení připojených k vaší službě.
    ![Vyberte zařízení.](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Vyberte a klikněte na zdrojové zařízení. Zdrojové zařízení má kontejnery svazků, které chcete převzít služby při selhání. Přejděte na **Nastavení > kontejnery svazků**.

    ![Vyberte zařízení.](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Vyberte kontejner svazků, která chcete převzít služby při selhání na jiné zařízení. Klikněte na kontejner svazků pro zobrazení seznamu svazků v tomto kontejneru. Vyberte svazek, klikněte pravým tlačítkem a klikněte na **přepnout do režimu Offline** uvedení svazku do režimu offline.

    ![Vyberte zařízení.](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Tento postup opakujte pro všechny svazky v kontejneru svazků.

     ![Vyberte zařízení.](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Předchozí krok opakujte pro všechny kontejnery svazků, které chcete převzít služby při selhání na jiné zařízení.

7. Přejděte zpět **zařízení** okno. Na panelu příkazů klikněte na tlačítko **převzetí služeb při selhání**.

    ![Klikněte na selhání přes](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. V **převzetí služeb při selhání** okno, proveďte následující kroky:
   
    1. Klikněte na tlačítko **zdroj**. Vyberte kontejnery svazků pro převzetí služeb při selhání. **Zobrazí se pouze kontejnery svazků s související cloudové snímky a offline svazky.**
        ![Výběr zdroje](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Klikněte na tlačítko **cílové**. Vyberte z rozevíracího seznamu dostupných zařízení cíl cloudového zařízení. **V seznamu se zobrazují jenom zařízení, které mají dostatečnou kapacitu tak, aby vyhovovaly zdroj kontejnery svazků.**

        ![Výběr cíle](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Zkontrolujte nastavení převzetí služeb při selhání v rámci **Souhrn** a zaškrtněte políčko označující, že svazky ve vybrané kontejnery svazků jsou offline. 

        ![Zkontrolujte nastavení převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Vytvoření úlohy převzetí služeb při selhání. Pokud chcete monitorovat úlohu převzetí služeb při selhání, klikněte na úlohu oznámení.

    ![Monitorování úlohy převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Po dokončení převzetí služeb, přejděte zpět na **zařízení** okno.

    1. Vyberte zařízení, které se používá jako cíl převzetí služeb.

       ![Vyberte zařízení.](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Klikněte na tlačítko **kontejnery svazků**. Všechny kontejnery svazků, spolu s svazků ze staré zařízení by měla být uvedená.

       Pokud kontejner svazků, které převzetí služeb při selhání se místně připojené svazky, tyto svazky se převzetí služeb při selhání jako vrstvené svazky. Řešení StorSimple Cloud Appliance místně připojené svazky nepodporují.

       ![Kontejnery svazků cílové zobrazení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Další postup

* Po provedení převzetí služeb při selhání, budete muset [Deaktivování nebo odstranění zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Informace o tom, jak použít službu StorSimple Device Manager přejděte na [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

