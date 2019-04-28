---
title: Použití zařízení StorSimple řady 8000 souhrn | Dokumentace Microsoftu
description: Popisuje přehledu zařízení služby Správce zařízení StorSimple a jak ji používat k zobrazení metrik storage a propojené iniciátory a vyhledat sériové číslo a název IQN.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578224"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Použití přehledu služby Správce zařízení StorSimple zařízení

## <a name="overview"></a>Přehled
Okně s přehledem zařízení StorSimple poskytuje přehled o informace pro konkrétní zařízení StorSimple, na rozdíl od okně přehledu služby, která poskytuje informace o všech zařízeních, které jsou součástí řešení Microsoft Azure StorSimple.

Okně s přehledem zařízení poskytuje přehled o zařízení řady StorSimple 8000, která je registrována s danou StorSimple Device Manager, zvýraznění problémy zařízení, které vyžadují pozornost. správce systému. Tento kurz představuje okně s přehledem zařízení, vysvětluje obsah a funkce a popisuje úlohy, které můžete provádět z tohoto okna.

Okně s přehledem zařízení zobrazí následující informace:

![Okno souhrn zařízení](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Panel příkazů správy

V okně zařízení StorSimple zobrazí se možnosti pro správu zařízení StorSimple. Příkazy pro správu se zobrazí v horní části okna a na levé straně. Pomocí těchto možností můžete přidat sdílené složky nebo svazky, aktualizovat nebo převzetí služeb při selhání zařízení.

![Panel příkazů správy](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Základy

Oblasti essentials pojednává o některých důležitých vlastností, jako, stav, modelu, cílové IQN a verze softwaru. 

![Základy zařízení](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorování

* **Výstrahy** dlaždice poskytuje snímek všechny aktivní výstrahy pro vaše zařízení, seskupené podle závažnosti výstrahy.

    ![Dlaždice výstrah](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Kliknutím na dlaždici otevřít **výstrahy** okno a potom klikněte na jednotlivé výstrahy zobrazíte další podrobnosti o této výstraze, včetně všech doporučené akce. Výstrahy můžete také zrušit, pokud byl problém vyřešen.

    ![Kliknutím na dlaždici upozornění](./media/storsimple-8000-device-dashboard/device-summary10.png)

* **Stav** dlaždice poskytuje přehled o stavu komponenty hardwaru pro zařízení i stav zařízení. Stav zařízení může být offline, online, Deaktivované nebo připraveno k nastavení.

    ![Dlaždice stavu a stavu](./media/storsimple-8000-device-dashboard/device-summary5.png)

* **Svazky** dlaždice obsahuje souhrn počtu svazků v zařízení seskupených podle stavu.

    ![Dlaždice svazky](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kliknutím na dlaždici otevřít **svazky** seznamu okno a potom klikněte na jednom svazku lze zobrazit nebo upravit její vlastnosti.
    
    ![Kliknutím na dlaždici svazky](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Další informace najdete v tématu Jak [Správa svazků](storsimple-8000-manage-volumes-u2.md).

* V **využití** grafu, můžete zobrazit z primárního úložiště použít v rámci vašeho zařízení a cloudové úložiště spotřebované za posledních 7 dnů, výchozí časové období.

     ![Dlaždice využití](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Chcete-li vybrat různé časové měřítko, použijte **upravit** možnost v pravém horním rohu grafu.

     ![Upravit graf využití](./media/storsimple-8000-device-dashboard/device-summary12.png)

     V tomto grafu můžete zobrazit metriky pro celkový počet primární úložiště (množství dat zapsaných hostiteli vaše zařízení) a celkový počet cloudové úložiště spotřebovávané zařízení po určitou dobu.
  
     V tomto kontextu *primárního úložiště* odkazuje na celkové množství dat zapsaných hostitele a je možné rozdělit podle typu svazku: *primární vrstvené úložiště* obsahuje jak místně uložená data a data Vrstvená do v cloudu. *Primární místně připojené úložiště* obsahuje pouze data uložená místně. *Cloudové úložiště*, na druhé straně je měření celkové množství dat uložených v cloudu. Toto úložiště zahrnuje Vrstvená data a zálohy. Data uložená v cloudu se odstraňují duplicity a komprimovaný, zatímco primární úložiště označuje velikost úložiště využitá předtím, než je s odstraněním duplicitních dat a komprimovaná data. (Můžete porovnat tato dvě čísla, kde získáte představu míry komprese.) Pro obě primární a cloudového úložiště, zobrazené částky jsou založeny na frekvenci sledování nakonfigurujete. Například pokud se rozhodnete frekvenci jeden týden, pak graf zobrazuje data za každý den předchozího týdne.

     Pokud chcete zobrazit množství cloudové úložiště spotřebované v čase, vyberte **CLOUDOVÉ úložiště používat** možnost. Pokud chcete zobrazit celkový úložiště, které jsou napsány hostitele, vyberte **primární VRSTVENÉ úložiště použít** a **primární MÍSTNĚ připojené úložiště použít** možnosti. 
     Další informace najdete v tématu [použít k monitorování zařízení StorSimple ve službě Správce zařízení StorSimple](storsimple-monitor-device.md).


* **Kapacity** dlaždici se zobrazuje primární úložiště, která je zřízená a zbývající zařízení vzhledem k celkový úložiště k dispozici pro stejné. **Zřízení** odkazuje na velikost úložiště, který je připravený a přidělené, **zbývající** odkazuje na zbývající kapacitu, kterou je možné zřídit přes toto zařízení. 

    ![Dlaždice využití](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Klikněte na tuto dlaždici zobrazíte, jak je zřízené kapacity ve místně připnuté a vrstvené svazky. **Zbývající Vrstvená** kapacita je k dispozici kapacita, která se dá zřídit včetně cloudu, zatímco **zbývající místní** je kapacita zbývající na disky připojené k tomuto zařízení.

    ![Klikněte na graf využití](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Další postup
* Další informace o [okno s přehledem služby StorSimple](storsimple-8000-service-dashboard.md).
* Další informace o [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

