---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95558966"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Instalace aktualizací prostřednictvím webu Azure Portal

1. Přejděte do Správce zařízení StorSimple a vyberte **Zařízení**. Ze seznamu zařízení připojených k vaší službě vyberte a klikněte na zařízení, které chcete aktualizovat. 

    ![V nedávné době se Device Manager MySSDevManager zvýrazní a vybere, zařízení se zvýrazní a vybere a zařízení MYSSIS1103 se zvýrazní a vybere.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. V okně **Nastavení** klikněte na **Aktualizace zařízení**. 

    ![Zobrazí se informace pro MYSSIS1103. V nastavení se zvýrazní aktualizace zařízení.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. Pokud jsou k dispozici aktualizace softwaru, zobrazí se zpráva. Aktualizace můžete zkontrolovat také kliknutím na **Vyhledat**.

    ![V podokně aktualizace zařízení se zvýrazní tlačítko vyhledat. Je k dispozici čtyři řádková zpráva: poslední naskenovaný/nenaskenovaný/10.0.10280.0/verze softwaru.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    Po spuštění a úspěšném dokončení vyhledávání se zobrazí zpráva.

    ![Oznámení říká "vyhledávání aktualizací pro zařízení MySSIS 1103.2:12 PM/úspěšně dokončil operaci".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. Jakmile budou aktualizace prohledané, klikněte na **Stáhnout aktualizace**. 

    ![V podokně aktualizace zařízení jsou k dispozici "nové aktualizace jsou k dispozici" a "Poslední kontrola/11/3/2016 2:12 PM/10.0.10280.0 ODP./verze softwaru/". Verze je zvýrazněna.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. V okně **Nové aktualizace** si prohlédněte informaci o nutnosti potvrdit instalaci po stažení aktualizací. Klikněte na **OK**.

    ![Nové podokno aktualizace uvádí, že se po stažení aktualizací bude nutné potvrdit instalaci. Je zvýrazněno tlačítko OK.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. Po spuštění a úspěšném dokončení nahrávání se zobrazí zpráva.

     ![Oznámení říká "stažení aktualizací pro MySSIS zařízení... 2:13 PM.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. V okně **Aktualizace zařízení** klikněte na **Nainstalovat**.

     ![V podokně aktualizace zařízení se zobrazí zpráva "Potvrdit instalaci aktualizací". Tlačítko nainstalovat je zvýrazněné.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. V okně **Nové aktualizace** se zobrazí upozornění na narušení běhu zařízení touto aktualizací. Protože je virtuální pole zařízením s jedním uzlem, zařízení se po aktualizaci restartuje. To naruší běh všech probíhajících V/V. Kliknutím na **OK** nainstalujte aktualizace. 

    ![Zpráva v novém podokně aktualizace je "při instalaci těchto aktualizací zařízení bude mít výpadek." Je zvýrazněno tlačítko OK.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. Po spuštění úlohy instalace se zobrazí zpráva. 

    ![Oznámení říká "instalace aktualizací pro zařízení MYSSIS1103". 2:15 PM.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  Po úspěšném dokončení úlohy instalace můžete kliknutím na odkaz **Zobrazit úlohu** v okně **Aktualizace zařízení** monitorovat instalaci. 

    ![V podokně aktualizace zařízení je odkaz označený jako "instalace aktualizací". Je zvýrazněna možnost Zobrazit úlohu.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    Tím přejdete do okna **Instalace aktualizací**. Tady si můžete prohlédnout podrobné informace o úloze.

    ![Podokno instalovat aktualizace obsahuje informace o instalaci, včetně zařízení, stavu, doby trvání, času spuštění a času zastavení.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. Po úspěšné instalaci aktualizací se o tom zobrazí zpráva v okně Aktualizace zařízení. Verze softwaru se také změní na **10.0.10288.0**. 

    ![V podokně aktualizace zařízení se zobrazí zpráva "vaše zařízení je aktuální a poskytuje verzi softwaru (10.0.10288.0).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
