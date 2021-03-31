---
title: Správa webového uživatelského rozhraní pro virtuální pole StorSimple | Microsoft Docs
description: Popisuje, jak provádět základní úlohy správy zařízení prostřednictvím webového uživatelského rozhraní StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 6bea9c9b7807ffb32b6071c968d3186965477009
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000925"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Správa virtuálního pole StorSimple pomocí webového uživatelského rozhraní
![průběh procesu nastavení](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Přehled
Kurzy v tomto článku se vztahují na Microsoft Azure StorSimple virtuální pole (označované také jako místní virtuální zařízení StorSimple) s vydáním obecné dostupnosti (GA) od března 2016. Tento článek popisuje některé komplexní pracovní postupy a úlohy správy, které lze provést ve virtuálním poli StorSimple. Virtuální pole StorSimple můžete spravovat pomocí uživatelského rozhraní služby StorSimple Manager (označované jako uživatelské rozhraní portálu) a místního webového uživatelského rozhraní pro dané zařízení. Tento článek se zaměřuje na úlohy, které můžete provádět pomocí webového uživatelského rozhraní.

Tento článek obsahuje následující návody:

* Získat šifrovací klíč dat služby
* Řešení chyb při instalaci webového uživatelského rozhraní
* Generování balíčku protokolu
* Vypnutí nebo restartování zařízení

## <a name="get-the-service-data-encryption-key"></a>Získat šifrovací klíč dat služby
Šifrovací klíč dat služby se vygeneruje při registraci prvního zařízení ve službě StorSimple Manager. Tento klíč se pak vyžaduje s registračním klíčem služby k registraci dalších zařízení ve službě StorSimple Manager.

Pokud jste svůj šifrovací klíč dat služby nastavili a potřebujete ho načíst, proveďte následující kroky v místním webovém uživatelském rozhraní zařízení zaregistrovaného ve vaší službě.

#### <a name="to-get-the-service-data-encryption-key"></a>Získání šifrovacího klíče dat služby
1. Připojte se k místnímu webovému uživatelskému rozhraní. Přejít na   >  **Nastavení cloudu** konfigurace.
2. V dolní části stránky klikněte na **získat šifrovací klíč dat služby**. Zobrazí se klíč. Zkopírujte a uložte tento klíč.
   
    ![získat šifrovací klíč dat služby 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Řešení chyb při instalaci webového uživatelského rozhraní
V některých případech, kdy nakonfigurujete zařízení prostřednictvím místního webového uživatelského rozhraní, může dojít k chybám. Chcete-li diagnostikovat a řešit tyto chyby, můžete spustit diagnostické testy.

#### <a name="to-run-the-diagnostic-tests"></a>Spuštění diagnostických testů
1. V místním webovém uživatelském rozhraní si přečtěte **Poradce při potížích s**  >  **diagnostickými testy**.
   
    ![spustit diagnostiku 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. V dolní části stránky klikněte na **Spustit diagnostické testy**. Tím se zahájí testy pro diagnostiku všech možných problémů se sítí, zařízením, webovým proxy serverem, časem nebo nastavením cloudu. Budete upozorněni na to, že zařízení spouští testy.
3. Po dokončení testů se zobrazí výsledky. Následující příklad ukazuje výsledky diagnostických testů. Všimněte si, že nastavení webového proxy serveru není na tomto zařízení nakonfigurované, a proto se test webového proxy serveru nespustil. Všechny ostatní testy nastavení sítě, serveru DNS a času byly úspěšné.
   
    ![spustit diagnostiku 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generování balíčku protokolu
Balíček protokolu se skládá ze všech relevantních protokolů, které vám můžou pomoct podpora Microsoftu s řešením potíží s problémy v zařízeních. V této verzi se dá balíček protokolu vygenerovat prostřednictvím místního webového uživatelského rozhraní.

#### <a name="to-generate-the-log-package"></a>Vygenerování balíčku protokolu
1. V místním webovém uživatelském rozhraní si přečtěte **řešení potíží** se  >  **systémovými protokoly**.
   
    ![generovat protokolový balíček 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. V dolní části stránky klikněte na **vytvořit balíček protokolu**. Vytvoří se balíček systémových protokolů. Tato akce bude trvat několik minut.
   
    ![generovat protokolový balíček 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Po úspěšném vytvoření balíčku budete upozorněni a stránka bude aktualizována, aby označovala datum a čas vytvoření balíčku.
   
    ![generovat protokolový balíček 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Klikněte na **Stáhnout balíček protokolu**. V systému bude stažen balíček zip.
   
    ![generovat protokolový balíček 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Stažený balíček protokolu můžete rozbalit a zobrazit soubory systémových protokolů.

## <a name="shut-down-and-restart-your-device"></a>Vypnutí a restartování zařízení
Virtuální zařízení můžete vypnout nebo restartovat pomocí místního webového uživatelského rozhraní. Doporučujeme, abyste před restartováním převedli svazky nebo sdílené složky do režimu offline na hostiteli a pak na zařízení. Tím se minimalizuje jakákoli možnost poškození dat. 

#### <a name="to-shut-down-your-virtual-device"></a>Vypnutí virtuálního zařízení
1. V místním webovém uživatelském rozhraní přejdete na   >  **nastavení napájení** údržby.
2. V dolní části stránky klikněte na tlačítko **vypnout**.
   
    ![vypnutí zařízení 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zobrazí se upozornění s informacemi o tom, že vypnutí zařízení přeruší všechny vstupně-výstupní operace, což vede k výpadku. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Upozornění na vypnutí zařízení](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Budete upozorněni, že bylo iniciováno vypnutí.
   
    ![vypnutí zařízení bylo spuštěno.](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Zařízení se teď vypne. Pokud chcete zařízení spustit, budete ho muset udělat přes Správce technologie Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Restartování virtuálního zařízení
1. V místním webovém uživatelském rozhraní přejdete na   >  **nastavení napájení** údržby.
2. V dolní části stránky klikněte na **restartovat**.
   
    ![restartování zařízení](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zobrazí se upozornění oznamující, že restartování zařízení bude přerušit všechny probíhající IOs, což vedlo k výpadku. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Upozornění na restartování](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Zobrazí se oznámení o zahájení restartování.
   
    ![restart inicializován](./media/storsimple-ova-web-ui-admin/image39.png)
   
    V průběhu restartování dojde ke ztrátě připojení k uživatelskému rozhraní. Restartování můžete monitorovat pravidelným obnovením uživatelského rozhraní. Případně můžete monitorovat stav restartování zařízení pomocí Správce technologie Hyper-V.

## <a name="next-steps"></a>Další kroky
Naučte se [používat službu StorSimple Manager ke správě zařízení](storsimple-virtual-array-manager-service-administration.md).

