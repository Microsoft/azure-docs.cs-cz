---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005808"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Instalace aktualizací prostřednictvím webu Azure Portal

1. Přejděte do Správce zařízení StorSimple a vyberte **Zařízení**. Ze seznamu zařízení připojených k vaší službě vyberte a klikněte na zařízení, které chcete aktualizovat.

    ![V nedávné době se Device Manager MySSDevManager zvýrazní a vybere, zařízení se zvýrazní a vybere a zařízení MYSSIS1103 se zvýrazní a vybere.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. V okně **Nastavení** klikněte na **Aktualizace zařízení**.

    ![Zobrazí se informace pro MYSSIS1103. V nastavení se zvýrazní aktualizace zařízení.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Pokud jsou k dispozici aktualizace softwaru, zobrazí se zpráva. Aktualizace můžete zkontrolovat také kliknutím na **Vyhledat**. Poznamenejte si verzi softwaru, kterou používáte. 

    ![V podokně aktualizace zařízení se zobrazí zpráva "nové aktualizace jsou k dispozici" a "poslední prohledaná/11/01/2017 10:56 AM/software verze/10.0.10289.0". Verze je zvýrazněna.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Po zahájení a úspěšném dokončení kontroly se zobrazí oznámení.

    ![Oznámení říká "vyhledávání aktualizací pro MySVAFS110 zařízení.... 10:57 dopoledne/operace právě probíhá. "](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Jakmile budou aktualizace prohledané, klikněte na **Stáhnout aktualizace**.

    ![V podokně aktualizace zařízení se zobrazí zpráva "nové aktualizace jsou k dispozici". Je zvýrazněno tlačítko stáhnout aktualizace.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. V okně **nové aktualizace** si přečtěte poznámky k verzi. Všimněte si také, že po stažení aktualizací je nutné potvrdit instalaci. Klikněte na **OK**.

    ![Nové podokno aktualizace uvádí, že se po stažení aktualizací bude nutné potvrdit instalaci. Je zvýrazněno tlačítko OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. Po spuštění a úspěšném dokončení nahrávání se zobrazí zpráva.

     ![Oznámení říká "stažení aktualizací pro MySVAFS zařízení... 11:07 DOP.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. V okně **Aktualizace zařízení** klikněte na **Nainstalovat**.

     ![Aktualizace zařízení říká "Potvrdit instalaci aktualizací". Tlačítko nainstalovat je zvýrazněné.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. V okně **Nové aktualizace** se zobrazí upozornění na narušení běhu zařízení touto aktualizací. Protože je virtuální pole zařízením s jedním uzlem, zařízení se po aktualizaci restartuje. To naruší běh všech probíhajících V/V. Kliknutím na **OK** nainstalujte aktualizace.

    ![Zpráva v novém podokně aktualizace je "při instalaci těchto aktualizací zařízení bude mít výpadek." Je zvýrazněno tlačítko OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Po spuštění úlohy instalace se zobrazí zpráva.

    ![Oznámení říká "instalace aktualizací pro MySVAFS110 zařízení.... 11:09 DOP.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Po úspěšném dokončení úlohy instalace můžete kliknutím na odkaz **Zobrazit úlohu** v okně **Aktualizace zařízení** monitorovat instalaci. 

    ![V podokně aktualizace zařízení se zobrazí odkaz s názvem instalace aktualizací. Zobrazit úlohu. Tlačítko nainstalovat je zvýrazněné.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Tato akce přejde do okna **instalovat aktualizace** . Tady si můžete prohlédnout podrobné informace o úloze.

    ![Podokno instalovat aktualizace obsahuje informace o instalaci, včetně zařízení, stavu, doby trvání, času spuštění a času zastavení.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Pokud jste začali s virtuálním polem, na kterém běží aktualizace softwaru 0,6 (10.0.10293.0), teď používáte aktualizaci 1 a máte hotovou verzi. Zbývající kroky můžete přeskočit. Pokud jste začali s virtuálním polem, na kterém je spuštěná verze softwaru před aktualizací 0,6 (10.0.10293.0), je teď aktualizace 0,6. Zobrazí se další zpráva oznamující, že jsou k dispozici aktualizace. Opakujte kroky 4-8 pro instalaci aktualizace Update 1.

    ![V podokně aktualizace zařízení se zobrazí zpráva "nové aktualizace jsou k dispozici" a "poslední prohledaná/11/1/2017 10:56 AM/software verze/10.0.10293.0".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

