---
title: StorSimple převzetí služeb při selhání, zotavení po havárii pro řady 8000 zařízení | Microsoft Docs
description: Naučte se převzít zařízení StorSimple na stejné zařízení.
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: acc8929dc3476e9590e8e4d9526b38b7c0719570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874819"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Selhání fyzického zařízení StorSimple ve stejném zařízení

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky potřebné k převzetí služeb při selhání fyzického zařízení StorSimple 8000 řady na sebe sama Pokud dojde k havárii. StorSimple používá funkci zařízení převzetí služeb při selhání k migraci dat ze zdrojového fyzického zařízení v datovém centru do jiného fyzického zařízení. Pokyny v tomto kurzu platí pro řady StorSimple 8000 fyzických zařízení spuštěná verze softwaru Update 3 nebo novější.

Další informace o převzetí služeb při selhání zařízení a jak se používají k zotavení po havárii, přejděte na [převzetí služeb při selhání a zotavení po havárii pro řadu zařízení StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Selhání fyzického zařízení k jiné fyzické zařízení, přejděte na [převzetí služeb při selhání do stejného fyzického zařízení StorSimple](storsimple-8000-device-failover-physical-device.md). Selhání fyzického zařízení StorSimple a o cloudu zařízení StorSimple, přejděte na [převzetí služeb při selhání o cloudu zařízení StorSimple](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste si přečetli důležité informace pro převzetí služeb při selhání zařízení. Další informace, přejděte na [častá rozhodnutí při převzetí služeb při selhání zařízení](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Postup převzetí služeb při selhání do stejného zařízení

Pokud potřebujete převzetí služeb při selhání do stejného zařízení, proveďte následující kroky.

1. Udělat snímky cloudu všechny svazky v zařízení. Další informace, přejděte na [služby pomocí Správce zařízení StorSimple vytvářet zálohy](storsimple-8000-manage-backup-policies-u2.md).
2. Obnovte v zařízení výchozí tovární nastavení. Podrobné pokyny v [jak resetovat výchozí tovární nastavení zařízení StorSimple](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Přejděte do služby StorSimple Manager zařízení a potom vyberte **zařízení**. V **zařízení** okně staré zařízení by měl zobrazit jako **Offline**.

    ![Zdrojového zařízení do offline režimu](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Konfigurace zařízení a zaregistrujte ho znovu pomocí služby StorSimple Manager zařízení. Nově registrovaná zařízení by měl zobrazit jako **připravení nastavit**. Název zařízení pro nové zařízení je stejný jako původní zařízení ale připojí číslem, chcete-li znamenat, že se zařízení resetovat na výchozí objekt pro vytváření a znovu zaregistrovat.

    ![Nově registrovaná zařízení jste připraveni k nastavení](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Pro nové zařízení dokončete instalaci zařízení. Další informace, přejděte na [krok 4: dokončení minimální instalace zařízení](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Na **zařízení** okně Stav zařízení změní na **Online**.

   > [!IMPORTANT]
   > **Dokončení minimální požadavky na konfiguraci nejprve nebo vaše zotavení po Havárii může selhat.**

    ![Online nově registrovaná zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Vyberte původní zařízení (offline stavu) a na panelu příkazů klikněte na **převzetí služeb při selhání**. V **převzetí služeb při selhání** okně vyberte původní zařízení jako zdroj a zadat cílové zařízení jako nově registrovaná zařízení.

    ![Souhrn převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Podrobné pokyny najdete v části [převzetí služeb při selhání do jiného fyzického zařízení](#fail-over-to-another-physical-device).

7. Úloha obnovení zařízení se vytvoří, můžete monitorovat z **úlohy** okno.

8. Po úspěšném dokončení úlohy, přístup k nové zařízení a přejděte do **kontejnery svazků** okno. Ověřte, že všechny kontejnery svazků ze staré zařízení byly migrovány do nového zařízení.

   ![Kontejnery svazků migrovat](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Po dokončení převzetí služeb při selhání můžete deaktivovat a odstranit stará zařízení z portálu. Vyberte v původním zařízení (offline), klikněte pravým tlačítkem a pak vyberte **deaktivovat**. Po deaktivaci zařízení se aktualizuje stav zařízení.

     ![Deaktivovat zdrojového zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Vyberte deaktivované zařízení, klikněte pravým tlačítkem a pak vyberte **odstranit**. Toto zařízení odstraní ze seznamu zařízení.

    ![Odstranit zdrojového zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Další kroky

* Po provedení převzetí služeb při selhání, budete muset [deaktivujte nebo odstraňte zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Informace o tom, jak používat službu StorSimple Manager zařízení, přejděte na [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

