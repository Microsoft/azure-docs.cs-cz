---
title: StorSimple převzetí služeb při selhání, zotavení po havárii pro fyzické zařízení řady StorSimple 8000 | Microsoft Docs
description: Zjistěte, jak při selhání fyzického zařízení řady StorSimple 8000 do jiného fyzického zařízení.
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
ms.openlocfilehash: f3ac9545a341fc24ca12c9f2547805d6956cd98a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874861"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Převzetí služeb při selhání fyzického zařízení StorSimple 8000 řady

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky potřebné k převzetí služeb při selhání fyzického zařízení StorSimple 8000 řady do jiného fyzického zařízení StorSimple Pokud dojde k havárii. StorSimple používá funkci zařízení převzetí služeb při selhání k migraci dat ze zdrojového fyzického zařízení v datovém centru do jiného fyzického zařízení. Pokyny v tomto kurzu platí pro řady StorSimple 8000 fyzických zařízení spuštěná verze softwaru Update 3 nebo novější.

Další informace o převzetí služeb při selhání zařízení a jak se používají k zotavení po havárii, přejděte na [převzetí služeb při selhání a zotavení po havárii pro řadu zařízení StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Selhání fyzického zařízení StorSimple a o cloudu zařízení StorSimple, přejděte na [převzetí služeb při selhání o cloudu zařízení StorSimple](storsimple-8000-device-failover-cloud-appliance.md). Selhání fyzického zařízení sám na sebe, přejděte na [převzetí služeb při selhání do stejného fyzického zařízení StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste si přečetli důležité informace pro převzetí služeb při selhání zařízení. Další informace, přejděte na [častá rozhodnutí při převzetí služeb při selhání zařízení](storsimple-8000-device-failover-disaster-recovery.md).

- Musíte mít fyzického zařízení StorSimple 8000 řady nasazena v datovém centru. Zařízení musí používat verzi Update 3 nebo novější verze softwaru. Další informace, přejděte na [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Postup převzetí služeb při selhání fyzického zařízení

Proveďte následující kroky k obnovení vašeho zařízení na cílové fyzické zařízení.

1. Ověřte, že kontejner svazků, které chcete převzetí služeb při selhání je spojen cloudových snímků. Další informace, přejděte na [služby pomocí Správce zařízení StorSimple vytvářet zálohy](storsimple-8000-manage-backup-policies-u2.md).
2. Přejděte k vaší Správce zařízení StorSimple a pak klikněte na tlačítko **zařízení**. V **zařízení** okno, přejděte na seznam zařízení připojená k vaší službě.
    ![Vyberte zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Vyberte a klikněte na vaše zdrojové zařízení. Zdrojové zařízení má kontejnery svazků, které chcete převzetí služeb při selhání. Přejděte na **Nastavení > kontejnery svazků**.
4. Vyberte kontejner svazků, které byste chtěli převzetí služeb při selhání na jiné zařízení. Klikněte na tlačítko kontejneru svazků, které chcete zobrazit seznam svazků v tomto kontejneru. Vyberte svazek, klikněte pravým tlačítkem a klikněte na **přepnout do režimu Offline** uvedení svazku do režimu offline. Tento postup opakujte pro všechny svazky v kontejneru svazků.
5. Předchozí krok opakujte pro všechny kontejnery svazků, které byste chtěli převzetí služeb při selhání na jiné zařízení.
6. Přejděte zpět **zařízení** okno. Na panelu příkazů klikněte na **převzetí služeb při selhání**.
    ![Klikněte na tlačítko selhání přes](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. V **převzetí služeb při selhání** okno, proveďte následující kroky:
   
   1. Klikněte na tlačítko **zdroj**. Zobrazí se kontejnery svazků pro svazky, které jsou přidružené k cloudových snímků. Pouze kontejnery zobrazí jsou způsobilé pro převzetí služeb při selhání. V seznamu kontejnery svazků vyberte kontejnery svazků, které byste chtěli převzetí služeb při selhání. **Zobrazí se pouze kontejnery svazků přidruženého cloudu snímky a offline svazky.**

       ![Vyberte zdroj](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Klikněte na tlačítko **cíl**. Pro kontejnery svazků vybraných v předchozím kroku vyberte z rozevíracího seznamu k dispozici zařízení cílové zařízení. V seznamu se zobrazí jenom zařízení, která mají dostatečnou kapacitu, aby dokázala pojmout kontejnery svazků zdroje.

        ![Vyberte cíl](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Nakonec zkontrolujte všechna nastavení převzetí služeb při selhání v **Souhrn**. Po zkontrolování nastavení, zaškrtněte políčko označující, zda jsou svazky v kontejnerech vybraný svazek offline. Klikněte na **OK**.

       ![Zkontrolujte nastavení převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple vytvoří úlohu převzetí služeb při selhání. Kliknutím na úlohu oznámení monitorování úlohy převzetí služeb při selhání pomocí **úlohy** okno.

    Pokud kontejneru svazků, které při selhání má místní svazků, zobrazí jednotlivých úloh obnovení pro každý místní svazek (ne pro vrstvené svazky) v kontejneru. Tyto obnovení úloh může trvat dlouho. Je pravděpodobné, zda může dříve dokončí úlohu převzetí služeb při selhání. Tyto svazky budou mít místní záruky, až poté, co byly dokončeny úlohy obnovení.

    ![Úloha monitoru převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Po dokončení převzetí služeb při selhání, přejděte na **zařízení** okno.
   
   1. Vyberte zařízení, která byla použita jako cílové zařízení pro proces převzetí služeb při selhání.

       ![Vyberte zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Přejděte na **kontejnery svazků** okno. Všechny kontejnery svazků, společně s svazky z původního zařízení by měl být uvedený.

       ![Kontejnery svazků cílové zobrazení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Další kroky

* Po provedení převzetí služeb při selhání, budete muset [deaktivujte nebo odstraňte zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Informace o tom, jak používat službu StorSimple Manager zařízení, přejděte na [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

