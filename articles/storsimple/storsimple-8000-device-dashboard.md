---
title: Použití souhrnu zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje souhrn servisního zařízení Správce zařízení StorSimple a způsob jeho použití k zobrazení metrik úložiště a připojených iniciátorů a k nalezení sériového čísla a čísla IQN.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 1d88af2c0739c30b2562bad7660015b890e8159c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60578224"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Použití souhrnu zařízení ve službě Správce zařízení StorSimple

## <a name="overview"></a>Přehled
Okno souhrnu zařízení StorSimple poskytuje přehled informací pro konkrétní zařízení StorSimple, na rozdíl od okna souhrnu služby, který poskytuje informace o všech zařízeních zahrnutých v řešení Microsoft Azure StorSimple.

Okno souhrnu zařízení poskytuje souhrnné zobrazení zařízení řady StorSimple 8000, které je registrováno v daném Správci zařízení StorSimple, a upozorňuje na problémy se zařízením, které vyžadují pozornost správce systému. Tento kurz představuje okno souhrnu zařízení, vysvětluje obsah a funkce a popisuje úkoly, které můžete provádět z tohoto okna.

V souhrnném okně zařízení se zobrazí následující informace:

![Souhrnný nůž zařízení](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Panel příkazů Správa

V okně zařízení StorSimple se zobrazí možnosti správy zařízení StorSimple. Zobrazí se příkazy pro správu v horní části okna a na levé straně. Pomocí těchto možností můžete přidat sdílené složky nebo svazky nebo aktualizovat zařízení nebo je přepojit na po selhání.

![Panel příkazů Správa](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Základy

Oblast essentials zachycuje některé důležité vlastnosti, jako je stav, model, cílové IQN a verze softwaru. 

![Základní informace o zařízení](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorování

* Dlaždice **Výstrahy** poskytuje snímek všech aktivních výstrah pro vaše zařízení seskupených podle závažnosti výstrahy.

    ![Dlaždice výstrahy](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Kliknutím na dlaždici otevřete okno **Výstrahy** a kliknutím na jednotlivou výstrahu zobrazíte další podrobnosti o této výstraze, včetně doporučených akcí. Výstrahu můžete také vymazat, pokud byl problém vyřešen.

    ![Klikněte na dlaždici upozornění.](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Dlaždice **Stav a stav** poskytuje přehled o stavu hardwarové součásti pro zařízení včetně stavu zařízení. Stav zařízení může být offline, online, deaktivován nebo připraven k nastavení.

    ![Dlaždice stavu a stavu](./media/storsimple-8000-device-dashboard/device-summary5.png)

* Dlaždice **Objemy** obsahuje souhrn počtu svazků v zařízení seskupených podle stavu.

    ![Dlaždice Objemy](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kliknutím na dlaždici otevřete okno seznamu **Svazky** a kliknutím na jednotlivý svazek zobrazte nebo upravte jeho vlastnosti.
    
    ![Klikněte na dlaždici svazků](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Další informace naleznete v tématu [správa svazků](storsimple-8000-manage-volumes-u2.md).

* V grafu **Využití** můžete zobrazit primární úložiště používané v celém zařízení a cloudové úložiště spotřebované za posledních 7 dní, což je výchozí časové období.

     ![Dlaždice použití](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Chcete-li zvolit jiné časové měřítko, použijte volbu **Upravit** v pravém horním rohu grafu.

     ![Upravit graf využití](./media/storsimple-8000-device-dashboard/device-summary12.png)

     V tomto grafu můžete zobrazit metriky pro celkové primární úložiště (množství dat napsaných hostiteli do vašeho zařízení) a celkové cloudové úložiště spotřebované zařízením po určitou dobu.
  
     V tomto kontextu *primární úložiště* odkazuje na celkové množství dat zapsaných hostitelem a lze rozdělit podle typu svazku: *primární vrstvené úložiště* zahrnuje místně uložená data a data vrstvená do cloudu. *Primární místně připnuté úložiště* zahrnuje pouze data uložená místně. *Cloudové úložiště*je na druhé straně měření celkového množství dat uložených v cloudu. Toto úložiště zahrnuje vrstvená data a zálohy. Data uložená v cloudu jsou deduplikována a komprimována, zatímco primární úložiště označuje velikost úložiště použitého před odstraněním duplicit a komprimací dat. (Můžete porovnat tyto dvě čísla získat představu o kompresní rychlost.) Pro primární i cloudové úložiště jsou zobrazené částky založeny na frekvenci sledování, kterou nakonfigurujete. Pokud například zvolíte frekvenci jednoho týdne, graf zobrazí data pro každý den v předchozím týdnu.

     Chcete-li zobrazit množství cloudového úložiště spotřebovaného v průběhu času, vyberte možnost **POUŽITÉ CLOUDOVÉ ÚLOŽIŠTĚ.** Chcete-li zobrazit celkové úložiště, které bylo napsáno hostitelem, vyberte **primární vrstvené úložiště použité** a primární místně **připnuté úložiště použité** možnosti. 
     Další informace naleznete [v tématu Sledování zařízení StorSimple Device Manager pomocí služby StorSimple](storsimple-monitor-device.md)Device Manager .


* **Dlaždice Kapacita** zobrazuje primární úložiště, které je zřízeno a zůstává v celém zařízení vzhledem k celkovému dostupnému úložišti pro stejné. **Zřízené** odkazuje na množství úložiště, které je připravena a přidělena pro použití, **Zbývající** odkazuje na zbývající kapacitu, která může být zřízena v rámci tohoto zařízení. 

    ![Dlaždice použití](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Kliknutím na tuto dlaždici zobrazíte, jak se kapacita zřápá napříč vrstvené a místně vázaných svazků. **Zbývající vrstvené kapacity** je dostupná kapacita, která může být zřízena, včetně cloudu, zatímco **zbývající místní** je kapacita zbývající na discích připojených k tomuto zařízení.

    ![Klikněte na graf využití.](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Další kroky
* Další informace o [okně souhrnu služby StorSimple](storsimple-8000-service-dashboard.md).
* Další informace o [správě zařízení StorSimple Device Manager pomocí služby StorSimple](storsimple-8000-manager-service-administration.md).

