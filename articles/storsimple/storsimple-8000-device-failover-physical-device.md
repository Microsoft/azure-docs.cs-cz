---
title: Převzetí služeb při selhání, zotavení po havárii do jiného zařízení StorSimple 8000
description: Přečtěte si, jak převzít služby při selhání fyzického zařízení řady StorSimple 8000 na jiné fyzické zařízení.
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 67d6fb1f3cc359288ed942d915e186542a62b0fc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017130"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Převzetí služeb při selhání u fyzického zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky nutné při převzetí služeb při selhání fyzického zařízení řady StorSimple 8000 na jiné fyzické zařízení StorSimple v případě havárie. StorSimple využívá funkci převzetí služeb při selhání zařízení k migraci dat ze zdrojového fyzického zařízení v datacentru do jiného fyzického zařízení. Pokyny v tomto kurzu se vztahují na fyzická zařízení řady StorSimple 8000, na kterých běží verze softwaru Update 3 a novější.

Další informace o převzetí služeb při selhání zařízení a způsobu jeho použití k zotavení po havárii najdete v tématu [převzetí služeb při selhání a zotavení po havárii pro zařízení s StorSimple 8000 series](storsimple-8000-device-failover-disaster-recovery.md).

Pokud chcete převzít služby při selhání fyzického zařízení StorSimple na StorSimple Cloud Appliance, přečtěte si [převzetí služeb při selhání do StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Pokud chcete převzít služby fyzického zařízení při selhání, přečtěte si [převzetí služeb při selhání na stejné fyzické zařízení StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste si prošli požadavky na převzetí služeb při selhání zařízení. Další informace najdete v [častých otázkách pro převzetí služeb zařízení při selhání](storsimple-8000-device-failover-disaster-recovery.md).

- Musíte mít v datacentru nasazené fyzické zařízení řady StorSimple 8000. V zařízení musí běžet verze softwaru Update 3 nebo novější. Další informace najdete [v poznámkách k nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Postup převzetí služeb při selhání fyzického zařízení

Provedením následujících kroků obnovíte své zařízení na cílové fyzické zařízení.

1. Ověřte, že kontejner svazků, u kterého chcete převzít služby při selhání, má přidružené cloudové snímky. Další informace najdete v pro [vytváření záloh pomocí služby StorSimple Device Manager](storsimple-8000-manage-backup-policies-u2.md).
2. Přejděte na StorSimple Device Manager a pak klikněte na **zařízení**. V okně **zařízení** přejdete na seznam zařízení, která jsou připojená k vaší službě.
    ![Vybrat zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Vyberte zdrojové zařízení a klikněte na něj. Zdrojové zařízení obsahuje kontejnery svazků, u kterých chcete převzít služby při selhání. Přejít na **nastavení > kontejnery svazků**.
4. Vyberte kontejner svazků, u kterého chcete převzít služby při selhání jiného zařízení. Kliknutím na kontejner svazků zobrazíte seznam svazků v rámci tohoto kontejneru. Vyberte svazek, klikněte pravým tlačítkem myši a kliknutím na tlačítko **Přepnout** do režimu offline zaveďte svazek do režimu offline. Tento postup opakujte pro všechny svazky v kontejneru svazků.
5. Opakujte předchozí krok u všech kontejnerů svazků, u kterých chcete převzít služby při selhání do jiného zařízení.
6. Vraťte se do okna **zařízení** . Na panelu příkazů klikněte na převzetí **služeb při selhání**.
    ![Klikněte na převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. V okně **převzetí služeb při selhání** proveďte následující kroky:
   
   1. Klikněte na **zdroj**. Zobrazí se kontejnery svazků se svazky přidruženými ke snímkům v cloudu. Pro převzetí služeb při selhání mají nárok jenom zobrazené kontejnery. V seznamu kontejnerů svazků vyberte kontejnery svazků, u kterých chcete převzít služby při selhání. **Zobrazují se jenom kontejnery svazků s přidruženými snímky cloudu a offline svazky.**

       ![Zvolit zdroj](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Klikněte na **cíl**. V případě kontejnerů svazků vybraných v předchozím kroku vyberte cílové zařízení v rozevíracím seznamu dostupných zařízení. V seznamu se zobrazí pouze zařízení s dostatečnou kapacitou pro kontejnery zdrojových svazků.

        ![Výběr cíle](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Nakonec zkontrolujte všechna nastavení převzetí služeb při selhání v části **Souhrn**. Po zkontrolování nastavení zaškrtněte políčko s oznámením, že svazky ve vybraných kontejnerech svazků jsou offline. Klikněte na **OK**.

       ![Kontrola nastavení převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple vytvoří úlohu převzetí služeb při selhání. Kliknutím na oznámení úlohy můžete monitorovat úlohu převzetí služeb při selhání přes okno **úlohy** .

    Pokud je kontejner svazků, u kterého převzala služby při selhání, místní svazky, pak se v kontejneru zobrazí jednotlivé úlohy obnovení pro každý místní svazek (ne pro vrstvené svazky). Dokončení těchto úloh obnovení může trvat poměrně dlouho. Je možné, že se úloha převzetí služeb při selhání dokončí dříve. Tyto svazky budou mít místní záruky až po dokončení úloh obnovení.

    ![Sledovat úlohu převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Po dokončení převzetí služeb při selhání přejdete do okna **zařízení** .
   
   1. Vyberte zařízení, které se použilo jako cílové zařízení pro proces převzetí služeb při selhání.

       ![Vyberte zařízení 2.](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Otevřete okno **kontejnery svazků** . Měly by být uvedené všechny kontejnery svazků společně se svazky ze starého zařízení.

       ![Zobrazit kontejnery cílového svazku](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Další kroky

* Po provedení převzetí služeb při selhání možná budete muset [zařízení StorSimple deaktivovat nebo odstranit](storsimple-8000-deactivate-and-delete-device.md).
* Informace o tom, jak používat službu StorSimple Device Manager, najdete v článku [použití služby StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

