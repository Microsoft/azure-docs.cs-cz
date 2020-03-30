---
title: Převzetí služeb při selhání, zotavení po havárii do jiného zařízení StorSimple 8000
description: Přečtěte si, jak přepojit fyzické zařízení řady StorSimple 8000 na jiné fyzické zařízení.
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 9e4e890ab5491e46ffe5ea0e1c168d168f9cc729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468605"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Převzetí služeb při selhání do fyzického zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky potřebné k převzetí služeb při selhání fyzické zařízení řady StorSimple 8000 do jiného fyzického zařízení StorSimple, pokud dojde k havárii. StorSimple používá funkci převzetí služeb při selhání zařízení k migraci dat ze zdrojového fyzického zařízení v datovém centru do jiného fyzického zařízení. Pokyny v tomto kurzu platí pro StorSimple 8000 série fyzických zařízení se systémem verze softwaru Update 3 a novější.

Další informace o převzetí služeb při selhání zařízení a způsobu jeho zotavení z havárie naleznete v části [Převzetí služeb při selhání a zotavení po havárii pro zařízení řady StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Chcete-li převzetí služeb při selhání zařízení StorSimple na StorSimple cloud appliance, přejděte k [převzetí služeb při selhání na StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Chcete-li převzetí služeb při selhání fyzické zařízení k sobě, přejděte k [převzetí služeb při selhání na stejné fyzické zařízení StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste zkontrolovali důležité informace o převzetí služeb při selhání zařízení. Další informace naleznete [v části Běžné důležité informace o převzetí služeb při selhání zařízení](storsimple-8000-device-failover-disaster-recovery.md).

- Musíte mít v datovém centru nasazené fyzické zařízení řady StorSimple 8000. Zařízení musí spustit verzi softwaru aktualizace 3 nebo novější. Další informace najdete v [části Nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Kroky převzetí služeb při selhání do fyzického zařízení

Chcete-li zařízení obnovit do cílového fyzického zařízení, proveďte následující kroky.

1. Ověřte, zda kontejner svazku, který chcete přepojit převzetím služeb při selhání, má přidružené snímky cloudu. Další informace naleznete v části [Vytvoření záloh pomocí služby StorSimple Device Manager](storsimple-8000-manage-backup-policies-u2.md).
2. Přejděte do Správce zařízení StorSimple a klepněte na **položku Zařízení**. V okně **Zařízení** přejděte na seznam zařízení připojených k vaší službě.
    ![Vybrat zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Vyberte zdrojové zařízení a klikněte na něj. Zdrojové zařízení má kontejnery svazku, které chcete převést na služebnou služeb. Přejděte **na Nastavení > kontejnery svazků**.
4. Vyberte kontejner svazku, který chcete převést na jiné zařízení. Kliknutím na kontejner svazků zobrazíte seznam svazků v rámci tohoto kontejneru. Vyberte svazek, klikněte pravým tlačítkem myši a kliknutím na **Převést offline** přepnutí montovny do offline. Tento postup opakujte pro všechny svazky v kontejneru svazků.
5. Opakujte předchozí krok pro všechny kontejnery svazku, které chcete převést na jiné zařízení.
6. Vraťte se k noži **Zařízení.** Na panelu příkazů klepněte na **příkaz Převzetí služeb při selhání**.
    ![Klikněte na převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. V okně **Převzetí služeb při selhání** proveďte následující kroky:
   
   1. Klepněte na **položku Zdroj**. Zobrazí se kontejnery svazků se svazky přidružené ke snímkům cloudu. Pouze zobrazené kontejnery jsou způsobilé pro převzetí služeb při selhání. V seznamu kontejnerů svazků vyberte kontejnery svazků, které chcete převést na služebnou služeb. **Zobrazí se pouze kontejnery svazků s přidruženými cloudovými snímky a offline svazky.**

       ![Výběr zdroje](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Klepněte na **cíl**. Pro kontejnery svazků vybrané v předchozím kroku vyberte cílové zařízení z rozevíracího seznamu dostupných zařízení. V seznamu jsou zobrazena pouze zařízení, která mají dostatečnou kapacitu pro umístění kontejnerů zdrojového svazku.

        ![Výběr cíle](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Nakonec zkontrolujte všechna nastavení převzetí služeb při selhání v části **Souhrn**. Po kontrole nastavení zaškrtněte políčko označující, že svazky ve vybraných kontejnerech svazků jsou offline. Klikněte na tlačítko **OK**.

       ![Kontrola nastavení převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple vytvoří úlohu převzetí služeb při selhání. Kliknutím na oznámení úlohy můžete sledovat úlohu převzetí služeb při selhání pomocí okna **Úlohy.**

    Pokud kontejner svazku, který jste převzali, obsahuje místní svazky, zobrazí se v kontejneru jednotlivé úlohy obnovení pro každý místní svazek (ne pro vrstvené svazky). Tyto úlohy obnovení může trvat nějakou dobu k dokončení. Je pravděpodobné, že úloha převzetí služeb při selhání může být dokončena dříve. Tyto svazky budou mít místní záruky až po dokončení úloh obnovení.

    ![Monitorování úlohy převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Po dokončení převzetí služeb při selhání přejděte do okna **Zařízení.**
   
   1. Vyberte zařízení, které bylo použito jako cílové zařízení pro proces převzetí služeb při selhání.

       ![Vybrat zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Přejděte do okna **Objemové kontejnery.** Měly by být uvedeny všechny objemové kontejnery spolu se svazky ze starého zařízení.

       ![Zobrazit kontejnery cílového svazku](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Další kroky

* Po provedení převzetí služeb při selhání bude pravděpodobně nutné [deaktivovat nebo odstranit zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Informace o použití služby StorSimple Device Manager naleznete v [části Správa zařízení StorSimple pomocí služby StorSimple Device Manager](storsimple-8000-manager-service-administration.md).

