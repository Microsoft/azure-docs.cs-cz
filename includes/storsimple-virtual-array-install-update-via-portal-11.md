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
ms.openlocfilehash: 65d5a88f7b5d059deb633f062639e455c64ef2f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175531"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Instalace aktualizací prostřednictvím webu Azure Portal

1. Přejděte do Správce zařízení StorSimple a vyberte **Zařízení**. Ze seznamu zařízení připojených k vaší službě vyberte a klikněte na zařízení, které chcete aktualizovat.

2. V části **Nastavení**klepněte na **položku Aktualizace zařízení**.  

3. Pokud jsou k dispozici aktualizace softwaru, zobrazí se zpráva. Aktualizace můžete zkontrolovat také kliknutím na **Vyhledat**. Poznamenejte si verzi softwaru, kterou používáte. 

    ![aktualizace zařízení](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Budete upozorněni při úspěšném spuštění a dokončení prohledávky.
 
4. Jakmile budou aktualizace prohledané, klikněte na **Stáhnout aktualizace**. V části **Nové aktualizace**si přečtěte poznámky k verzi. Všimněte si také, že po stažení aktualizací je třeba instalaci potvrdit. Klikněte na tlačítko **OK**.

    ![aktualizace zařízení](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    Po spuštění a úspěšném dokončení nahrávání se zobrazí zpráva.

5. V části **Aktualizace zařízení**klepněte na tlačítko **Instalovat**.

     ![aktualizace zařízení](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. V části **Nové aktualizace**budete upozorněni, že aktualizace je rušivá. Protože je virtuální pole zařízením s jedním uzlem, zařízení se po aktualizaci restartuje. To naruší běh všech probíhajících V/V. Kliknutím na **OK** nainstalujte aktualizace.

    ![aktualizace zařízení](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Po spuštění úlohy instalace se zobrazí zpráva.

7.  Po úspěšném dokončení úlohy instalace klikněte na **odkaz Zobrazit úlohu.** Tato akce vás přenese do okna **Instalovat aktualizace.** Tady si můžete prohlédnout podrobné informace o úloze. 

    ![aktualizace zařízení](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Pokud jste začali s virtuálním polem se spuštěnou verzí softwaru Update 1 (10.0.10296.0), nyní používáte aktualizaci 1.1 a jste hotovi. Zbývající kroky můžete přeskočit. 

    Pokud jste začali s virtuálním polem s verzí softwaru Update 0.6 (10.0.10293.0), budete nyní aktualizováni na aktualizaci 1.0. Zobrazí se další zpráva oznamující, že aktualizace jsou k dispozici. Opakujte kroky 4-7 pro instalaci aktualizace 1.1.

    

