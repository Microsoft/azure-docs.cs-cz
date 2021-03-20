---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "67175068"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>Konfigurace vzdálené správy na cloudovém zařízení

1. Ve službě Správce zařízení StorSimple klikněte na **Zařízení**. V seznamu zařízení připojených ke službě vyberte a klikněte na vaše cloudové zařízení.
    ![Výběr cloudového zařízení StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. Přejděte do **Nastavení > Zabezpečení** a otevře se okno **Nastavení zabezpečení**.

     ![Nastavení zabezpečení StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. Přejděte do části **Vzdálená správa**. Klikněte na pole **Vzdálená správa**.
     ![Vzdálená správa StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. V okně **Vzdálená správa**:

    1. Ověřte, že je zapnutá možnost **Povolit vzdálenou správu**.
    2. Výchozí možností je připojení pomocí protokolu HTTPS. Můžete zvolit připojení pomocí protokolu HTTP. Připojení pomocí protokolu HTTP je přijatelné jenom v důvěryhodných sítích. Ujistěte se, že je povolený protokol HTTP.
    3. Na panelu příkazů v horní části okna klikněte na **... Další** a potom kliknutím na **Stáhnout certifikát** Stáhněte certifikát pro vzdálenou správu. Můžete zadat umístění, do kterého chcete tento soubor uložit. Tento certifikát se musí nainstalovat na klientský nebo hostitelský počítač, pomocí kterého se budete připojovat ke cloudovému zařízení.

        ![Okno pro vzdálenou správu](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. Klikněte na **Uložit** a po zobrazení výzvy potvrďte změny.