---
title: Převzetí služeb při selhání a zotavení po havárii na stejné zařízení StorSimple 8000
description: Přečtěte si, jak přepojit zařízení StorSimple na stejné zařízení.
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
ms.openlocfilehash: c8fe2d7ec7649f47f6cb9c8ae2c83f19c15691b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471801"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Převzetí služeb při selhání fyzického zařízení StorSimple na stejné zařízení

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky potřebné k převzetí služeb při selhání zařízení řady StorSimple 8000 pro sebe, pokud dojde k havárii. StorSimple používá funkci převzetí služeb při selhání zařízení k migraci dat ze zdrojového fyzického zařízení v datovém centru do jiného fyzického zařízení. Pokyny v tomto kurzu platí pro StorSimple 8000 série fyzických zařízení se systémem verze softwaru Update 3 a novější.

Další informace o převzetí služeb při selhání zařízení a způsobu jeho zotavení z havárie naleznete v části [Převzetí služeb při selhání a zotavení po havárii pro zařízení řady StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Chcete-li převzetí služeb při selhání fyzickézařízení do jiného fyzického zařízení, přejděte k [převzetí služeb při selhání na stejné fyzické zařízení StorSimple](storsimple-8000-device-failover-physical-device.md). Chcete-li převzetí služeb při selhání zařízení StorSimple na StorSimple cloud appliance, přejděte k [převzetí služeb při selhání na StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste zkontrolovali důležité informace o převzetí služeb při selhání zařízení. Další informace naleznete [v části Běžné důležité informace o převzetí služeb při selhání zařízení](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Kroky k převzetí služeb při selhání na stejné zařízení

Pokud potřebujete přepojit na stejné zařízení, proveďte následující kroky.

1. Pořizujte snímky všech svazků v zařízení v cloudu. Další informace naleznete v části [Vytvoření záloh pomocí služby StorSimple Device Manager](storsimple-8000-manage-backup-policies-u2.md).
2. Obnovte zařízení do továrního nastavení. Postupujte podle podrobných pokynů v [tom, jak obnovit výchozí nastavení zařízení StorSimple do továrního nastavení](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Přejděte na službu Správce zařízení StorSimple a vyberte **položku Zařízení**. V okně **Devices** by se staré zařízení mělo zobrazit jako **offline**.

    ![Zdrojové zařízení offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Nakonfigurujte zařízení a zaregistrujte jej znovu pomocí služby StorSimple Device Manager. Nově registrované zařízení by se mělo zobrazit jako **připravené k nastavení**. Název zařízení pro nové zařízení je stejný jako staré zařízení, ale je připojen s číslicí označující, že zařízení bylo resetováno do továrního nastavení a znovu registrováno.

    ![Nově registrované zařízení připravené k nastavení](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. U nového zařízení dokončete nastavení zařízení. Další informace naleznete v [kroku 4: Dokončete minimální nastavení zařízení](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). V okně **Zařízení** se stav zařízení změní na **Online**.

   > [!IMPORTANT]
   > **Nejprve dokončete minimální konfiguraci, jinak může vaše zotavení po havárii selhat.**

    ![Nově registrované zařízení online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Vyberte staré zařízení (stav offline) a na panelu příkazů klikněte na **Převzetí služeb při selhání**. V okně **Převzetí služeb při selhání** vyberte jako zdroj staré zařízení a zadejte cílové zařízení jako nově registrované zařízení.

    ![Souhrn převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Podrobné pokyny naleznete v části Převzetí služeb při selhání do jiného fyzického zařízení.

7. Je vytvořena úloha obnovení zařízení, kterou můžete sledovat z okna **Úlohy.**

8. Po úspěšném dokončení úlohy přejděte k novému zařízení a přejděte do okna **kontejnerů svazků.** Ověřte, zda všechny kontejnery svazků ze starého zařízení byly migrovány do nového zařízení.

   ![Přenesené kontejnery svazků](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Po dokončení převzetí služeb při selhání můžete deaktivovat a odstranit staré zařízení z portálu. Vyberte staré zařízení (offline), klikněte pravým tlačítkem myši a pak vyberte **Deaktivovat**. Po deaktivaci zařízení se aktualizuje stav zařízení.

     ![Zdrojové zařízení deaktivováno](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Vyberte deaktivované zařízení, klikněte pravým tlačítkem myši a pak vyberte **Odstranit**. Tím odstraníte zařízení ze seznamu zařízení.

    ![Zdrojové zařízení bylo odstraněno.](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Další kroky

* Po provedení převzetí služeb při selhání bude pravděpodobně nutné [deaktivovat nebo odstranit zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Informace o použití služby StorSimple Device Manager naleznete v [části Správa zařízení StorSimple pomocí služby StorSimple Device Manager](storsimple-8000-manager-service-administration.md).

