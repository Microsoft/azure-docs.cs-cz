---
title: Obnovení služeb při selhání a zotavení po havárii do cloudového zařízení StorSimple
description: Přečtěte si, jak přepojit fyzické zařízení řady StorSimple 8000 na cloudové zařízení.
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
ms.openlocfilehash: 347b899608d4322a7873b9f80f38ca1c767194d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468741"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Převzetí služeb při selhání do vašeho cloudového zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky potřebné k převzetí služeb při selhání fyzické zařízení řady StorSimple 8000 na zařízení StorSimple Cloud Appliance, pokud dojde k havárii. StorSimple používá funkci převzetí služeb při selhání zařízení k migraci dat ze zdrojového fyzického zařízení v datovém centru do cloudového zařízení spuštěného v Azure. Pokyny v tomto kurzu platí pro StorSimple 8000 řady fyzických zařízení a cloudových zařízení se systémem verze softwaru aktualizace 3 a novější.

Další informace o převzetí služeb při selhání zařízení a způsobu jeho zotavení z havárie naleznete v části [Převzetí služeb při selhání a zotavení po havárii pro zařízení řady StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Chcete-li převzetí služeb při selhání zařízení StorSimple fyzické zařízení do jiného fyzického zařízení, přejděte k [převzetí služeb při selhání na storSimple fyzické zařízení](storsimple-8000-device-failover-physical-device.md). Chcete-li převzetí služeb při selhání zařízení k sobě, přejděte k [převzetí služeb při selhání na stejné fyzické zařízení StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste zkontrolovali důležité informace o převzetí služeb při selhání zařízení. Další informace naleznete [v části Běžné důležité informace o převzetí služeb při selhání zařízení](storsimple-8000-device-failover-disaster-recovery.md).

- Před spuštěním tohoto postupu musíte mít vytvořeno a nakonfigurováno zařízení StorSimple Cloud Appliance. Pokud používáte verzi softwaru aktualizace 3 nebo novější, zvažte použití cloudového zařízení 8020 pro zotavení po havárii. Model 8020 má 64 TB a využívá premium storage. Další informace naleznete v [najděte nasazení a správu StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Kroky k převzetí služeb při selhání do cloudového zařízení

Proveďte následující kroky k obnovení zařízení na cílové StorSimple Cloud Appliance.

1.  Ověřte, zda kontejner svazku, který chcete přepojit převzetím služeb při selhání, má přidružené snímky cloudu. Další informace naleznete v části [Vytvoření záloh pomocí služby StorSimple Device Manager](storsimple-8000-manage-backup-policies-u2.md).
2. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V okně **Zařízení** přejděte na seznam zařízení připojených k vaší službě.
    ![Vybrat zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Vyberte zdrojové zařízení a klikněte na něj. Zdrojové zařízení má kontejnery svazku, které chcete převést na služebnou služeb. Přejděte **na Nastavení > kontejnery svazků**.

    ![Vybrat zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Vyberte kontejner svazku, který chcete převést na jiné zařízení. Kliknutím na kontejner svazků zobrazíte seznam svazků v rámci tohoto kontejneru. Vyberte svazek, klikněte pravým tlačítkem myši a kliknutím na **Převést offline** přepnutí montovny do offline.

    ![Vybrat zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Tento postup opakujte pro všechny svazky v kontejneru svazků.

     ![Vybrat zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Opakujte předchozí krok pro všechny kontejnery svazku, které chcete převést na jiné zařízení.

7. Vraťte se k noži **Zařízení.** Na panelu příkazů klepněte na **příkaz Převzetí služeb při selhání**.

    ![Klikněte na převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. V okně **Převzetí služeb při selhání** proveďte následující kroky:
   
    1. Klepněte na **položku Zdroj**. Vyberte kontejnery svazků, které chcete převést na služebnou služeb. **Zobrazí se pouze kontejnery svazků s přidruženými cloudovými snímky a offline svazky.**
        ![Výběr zdroje](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Klepněte na **cíl**. Vyberte cílové cloudové zařízení z rozevíracího seznamu dostupných zařízení. **V seznamu jsou zobrazena pouze zařízení, která mají dostatečnou kapacitu pro umístění kontejnerů zdrojového svazku.**

        ![Výběr cíle](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Zkontrolujte nastavení převzetí služeb při selhání v části **Souhrn** a zaškrtněte políčko označující, že svazky ve vybraných kontejnerech svazků jsou offline. 

        ![Kontrola nastavení převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Je vytvořena úloha převzetí služeb při selhání. Chcete-li úlohu převzetí služeb při selhání sledovat, klepněte na oznámení úlohy.

    ![Monitorování úlohy převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Po dokončení převzetí služeb při selhání se vraťte do okna **Zařízení.**

    1. Vyberte zařízení, které bylo použito jako cíl pro převzetí služeb při selhání.

       ![Vybrat zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Klepněte na **položku Kontejnery svazků**. Měly by být uvedeny všechny objemové kontejnery spolu se svazky ze starého zařízení.

       Pokud kontejner svazku, který jste převzali, má místně vázaných svazků, tyto svazky se splní jako vrstvené svazky. Místně vázaných svazků nejsou podporovány na StorSimple Cloud Appliance.

       ![Zobrazit kontejnery cílového svazku](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Další kroky

* Po provedení převzetí služeb při selhání bude pravděpodobně nutné [deaktivovat nebo odstranit zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Informace o použití služby StorSimple Device Manager naleznete v [části Správa zařízení StorSimple pomocí služby StorSimple Device Manager](storsimple-8000-manager-service-administration.md).

