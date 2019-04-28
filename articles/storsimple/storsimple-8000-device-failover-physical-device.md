---
title: StorSimple převzetí služeb při selhání, zotavení po havárii do fyzického zařízení StorSimple 8000 series | Dokumentace Microsoftu
description: Zjistěte, jak převzít služby při selhání fyzického zařízení StorSimple 8000 series do jiného fyzického zařízení.
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
ms.openlocfilehash: 5fcf95a1a3033a5150945dbd841f12d50ebb023b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577181"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Převzetí služeb při selhání do fyzického zařízení StorSimple 8000 series

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky potřebné k převzetí služeb při selhání fyzického zařízení StorSimple 8000 series do jiného fyzického zařízení StorSimple, pokud nedojde k havárii. StorSimple využívá funkce převzetí služeb při selhání zařízení pro migraci dat ze zdrojové fyzické zařízení v datovém centru do jiného fyzického zařízení. Pokyny v tomto kurzu platí pro StorSimple řady 8000 fyzických zařízení, které používají software verze Update 3 a novější.

Další informace o převzetí služeb při selhání zařízení a jak se používá k zotavení po havárii, přejděte na [převzetí služeb při selhání a zotavení po havárii pro zařízení StorSimple 8000 series](storsimple-8000-device-failover-disaster-recovery.md).

Převzetí služeb při selhání fyzického zařízení StorSimple k řešení StorSimple Cloud Appliance, přejděte na [převzetí služeb při selhání do cloudového zařízení StorSimple](storsimple-8000-device-failover-cloud-appliance.md). Převzetí služeb při selhání fyzické zařízení na sebe sama, přejděte na [převzetí služeb při selhání do stejného fyzického zařízení StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste si prohlédli důležité informace týkající se převzetí služeb při selhání zařízení. Další informace najdete v části [častá rozhodnutí při převzetí služeb při selhání zařízení](storsimple-8000-device-failover-disaster-recovery.md).

- Musíte mít zařízení fyzické řady StorSimple 8000 nasazeny v datovém centru. Zařízení musí používat s aktualizací Update 3 nebo novější verze softwaru. Další informace najdete v části [nasazení zařízení StorSimple v místním](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Postup převzetí služeb při selhání do fyzického zařízení

Proveďte následující kroky a obnovte zařízení do cílového fyzického zařízení.

1. Ověřte, že má kontejner svazků, které chcete převzít služby při selhání přidružené cloudové snímky. Další informace najdete v části [služby Správce zařízení StorSimple používá k vytvoření zálohy](storsimple-8000-manage-backup-policies-u2.md).
2. Přejděte do Správce zařízení StorSimple a klikněte na **zařízení**. V **zařízení** okno, přejděte k seznamu zařízení připojených k vaší službě.
    ![Vyberte zařízení.](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Vyberte a klikněte na zdrojové zařízení. Zdrojové zařízení má kontejnery svazků, které chcete převzít služby při selhání. Přejděte na **Nastavení > kontejnery svazků**.
4. Vyberte kontejner svazků, která chcete převzít služby při selhání na jiné zařízení. Klikněte na kontejner svazků pro zobrazení seznamu svazků v tomto kontejneru. Vyberte svazek, klikněte pravým tlačítkem a klikněte na **přepnout do režimu Offline** uvedení svazku do režimu offline. Tento postup opakujte pro všechny svazky v kontejneru svazků.
5. Předchozí krok opakujte pro všechny kontejnery svazků, které chcete převzít služby při selhání na jiné zařízení.
6. Přejděte zpět **zařízení** okno. Na panelu příkazů klikněte na tlačítko **převzetí služeb při selhání**.
    ![Klikněte na selhání přes](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. V **převzetí služeb při selhání** okno, proveďte následující kroky:
   
   1. Klikněte na tlačítko **zdroj**. Kontejnery svazků se svazky přidružené k cloudové snímky jsou zobrazeny. Pouze kontejnery, které jsou zobrazeny jsou způsobilé pro převzetí služeb při selhání. V seznamu kontejnerů svazků vyberte kontejnery svazků, které chcete převzít služby při selhání. **Zobrazí se pouze kontejnery svazků s související cloudové snímky a offline svazky.**

       ![Výběr zdroje](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Klikněte na tlačítko **cílové**. Pro kontejnery svazků vybraných v předchozím kroku vyberte z rozevíracího seznamu dostupných zařízení cílové zařízení. V seznamu se zobrazují jenom zařízení, které mají dostatečnou kapacitu tak, aby vyhovovaly zdroj kontejnery svazků.

        ![Výběr cíle](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Nakonec zkontrolujte všechna nastavení převzetí služeb při selhání v rámci **Souhrn**. Po zkontrolování nastavení, zaškrtněte políčko označující, že svazky ve vybrané kontejnery svazků jsou offline. Klikněte na **OK**.

       ![Zkontrolujte nastavení převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple vytvoří úlohu převzetí služeb při selhání. Kliknutím na úlohu oznámení monitorování úlohy převzetí služeb při selhání prostřednictvím **úlohy** okno.

    Pokud kontejner svazků, které převzetí služeb při selhání má místní svazky, uvidíte jednotlivých úloh obnovení pro všechny místní svazky (ne pro vrstvené svazky) v kontejneru. Tato obnovení úlohy může trvat poměrně dlouho pro dokončení. Je pravděpodobné, že úloha převzetí služeb při selhání může být dokončena dříve. Tyto svazky budou mít místní záruky pouze po dokončení úlohy obnovení.

    ![Monitorování úlohy převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Po dokončení převzetí služeb, přejděte **zařízení** okno.
   
   1. Vyberte zařízení, která byla použita jako cílové zařízení pro proces převzetí služeb při selhání.

       ![Vyberte zařízení.](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Přejděte **kontejnery svazků** okno. Všechny kontejnery svazků, spolu s svazků ze staré zařízení by měla být uvedená.

       ![Kontejnery svazků cílové zobrazení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Další postup

* Po provedení převzetí služeb při selhání, budete muset [Deaktivování nebo odstranění zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Informace o tom, jak použít službu StorSimple Device Manager přejděte na [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

