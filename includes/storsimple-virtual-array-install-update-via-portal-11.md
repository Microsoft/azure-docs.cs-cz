---
title: zahrnout soubor
description: zahrnout soubor
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 8ef8b9d3350d0599ab00dfdbb018cf8dd900e316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561781"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Instalace aktualizací prostřednictvím webu Azure Portal

1. Přejděte do Správce zařízení StorSimple a vyberte **Zařízení**. Ze seznamu zařízení připojených k vaší službě vyberte a klikněte na zařízení, které chcete aktualizovat.

2. V části **Nastavení** klikněte na **aktualizace zařízení**.  

3. Pokud jsou k dispozici aktualizace softwaru, zobrazí se zpráva. Aktualizace můžete zkontrolovat také kliknutím na **Vyhledat**. Poznamenejte si verzi softwaru, kterou používáte. 

    ![V podokně aktualizace zařízení se zobrazí zpráva "nové aktualizace jsou k dispozici" (zvýrazněné) a "Poslední kontrola/6/22/2018 2:46 ODP./verze softwaru/10.0.10296.0". Verze je zvýrazněna.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Po zahájení a úspěšném dokončení kontroly se zobrazí oznámení.
 
4. Jakmile budou aktualizace prohledané, klikněte na **Stáhnout aktualizace**. V části **nové aktualizace** si přečtěte poznámky k verzi. Všimněte si také, že po stažení aktualizací je nutné potvrdit instalaci. Klikněte na **OK**.

    ![V části aktualizace zařízení se zvýrazní tlačítko stáhnout aktualizace. V nových aktualizacích je k dispozici odkaz na poznámky k verzi a zprávu k potvrzení instalace po stažení aktualizací. Existuje tlačítko OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    Po spuštění a úspěšném dokončení nahrávání se zobrazí zpráva.

5. V části **aktualizace zařízení** klikněte na **nainstalovat**.

     ![Aktualizace zařízení říká "Potvrdit instalaci aktualizací". Zvýrazní se tlačítko nainstalovat a verze softwaru.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. V části **nové aktualizace** se zobrazí upozornění na přerušení aktualizace. Protože je virtuální pole zařízením s jedním uzlem, zařízení se po aktualizaci restartuje. To naruší běh všech probíhajících V/V. Kliknutím na **OK** nainstalujte aktualizace.

    ![Nové aktualizace obsahují informace o tom, že vaše zařízení bude mít při instalaci těchto aktualizací výpadek. K dispozici je odkaz na poznámky k verzi a tlačítko OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Po spuštění úlohy instalace se zobrazí zpráva.

7.  Po úspěšném dokončení úlohy instalace klikněte na **Zobrazit** odkaz na úlohu. Tato akce přejde do okna **instalovat aktualizace** . Tady si můžete prohlédnout podrobné informace o úloze. 

    ![Aktualizace zařízení mají odkaz označený jako "instalace aktualizací. Zobrazit úlohu.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Pokud jste začali s virtuálním polem, na kterém běží verze Software Update 1 (10.0.10296.0), nyní používáte aktualizaci 1,1 a budete hotovi. Zbývající kroky můžete přeskočit. 

    Pokud jste začali s virtuálním polem, na kterém běží aktualizace softwaru 0,6 (10.0.10293.0), aktualizuje se teď aktualizace 1,0. Zobrazí se další zpráva oznamující, že jsou k dispozici aktualizace. Opakujte kroky 4-7 pro instalaci aktualizace 1,1.

    

