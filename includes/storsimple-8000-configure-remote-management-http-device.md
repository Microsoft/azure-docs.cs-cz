---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736091"
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
    3. Na panelu příkazů v horní části okna klikněte na **... Další** a pak kliknutím na **Stáhnout certifikát** stáhněte certifikát pro vzdálenou správu. Můžete zadat umístění, do kterého chcete tento soubor uložit. Tento certifikát se musí nainstalovat na klientský nebo hostitelský počítač, pomocí kterého se budete připojovat ke cloudovému zařízení.

        ![Okno Vzdálená správa](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. Klikněte na **Uložit** a po zobrazení výzvy potvrďte změny.