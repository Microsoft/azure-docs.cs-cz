---
title: StorSimple Virtual Array webového uživatelského rozhraní pro správu | Dokumentace Microsoftu
description: Popisuje, jak provádět základní úlohy správy prostřednictvím webového uživatelského rozhraní StorSimple Virtual Array.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630358"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Správa StorSimple Virtual Array pomocí webového uživatelského rozhraní
![tok procesu instalace](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Přehled
Kurzy v tomto článku se vztahují na Microsoft Azure StorSimple Virtual Array (označované také jako místní virtuální zařízení StorSimple) spuštěná verze obecné dostupnosti (GA). března 2016. Tento článek popisuje některé komplexní pracovní postupy a úlohy správy, které lze provést u StorSimple Virtual Array. Můžete spravovat virtuální pole StorSimple ve StorSimple Manageru service (označované jako uživatelské rozhraní portálu) uživatelského rozhraní a místního webového uživatelského rozhraní pro zařízení. Tento článek se zaměřuje na úkoly můžete provést pomocí webového uživatelského rozhraní.

Tento článek obsahuje následující návody:

* Získání šifrovacího klíče dat služby
* Řešení potíží s chybami instalace webového uživatelského rozhraní
* Vygenerujte balíček pro protokol
* Vypnutí nebo restartování zařízení

## <a name="get-the-service-data-encryption-key"></a>Získání šifrovacího klíče dat služby
Šifrovací klíč dat služby se vygeneruje při registraci prvního zařízení ve službě StorSimple Manager. Tento klíč je pak nutné spolu s registračním klíčem služby k registraci dalších zařízení ve službě StorSimple Manager.

Pokud budete mít k jejich chybnému umístění šifrovací klíč dat služby a potřebujete ho, proveďte následující kroky v místním webovém uživatelském rozhraní zařízení registraci ve službě.

#### <a name="to-get-the-service-data-encryption-key"></a>Chcete-li získat šifrovací klíč dat služby
1. Připojte se k místního webového uživatelského rozhraní. Přejděte na **konfigurace** > **nastavení cloudu**.
2. V dolní části stránky klikněte na tlačítko **šifrovací klíč dat služby Get**. Zobrazí se nový klíč. Zkopírujte a uložte tento klíč.
   
    ![získání šifrovacího klíče dat služby 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Řešení potíží s chybami instalace webového uživatelského rozhraní
V některých případech při konfiguraci zařízení prostřednictvím místního webového uživatelského rozhraní, můžete narazit na chyby. Pro diagnostiku a řešení těchto chyb, můžete spustit diagnostické testy.

#### <a name="to-run-the-diagnostic-tests"></a>Spustit diagnostické testy
1. V místním webovém uživatelském rozhraní, přejděte na **Poradce při potížích s** > **diagnostických testů**.
   
    ![spustit diagnostiku 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. V dolní části stránky klikněte na tlačítko **spustit diagnostické testy**. Tím zahájíte testy k diagnostice všechny možné problémy s vaší sítě, zařízení, webový proxy server, čas nebo nastavení cloudu. Budete informováni, že zařízení je spuštění testů.
3. Po dokončení testů, zobrazí se výsledky. Následující příklad ukazuje výsledky diagnostické testy. Všimněte si, že nastavení webového proxy serveru nebyly nakonfigurovány na tomto zařízení, a proto se spustit webový test proxy. Všechny ostatní testy pro nastavení sítě, DNS server a nastavení času byly úspěšné.
   
    ![spustit diagnostiku 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Vygenerujte balíček pro protokol
Balíček protokol se skládá z příslušných protokoly, které mohou pomoci řešit jakékoli problémy zařízení v Microsoft Support. V této verzi můžete vygenerovat balíček protokolu prostřednictvím místního webového uživatelského rozhraní.

#### <a name="to-generate-the-log-package"></a>Protokol balíček.
1. V místním webovém uživatelském rozhraní, přejděte na **Poradce při potížích s** > **systémové protokoly**.
   
    ![Vygenerujte balíček pro protokol 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. V dolní části stránky klikněte na tlačítko **vytvořit balíček protokolu**. Balíček systémové protokoly se vytvoří. Bude to trvat několik minut.
   
    ![Vygenerujte balíček pro protokolu 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Poté, co balíček byl úspěšně vytvořen, a na stránce se aktualizuje a bude označovat čas a datum vytvoření balíčku, budete upozorněni.
   
    ![Vygenerujte balíček pro protokol 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Klikněte na tlačítko **balíček ke stažení protokolů**. Ve vašem systému se stáhne komprimovaný balíček.
   
    ![Vygenerujte balíček pro protokol 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Můžete rozbalte balíček stažených protokolů a zobrazit soubory systémového protokolu.

## <a name="shut-down-and-restart-your-device"></a>Vypne a restartuje vaše zařízení
Můžete vypnout nebo restartovat virtuální zařízení pomocí místního webového uživatelského rozhraní. Můžeme doporučit, před restartováním, trvat svazky nebo sdílené složky offline na tomto hostiteli a potom zařízení. Tím se minimalizují možnost poškození dat. 

#### <a name="to-shut-down-your-virtual-device"></a>Vypnutí virtuálního zařízení
1. V místním webovém uživatelském rozhraní, přejděte na **údržby** > **nastavení napájení**.
2. V dolní části stránky klikněte na tlačítko **vypnutí**.
   
    ![vypnutí zařízení 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Upozornění se zobrazí s informacemi o tom, že vypnutí zařízení, přeruší všechny vstupně-výstupních operací, které byly v průběhu, což vede k výpadek. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![upozornění vypnutí zařízení](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Budete informováni, že byl zahájen vypnutí počítače.
   
    ![vypnutí zařízení spuštění](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Zařízení se teď vypne. Pokud chcete spustit vaše zařízení, musíte to provést pomocí Správce technologie Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Restartování virtuálního zařízení
1. V místním webovém uživatelském rozhraní, přejděte na **údržby** > **nastavení napájení**.
2. V dolní části stránky klikněte na tlačítko **restartovat**.
   
    ![restartování zařízení](./media/storsimple-ova-web-ui-admin/image36.png)
3. Upozornění se zobrazí s informacemi o tom, že restartování zařízení přeruší jakékoli IOs, které byly v průběhu, výsledkem je výpadek. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![upozornění na restartování](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Budete informováni, že bylo zahájeno restartování.
   
    ![restart zahájen](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Zatímco probíhá restartování, dojde ke ztrátě připojení v uživatelském rozhraní. Restart můžete monitorovat pomocí uživatelského rozhraní pravidelně aktualizuje. Alternativně můžete monitorovat stav restartování zařízení pomocí Správce technologie Hyper-V.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [použít službu StorSimple Manager ke správě vašeho zařízení](storsimple-virtual-array-manager-service-administration.md).

