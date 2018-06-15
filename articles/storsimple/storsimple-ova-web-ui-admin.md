---
title: Pole virtuální zařízení StorSimple webového uživatelského rozhraní správy | Microsoft Docs
description: Popisuje, jak provádět úlohy správy základní zařízení StorSimple virtuální pole webového uživatelského rozhraní.
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
ms.openlocfilehash: 989e7b697f9b527df549fb32be18edd1d3c8d224
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875883"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Pomocí webového uživatelského rozhraní pro správu pole virtuální zařízení StorSimple
![tok procesu instalace](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Přehled
Kurzy v tomto článku se vztahují na Microsoft Azure StorSimple virtuální pole (také označované jako místní virtuální zařízení StorSimple) verzí obecné dostupnosti (GA). března 2016. Tento článek popisuje některé komplexní pracovní postupy a úlohy správy, které lze provést v poli virtuální zařízení StorSimple. Pole virtuální zařízení StorSimple pomocí StorSimple Manager můžete spravovat služby uživatelského rozhraní (označované jako portál uživatelského rozhraní) a místní webového uživatelského rozhraní pro zařízení. Tento článek se zaměřuje na úlohách, že můžete provádět pomocí webového uživatelského rozhraní.

Tento článek obsahuje následující kurzy:

* Získání šifrovacího klíče dat služby
* Řešení chyb instalace webového uživatelského rozhraní
* Generovat balíček protokolu
* Vypnutí nebo restartování zařízení

## <a name="get-the-service-data-encryption-key"></a>Získání šifrovacího klíče dat služby
Šifrovací klíč dat služby se vygeneruje, když zaregistrujete svoje první zařízení pomocí služby StorSimple Manager. Tento klíč je pak potřeba s registrační klíč služby k registraci dalších zařízení pomocí služby StorSimple Manager.

Pokud budete mít k jejich chybnému umístění šifrovacího klíče dat služby a nutnost jejich načtení, proveďte následující kroky v místní webového uživatelského rozhraní zařízení zaregistrovali služby.

#### <a name="to-get-the-service-data-encryption-key"></a>Chcete-li získat šifrovacího klíče dat služby
1. Připojte k místní webového uživatelského rozhraní. Přejděte na **konfigurace** > **nastavení cloudu**.
2. V dolní části stránky klikněte na tlačítko **šifrovacího klíče dat služby Get**. Zobrazí se klíč. Zkopírujte a uložte tento klíč.
   
    ![získání šifrovacího klíče dat služby 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Řešení chyb instalace webového uživatelského rozhraní
V některých případech při konfiguraci zařízení prostřednictvím místního webového uživatelského rozhraní, může dojít k chybám. Při diagnostice a řešení těchto chyb, můžete spustit testy diagnostiky.

#### <a name="to-run-the-diagnostic-tests"></a>Spustit diagnostické testy
1. V místní webového uživatelského rozhraní, přejděte do **Poradce při potížích s** > **diagnostických testů**.
   
    ![spustit diagnostiku 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. V dolní části stránky klikněte na tlačítko **spuštění diagnostických testů**. Tím zahájíte testy při diagnostice všech možných problémů s vaší sítě, zařízení, webový proxy server, čas nebo nastavení cloudu. Budete informováni, že je na zařízení spuštěný testy.
3. Po dokončení testů, se zobrazí výsledky. Následující příklad ukazuje výsledky diagnostických testů. Upozorňujeme, že nastavení webového proxy serveru nebyly nakonfigurovány na tomto zařízení a proto nebyl spuštěn test webového proxy serveru. Všechny ostatní testy pro nastavení sítě, DNS server a nastavení času byly úspěšné.
   
    ![spustit diagnostiku 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generovat balíček protokolu
Balíček protokol se skládá z příslušné protokoly, které vám mohou pomoci Microsoft Support s potíží jakékoli zařízení. V této verzi můžete generovat balíček protokolu prostřednictvím místního webového uživatelského rozhraní.

#### <a name="to-generate-the-log-package"></a>Pro vygenerování balíčku protokolu
1. V místní webového uživatelského rozhraní, přejděte do **Poradce při potížích s** > **protokoly systému**.
   
    ![Generovat balíček protokolu 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. V dolní části stránky klikněte na tlačítko **vytvořit balíček protokolu**. Bude vytvořen balíček protokolů systému. Bude to trvat několik minut.
   
    ![Generovat balíček protokolu 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Po úspěšném vytvoření balíčku se a stránky se aktualizují na označuje čas a datum vytvoření balíčku, budete upozorněni.
   
    ![Generovat balíček protokolu 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Klikněte na tlačítko **balíček ke stažení protokolů**. Komprimované balíčku budou staženy ve vašem systému.
   
    ![Generovat balíček protokolu 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Můžete rozbalte balíček stažené protokolu a zobrazit soubory protokolů systému.

## <a name="shut-down-and-restart-your-device"></a>Vypnutí a restartování zařízení
Můžete vypnout nebo restartovat virtuální zařízení pomocí místní webového uživatelského rozhraní. Jsme doporučujeme před restartováním, trvat svazky nebo sdílené složky do offline režimu na hostiteli a poté zařízení. Tím se minimalizují možnost poškození dat. 

#### <a name="to-shut-down-your-virtual-device"></a>Vypnutí virtuálního zařízení
1. V místní webového uživatelského rozhraní, přejděte do **údržby** > **nastavení napájení**.
2. V dolní části stránky klikněte na tlačítko **vypnutí**.
   
    ![vypnutí zařízení 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zobrazí se upozornění, s informacemi o tom, že vypnutí zařízení přeruší všechny vstupně-výstupní operace které byly v průběhu, což vede výpadku. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![upozornění vypnutí zařízení](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Budete informováni, že byla inicializována ukončení.
   
    ![vypnutí zařízení spuštění](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Zařízení bude nyní ukončena. Pokud chcete spustit zařízení, musíte to provést pomocí Správce technologie Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Restartování virtuálního zařízení
1. V místní webového uživatelského rozhraní, přejděte do **údržby** > **nastavení napájení**.
2. V dolní části stránky klikněte na tlačítko **restartujte**.
   
    ![restartování zařízení](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zobrazí se upozornění, s informacemi o tom, že restartování zařízení přeruší všechny IOs, které byly v průběhu, což vede výpadku. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![upozornění na restartování](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Budete informováni, zda bylo zahájeno restartování.
   
    ![restartování, inicializovat](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Když probíhá restartování, dojde ke ztrátě připojení k rozhraní. Pravidelně aktualizujte uživatelské rozhraní můžete sledovat na restartování. Alternativně můžete sledovat stav restartování zařízení pomocí Správce technologie Hyper-V.

## <a name="next-steps"></a>Další kroky
Zjistěte, jak [používat službu StorSimple Manager ke správě vašich zařízení](storsimple-virtual-array-manager-service-administration.md).

