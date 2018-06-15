---
title: StorSimple převzetí služeb při selhání, zotavení po havárii pro o cloudu zařízení StorSimple | Microsoft Docs
description: Zjistěte, jak při selhání fyzického zařízení řady StorSimple 8000 do cloudu zařízení.
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
ms.openlocfilehash: ec8bebf2854e84a37e84b45564e80fc20b63d8d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874896"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Převzetí služeb při selhání pro vaše zařízení StorSimple cloudu

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky potřebné k převzetí služeb při selhání fyzického zařízení řady StorSimple 8000 a o cloudu zařízení StorSimple Pokud dojde k havárii. StorSimple používá funkci zařízení převzetí služeb při selhání k migraci dat ze zdrojového fyzického zařízení v datovém centru cloudu zařízení běží v Azure. Pokyny v tomto kurzu platí pro řadu fyzického zařízení StorSimple 8000 a cloudu zařízení se systémem verze softwaru Update 3 nebo novější.

Další informace o převzetí služeb při selhání zařízení a jak se používají k zotavení po havárii, přejděte na [převzetí služeb při selhání a zotavení po havárii pro řadu zařízení StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Selhání fyzického zařízení StorSimple na jiné fyzické zařízení, přejděte na [převzetí služeb při selhání fyzického zařízení StorSimple](storsimple-8000-device-failover-physical-device.md). Pokud chcete převzít zařízení sám na sebe, přejděte na [převzetí služeb při selhání do stejného fyzického zařízení StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste si přečetli důležité informace pro převzetí služeb při selhání zařízení. Další informace, přejděte na [častá rozhodnutí při převzetí služeb při selhání zařízení](storsimple-8000-device-failover-disaster-recovery.md).

- Musíte mít cloudu zařízení StorSimple, vytvořený a nakonfigurovaný před spuštěním tohoto postupu. Pokud spuštění aktualizace 3 verze softwaru nebo novější, zvažte použití zařízení 8020 cloudu zotavení po Havárii. 8020 model má 64 TB a používá úložiště Premium. Další informace, přejděte na [nasadit a spravovat zařízení s StorSimple cloudu](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Postup převzetí služeb při selhání do cloudu zařízení

Proveďte následující kroky a v zařízení obnovit do cílového zařízení StorSimple cloudu.

1.  Ověřte, že kontejner svazků, které chcete převzetí služeb při selhání je spojen cloudových snímků. Další informace, přejděte na [služby pomocí Správce zařízení StorSimple vytvářet zálohy](storsimple-8000-manage-backup-policies-u2.md).
2. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V **zařízení** okno, přejděte na seznam zařízení připojená k vaší službě.
    ![Vyberte zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Vyberte a klikněte na vaše zdrojové zařízení. Zdrojové zařízení má kontejnery svazků, které chcete převzetí služeb při selhání. Přejděte na **Nastavení > kontejnery svazků**.

    ![Vyberte zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Vyberte kontejner svazků, které byste chtěli převzetí služeb při selhání na jiné zařízení. Klikněte na tlačítko kontejneru svazků, které chcete zobrazit seznam svazků v tomto kontejneru. Vyberte svazek, klikněte pravým tlačítkem a klikněte na **přepnout do režimu Offline** uvedení svazku do režimu offline.

    ![Vyberte zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Tento postup opakujte pro všechny svazky v kontejneru svazků.

     ![Vyberte zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Předchozí krok opakujte pro všechny kontejnery svazků, které byste chtěli převzetí služeb při selhání na jiné zařízení.

7. Přejděte zpět **zařízení** okno. Na panelu příkazů klikněte na **převzetí služeb při selhání**.

    ![Klikněte na tlačítko selhání přes](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. V **převzetí služeb při selhání** okno, proveďte následující kroky:
   
    1. Klikněte na tlačítko **zdroj**. Vyberte kontejnery svazků pro převzetí služeb při selhání. **Zobrazí se pouze kontejnery svazků přidruženého cloudu snímky a offline svazky.**
        ![Vyberte zdroj](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Klikněte na tlačítko **cíl**. Z rozevíracího seznamu k dispozici zařízení vyberte zařízení s cílový cloud. **V seznamu se zobrazí jenom zařízení, která mají dostatečnou kapacitu, aby dokázala pojmout kontejnery svazků zdroje.**

        ![Vyberte cíl](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Zkontrolujte nastavení převzetí služeb při selhání v rámci **Souhrn** a zaškrtněte políčko označující, zda jsou svazky v kontejnerech vybraný svazek offline. 

        ![Zkontrolujte nastavení převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Bude vytvořena úloha převzetí služeb při selhání. Monitorování úlohy převzetí služeb při selhání, klikněte na úlohu oznámení.

    ![Úloha monitoru převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Po dokončení převzetí služeb při selhání, přejděte zpět na **zařízení** okno.

    1. Vyberte zařízení, která byla použita jako cíl pro převzetí služeb při selhání.

       ![Vyberte zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Klikněte na tlačítko **kontejnery svazků**. Všechny kontejnery svazků, společně s svazky z původního zařízení by měl být uvedený.

       Pokud kontejneru svazků, které při selhání má místně vázaný svazky, jsou tyto svazky při selhání jako vrstvené svazky. O cloudu zařízení StorSimple nepodporuje místně vázaných svazků.

       ![Kontejnery svazků cílové zobrazení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Další kroky

* Po provedení převzetí služeb při selhání, budete muset [deaktivujte nebo odstraňte zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Informace o tom, jak používat službu StorSimple Manager zařízení, přejděte na [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

