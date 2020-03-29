---
title: Správa webového uživatelského uživatelského prostředí storSimple Virtual Array | Dokumenty společnosti Microsoft
description: Popisuje, jak provádět základní úlohy správy zařízení prostřednictvím webového uživatelského rozhraní StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 92671206a4171ca838423f55b526191ef30e5c35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254726"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Správa virtuálního pole StorSimple pomocí webového uživatelského prostředí
![tok procesu nastavení](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Přehled
Kurzy v tomto článku platí pro Virtuální pole Microsoft Azure StorSimple (označované také jako místní virtuální zařízení StorSimple) se spuštěním verze obecné dostupnosti (GA) z března 2016. Tento článek popisuje některé složité pracovní postupy a úlohy správy, které lze provádět na virtuálním poli StorSimple. Virtuální pole StorSimple můžete spravovat pomocí uživatelského rozhraní služby StorSimple Manager (označované jako uživatelské rozhraní portálu) a místního webového uživatelského rozhraní pro zařízení. Tento článek se zaměřuje na úkoly, které můžete provádět pomocí webového uživatelského uživatelského panelu.

Tento článek obsahuje následující návody:

* Získání šifrovacího klíče dat služby
* Poradce při potížích s chybami nastavení webového uživatelského portálu
* Generovat balíček protokolu
* Vypnutí nebo restartování zařízení

## <a name="get-the-service-data-encryption-key"></a>Získání šifrovacího klíče dat služby
Šifrovací klíč dat služby je generován při registraci prvního zařízení pomocí služby StorSimple Manager. Tento klíč je pak vyžadován s registračním klíčem služby pro registraci dalších zařízení se službou StorSimple Manager.

Pokud jste nesprávně umístili šifrovací klíč dat služby a potřebujete jej načíst, proveďte následující kroky v místním webovém uživatelském rozhraní zařízení registrovaného ve vaší službě.

#### <a name="to-get-the-service-data-encryption-key"></a>Získání šifrovacího klíče dat služby
1. Připojte se k místnímu webovému uživatelskému uživatelskému prostředí. Přejděte na **Nastavení konfiguračního** > **cloudu**.
2. V dolní části stránky klikněte na **Získat šifrovací klíč dat služby**. Zobrazí se klíč. Zkopírujte a uložte tento klíč.
   
    ![získání šifrovacího klíče dat služby 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Poradce při potížích s chybami nastavení webového uživatelského portálu
V některých případech při konfiguraci zařízení prostřednictvím místního webového uživatelského rozhraní může dojít k chybám. Chcete-li diagnostikovat a řešit tyto chyby, můžete spustit diagnostické testy.

#### <a name="to-run-the-diagnostic-tests"></a>Spuštění diagnostických testů
1. V místním webovém uživatelském rozhraní přejděte na **řešení potíží s diagnostickými** > **testy**.
   
    ![spustit diagnostiku 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. V dolní části stránky klepněte na tlačítko **Spustit diagnostické testy**. Tím zahájíte testy pro diagnostiku možných problémů s nastavením sítě, zařízení, webového proxy serveru, času nebo cloudu. Budete upozorněni, že zařízení spouštějí testy.
3. Po dokončení testů se zobrazí výsledky. Následující příklad ukazuje výsledky diagnostických testů. Všimněte si, že nastavení webového proxy serveru nebyly nakonfigurovány v tomto zařízení, a proto nebyl spuštěn test webového proxy serveru. Všechny ostatní testy nastavení sítě, serveru DNS a nastavení času byly úspěšné.
   
    ![spustit diagnostiku 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generovat balíček protokolu
Balíček protokolu se skládá ze všech příslušných protokolů, které mohou pomoci podpoře společnosti Microsoft při řešení problémů se zařízením. V této verzi lze vygenerovat balíček protokolu prostřednictvím místního webového uživatelského prostředí.

#### <a name="to-generate-the-log-package"></a>Generování balíčku protokolu
1. V místním webovém uživatelském prostředí přejděte na**protokoly** **systému řešení** > potíží .
   
    ![generovat balíček protokolu 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. V dolní části stránky klikněte na **Vytvořit balíček protokolu**. Bude vytvořen balíček systémových protokolů. Bude to trvat pár minut.
   
    ![generovat balíček protokolu 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Budete upozorněni po úspěšném vytvoření balíčku a stránka bude aktualizována tak, aby označovala čas a datum, kdy byl balíček vytvořen.
   
    ![generovat balíček protokolu 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Klepněte na tlačítko **Stáhnout balíček protokolu**. Zip balíček bude stažen do vašeho systému.
   
    ![generovat balíček protokolu 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Můžete rozbalit stažený balíček protokolu a zobrazit soubory protokolu systému.

## <a name="shut-down-and-restart-your-device"></a>Vypnutí a restartování zařízení
Virtuální zařízení můžete vypnout nebo restartovat pomocí místního webového uživatelského rozhraní. Doporučujeme, abyste před restartováním přepáčili svazky nebo sdílené složky do offline na hostiteli a potom na zařízení. Tím se minimalizuje možnost poškození dat. 

#### <a name="to-shut-down-your-virtual-device"></a>Vypnutí virtuálního zařízení
1. V místním webovém uživatelském prostředí přejděte na nastavení **údržby** > **.**
2. V dolní části stránky klepněte na **tlačítko Vypnout**.
   
    ![vypnutí zařízení 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zobrazí se upozornění, že vypnutí zařízení přeruší všechny vi, které byly v průběhu, což má za následek prostoje. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![upozornění na vypnutí zařízení](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Budete upozorněni, že vypnutí bylo zahájeno.
   
    ![vypnutí zařízení bylo spuštěno](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Zařízení se nyní vypne. Pokud chcete zařízení spustit, budete to muset udělat prostřednictvím Správce technologie Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Restartování virtuálního zařízení
1. V místním webovém uživatelském prostředí přejděte na nastavení **údržby** > **.**
2. V dolní části stránky klepněte na **tlačítko Restartovat**.
   
    ![restart zařízení](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zobrazí se upozornění, že restartování zařízení přeruší všechny probíhající vi, což vede k prostojům. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![upozornění na restartování](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Budete upozorněni, že restartování bylo zahájeno.
   
    ![restartovat počítač zahájen](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Během restartování dojde ke ztrátě připojení k umělá a umělá a ustálená. Restartování můžete sledovat pravidelným obnovením uhlavního počítače. Případně můžete sledovat stav restartování zařízení prostřednictvím Správce technologie Hyper-V.

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [používat službu StorSimple Manager ke správě zařízení](storsimple-virtual-array-manager-service-administration.md).

