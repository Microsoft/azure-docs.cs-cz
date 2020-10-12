---
title: Kurz – převzetí služeb při selhání fyzického zařízení StorSimple na stejné zařízení
description: Seznamte se s kroky potřebnými k převzetí služeb při selhání fyzického zařízení řady StorSimple 8000 v případě havárie.
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: 17c116194aa52a82246bcee9114824e8a918ebbd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184325"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Převzetí služeb při selhání fyzického zařízení StorSimple na stejné zařízení

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky nutné k převzetí služeb při selhání fyzického zařízení řady StorSimple 8000 v případě havárie. StorSimple využívá funkci převzetí služeb při selhání zařízení k migraci dat ze zdrojového fyzického zařízení v datacentru do jiného fyzického zařízení. Pokyny v tomto kurzu se vztahují na fyzická zařízení řady StorSimple 8000, na kterých běží verze softwaru Update 3 a novější.

Další informace o převzetí služeb při selhání zařízení a způsobu jeho použití k zotavení po havárii najdete v tématu [převzetí služeb při selhání a zotavení po havárii pro zařízení s StorSimple 8000 series](storsimple-8000-device-failover-disaster-recovery.md).

Pokud chcete převzít služby při selhání fyzického zařízení na jiné fyzické zařízení, přečtěte si [převzetí služeb při selhání na stejné StorSimple fyzické zařízení](storsimple-8000-device-failover-physical-device.md). Pokud chcete převzít služby při selhání fyzického zařízení StorSimple na StorSimple Cloud Appliance, přečtěte si [převzetí služeb při selhání do StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste si prošli požadavky na převzetí služeb při selhání zařízení. Další informace najdete v [častých otázkách pro převzetí služeb zařízení při selhání](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Postup převzetí služeb při selhání u stejného zařízení

Pokud potřebujete převzít služby při selhání do stejného zařízení, proveďte následující kroky.

1. Využijte cloudové snímky všech svazků v zařízení. Další informace najdete v pro [vytváření záloh pomocí služby StorSimple Device Manager](storsimple-8000-manage-backup-policies-u2.md).
2. Resetujte zařízení do výchozího továrního nastavení. Postupujte podle podrobných pokynů v tématu [Postup resetování zařízení StorSimple do výchozího továrního nastavení](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Ve službě StorSimple Device Manager a pak vyberte **zařízení**. V okně **zařízení** by se původní zařízení mělo zobrazit jako **offline**.

    ![Zdrojové zařízení offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Nakonfigurujte zařízení a znovu ho zaregistrujte ve službě StorSimple Device Manager. Nově registrované zařízení by se mělo zobrazit jako **připravené k nastavení**. Název zařízení pro nové zařízení je stejný jako původní zařízení, ale připojené s číslicí, které indikuje, že se zařízení obnovilo do továrního nastavení a zaregistruje se znovu.

    ![Nově zaregistrované zařízení je připravené k nastavení.](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Pro nové zařízení dokončete instalaci zařízení. Další informace najdete v [kroku 4: dokončení minimální instalace zařízení](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). V okně **zařízení** se stav zařízení změní na **online**.

   > [!IMPORTANT]
   > **Nejdříve proveďte nejprve minimální konfiguraci, jinak může dojít k selhání programu DR.**

    ![Nově registrované zařízení online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Vyberte staré zařízení (stav offline) a na panelu příkazů klikněte na převzít služby **při selhání**. V okně **převzetí služeb při selhání** vyberte jako zdroj původní zařízení a jako nově zaregistrované zařízení zadejte cílové zařízení.

    ![Souhrn převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Podrobné pokyny najdete v tématu převzetí služeb při selhání na jiném fyzickém zařízení.

7. Vytvoří se úloha obnovení zařízení, kterou můžete sledovat v okně **úlohy** .

8. Po úspěšném dokončení úlohy přejděte k novému zařízení a přejděte do okna **kontejnery svazků** . Ověřte, jestli se všechny kontejnery svazků ze starého zařízení migrovali do nového zařízení.

   ![Kontejnery svazků migrovány](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Po dokončení převzetí služeb při selhání můžete původní zařízení deaktivovat a odstranit z portálu. Vyberte staré zařízení (offline), klikněte pravým tlačítkem myši a pak vyberte **deaktivovat**. Po deaktivaci zařízení se stav zařízení aktualizuje.

     ![Zdrojové zařízení bylo deaktivováno.](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Vyberte deaktivované zařízení, klikněte pravým tlačítkem a pak vyberte **Odstranit**. Tím se zařízení odstraní ze seznamu zařízení.

    ![Zdrojové zařízení se odstranilo.](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Další kroky

* Po provedení převzetí služeb při selhání možná budete muset [zařízení StorSimple deaktivovat nebo odstranit](storsimple-8000-deactivate-and-delete-device.md).
* Informace o tom, jak používat službu StorSimple Device Manager, najdete v článku [použití služby StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

