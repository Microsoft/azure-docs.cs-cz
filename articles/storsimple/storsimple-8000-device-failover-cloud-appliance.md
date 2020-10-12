---
title: Převzetí služeb při selhání a zotavení po havárii na StorSimple Cloud Appliance
description: Přečtěte si, jak převzít služby při selhání fyzického zařízení řady StorSimple 8000 na cloudové zařízení.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 18b34ff466f3935cb5cd18d46e6d26e36e756a9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91774456"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Převzetí služeb při selhání na StorSimple Cloud Appliance

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky nutné při převzetí služeb při selhání fyzického zařízení řady StorSimple 8000 na StorSimple Cloud Appliance, pokud dojde k havárii. StorSimple využívá funkci převzetí služeb při selhání zařízení k migraci dat ze zdrojového fyzického zařízení v datacentru do cloudového zařízení běžícího v Azure. Pokyny v tomto kurzu se vztahují na fyzická zařízení řady StorSimple 8000 a cloudová zařízení s verzemi softwaru Update 3 a novější.

Další informace o převzetí služeb při selhání zařízení a způsobu jeho použití k zotavení po havárii najdete v tématu [převzetí služeb při selhání a zotavení po havárii pro zařízení s StorSimple 8000 series](storsimple-8000-device-failover-disaster-recovery.md).

Pokud chcete převzít služby při selhání fyzického zařízení StorSimple na jiné fyzické zařízení, přečtěte si [převzetí služeb při selhání na fyzické zařízení StorSimple](storsimple-8000-device-failover-physical-device.md). Pokud chcete převzít služby zařízení při selhání, převzetí [služeb při selhání na stejné fyzické zařízení StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste si prošli požadavky na převzetí služeb při selhání zařízení. Další informace najdete v [častých otázkách pro převzetí služeb zařízení při selhání](storsimple-8000-device-failover-disaster-recovery.md).

- Před spuštěním tohoto postupu musíte mít vytvořenou a nakonfigurovanou StorSimple Cloud Appliance. Pokud používáte verzi softwaru Update 3 nebo novější, zvažte použití cloudového zařízení 8020 pro DR. Model 8020 má 64 TB a používá Premium Storage. Další informace najdete na webu [nasazení a správa StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Postup při převzetí služeb při selhání do cloudového zařízení

Provedením následujících kroků obnovíte zařízení do cílového StorSimple Cloud Appliance.

1.  Ověřte, že kontejner svazků, u kterého chcete převzít služby při selhání, má přidružené cloudové snímky. Další informace najdete v pro [vytváření záloh pomocí služby StorSimple Device Manager](storsimple-8000-manage-backup-policies-u2.md).
2. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V okně **zařízení** přejdete na seznam zařízení, která jsou připojená k vaší službě.
    ![Vybrat zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Vyberte zdrojové zařízení a klikněte na něj. Zdrojové zařízení obsahuje kontejnery svazků, u kterých chcete převzít služby při selhání. Přejít na **nastavení > kontejnery svazků**.

    ![Vyberte zařízení 2.](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Vyberte kontejner svazků, u kterého chcete převzít služby při selhání jiného zařízení. Kliknutím na kontejner svazků zobrazíte seznam svazků v rámci tohoto kontejneru. Vyberte svazek, klikněte pravým tlačítkem myši a kliknutím na tlačítko **Přepnout** do režimu offline zaveďte svazek do režimu offline.

    ![Vyberte zařízení 3.](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Tento postup opakujte pro všechny svazky v kontejneru svazků.

     ![Vyberte zařízení 4.](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Opakujte předchozí krok u všech kontejnerů svazků, u kterých chcete převzít služby při selhání do jiného zařízení.

7. Vraťte se do okna **zařízení** . Na panelu příkazů klikněte na převzetí **služeb při selhání**.

    ![Klikněte na převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. V okně **převzetí služeb při selhání** proveďte následující kroky:
   
    1. Klikněte na **zdroj**. Vyberte kontejnery svazků pro převzetí služeb při selhání. **Zobrazují se jenom kontejnery svazků s přidruženými snímky cloudu a offline svazky.**
        ![Výběr zdroje](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Klikněte na **cíl**. Z rozevíracího seznamu dostupných zařízení vyberte cílové cloudové zařízení. **V seznamu se zobrazí pouze zařízení s dostatečnou kapacitou pro kontejnery zdrojových svazků.**

        ![Výběr cíle](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. V části **Souhrn** zkontrolujte nastavení převzetí služeb při selhání a zaškrtněte políčko s oznámením, že svazky ve vybraných kontejnerech svazků jsou offline. 

        ![Kontrola nastavení převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Vytvoří se úloha převzetí služeb při selhání. Pokud chcete monitorovat úlohu převzetí služeb při selhání, klikněte na oznámení úlohy.

    ![Sledovat úlohu převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Po dokončení převzetí služeb při selhání se vraťte do okna **zařízení** .

    1. Vyberte zařízení, které se použilo jako cíl pro převzetí služeb při selhání.

       ![Vyberte zařízení 5.](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Klikněte na **kontejnery svazků**. Měly by být uvedené všechny kontejnery svazků společně se svazky ze starého zařízení.

       Pokud je kontejner svazků, u kterého jste převzali zařízení, místně připnuté svazky, u těchto svazků dojde k převzetí služeb při selhání jako vrstvené svazky. Místně připojené svazky se na StorSimple Cloud Appliance nepodporují.

       ![Zobrazit kontejnery cílového svazku](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Další kroky

* Po provedení převzetí služeb při selhání možná budete muset [zařízení StorSimple deaktivovat nebo odstranit](storsimple-8000-deactivate-and-delete-device.md).

* Informace o tom, jak používat službu StorSimple Device Manager, najdete v článku [použití služby StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

