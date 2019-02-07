---
title: StorSimple převzetí služeb při selhání, zotavení po havárii pro zařízení 8000 series | Dokumentace Microsoftu
description: Zjistěte, jak převzít služby při selhání zařízení StorSimple do stejného zařízení.
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
ms.openlocfilehash: dd207eaad1a3e821724d51a890d0882bfffda131
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809057"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Převzetí služeb při selhání fyzického zařízení StorSimple do stejného zařízení

## <a name="overview"></a>Přehled

Tento kurz popisuje kroky potřebné k převzetí služeb při selhání fyzického zařízení StorSimple 8000 series na sebe sama, pokud nedojde k havárii. StorSimple využívá funkce převzetí služeb při selhání zařízení pro migraci dat ze zdrojové fyzické zařízení v datovém centru do jiného fyzického zařízení. Pokyny v tomto kurzu platí pro StorSimple řady 8000 fyzických zařízení, které používají software verze Update 3 a novější.

Další informace o převzetí služeb při selhání zařízení a jak se používá k zotavení po havárii, přejděte na [převzetí služeb při selhání a zotavení po havárii pro zařízení StorSimple 8000 series](storsimple-8000-device-failover-disaster-recovery.md).

Převzetí služeb při selhání fyzické zařízení na jiné fyzické zařízení, přejděte na [převzetí služeb při selhání do stejného fyzického zařízení StorSimple](storsimple-8000-device-failover-physical-device.md). Převzetí služeb při selhání fyzického zařízení StorSimple k řešení StorSimple Cloud Appliance, přejděte na [převzetí služeb při selhání do cloudového zařízení StorSimple](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste si prohlédli důležité informace týkající se převzetí služeb při selhání zařízení. Další informace najdete v části [častá rozhodnutí při převzetí služeb při selhání zařízení](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Postup převzetí služeb při selhání do stejného zařízení

Pokud je potřeba převzetí služeb při selhání do stejného zařízení, proveďte následující kroky.

1. Využijte cloudové snímky všech svazků v zařízení. Další informace najdete v části [služby Správce zařízení StorSimple používá k vytvoření zálohy](storsimple-8000-manage-backup-policies-u2.md).
2. Obnovte v zařízení výchozí tovární nastavení. Postupujte podle podrobných pokynů v [jak resetovat do výchozího továrního nastavení zařízení StorSimple](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Přejděte do služby Správce zařízení StorSimple a potom vyberte **zařízení**. V **zařízení** okně se starým zařízením by se zobrazit jako **Offline**.

    ![Zdrojové zařízení do offline režimu](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Konfigurace zařízení a zaregistrujte ho znovu pomocí služby Správce zařízení StorSimple. Nově registrovaná zařízení by se zobrazit jako **připraveno k nastavení**. Název zařízení pro nového zařízení je stejné jako staré zařízení ale připojeny číslo označuje, že se zařízení obnovíte výchozí tovární a zaregistrovat znovu.

    ![Připraveno k nastavení nově registrovaná zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Nové zařízení dokončete nastavování zařízení. Další informace najdete v části [krok 4: Dokončení minimální instalace zařízení](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Na **zařízení** okna, stav zařízení změní na **Online**.

   > [!IMPORTANT]
   > **Nejprve dokončete minimální požadavky na konfiguraci, nebo vaše zotavení po Havárii mohou selhat.**

    ![Online nově registrovaná zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Vyberte se starým zařízením (offline stav) a na panelu příkazů klikněte na tlačítko **převzetí služeb při selhání**. V **převzetí služeb při selhání** okně vyberte původní zařízení jako zdroj a zadat cílové zařízení jako na nově registrovaná zařízení.

    ![Souhrn převzetí služeb při selhání](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Podrobné pokyny najdete selhání přes do jiného fyzického zařízení.

7. Monitorování z se vytvoří úloha obnovení zařízení **úlohy** okno.

8. Po úspěšném dokončení úlohy, přístup k novým zařízením a přejděte **kontejnery svazků** okno. Ověřte, že všechny kontejnery svazků ze staré zařízení jste migrovali na nové zařízení.

   ![Kontejnery svazků migrovány](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Po dokončení převzetí služeb můžete deaktivovat a odstranit stará zařízení z portálu. Vyberte staré zařízení (offline), klikněte pravým tlačítkem a pak vyberte **deaktivovat**. Jakmile je zařízení deaktivováno, se aktualizuje stav zařízení.

     ![Zdrojové zařízení bylo deaktivováno](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Vyberte deaktivované zařízení, klikněte pravým tlačítkem a pak vyberte **odstranit**. Tím se odstraní ze seznamu zařízení zařízení.

    ![Zdrojové zařízení odstranit](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Další postup

* Po provedení převzetí služeb při selhání, budete muset [Deaktivování nebo odstranění zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Informace o tom, jak použít službu StorSimple Device Manager přejděte na [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

