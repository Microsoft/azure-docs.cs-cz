---
title: Souhrn zařízení řady StorSimple 8000 použití | Microsoft Docs
description: Popisuje zařízení služby StorSimple Manager zařízení, souhrn a způsobu jeho použití k zobrazení metriky úložiště a připojené iniciátory a najít sériové číslo a IQN.
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
ms.openlocfilehash: 784d3ce9d8f926b00ac1c6fbf48a05c0b04f900a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874854"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Použití zařízení souhrnné ve službě service Manager zařízení StorSimple

## <a name="overview"></a>Přehled
V okně Souhrn zařízení StorSimple bude stručně charakterizovat informace pro určité zařízení StorSimple, na rozdíl od souhrnu okna služby, které nabízí informace o všech zařízeních, které jsou součástí vašeho řešení Microsoft Azure StorSimple.

V okně Souhrn zařízení poskytuje přehled o zařízení řady StorSimple 8000, které je registrované s danou StorSimple Správce zařízení, zvýraznění těchto problémů zařízení, které vyžadují pozornost. správce systému. Tento kurz představuje souhrn okno zařízení, popisuje obsah a funkce a popisuje úlohy, které můžete provádět v tomto okně.

V okně Souhrn zařízení zobrazí následující informace:

![Souhrn okno zařízení](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Správa příkazového řádku

V okně zařízení StorSimple zobrazí se možnosti pro správu zařízení StorSimple. Příkazy pro správu zobrazí v horní části okna a na levé straně. Pomocí těchto možností můžete přidat sdílené složky nebo svazky, nebo aktualizovat nebo selhání zařízení.

![Správa příkazového řádku](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Základy

Oblasti essentials zaznamená některé důležité vlastnosti, jako, stav, modelu, cílový IQN a verze softwaru. 

![Zařízení essentials](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorování

* **Výstrahy** dlaždice poskytuje snímek všechny aktivní výstrahy pro vaše zařízení seskupené podle závažnosti výstrah.

    ![Dlaždice výstrah](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Kliknutím na dlaždici otevřít **výstrahy** okna a potom klikněte na jednotlivé výstrahy zobrazíte další podrobnosti o této výstraze, včetně všech doporučené akce. Pokud byl problém vyřešen, zrušte výstrahy.

    ![Klikněte na dlaždici výstrah](./media/storsimple-8000-device-dashboard/device-summary10.png)

* **Stav** dlaždice poskytuje přehled o stavu součásti hardwaru pro zařízení, včetně stavu zařízení. Stav zařízení může být offline, online, Deaktivované nebo připravení nastavit.

    ![Dlaždice stavu a stavu](./media/storsimple-8000-device-dashboard/device-summary5.png)

* **Svazky** dlaždice poskytuje přehled o počtu svazků v zařízení seskupené podle stavu.

    ![Dlaždice svazky](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kliknutím na dlaždici otevřít **svazky** seznamu okna a potom klikněte na svazku lze zobrazit nebo upravit jeho vlastnosti.
    
    ![Klikněte na dlaždice svazky](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Další informace najdete v tématu Jak [správu svazků](storsimple-8000-manage-volumes-u2.md).

* V **využití** grafu, můžete zobrazit primárního úložiště používat v zařízení a cloudového úložiště spotřebované za posledních 7 dnů, výchozí časové období.

     ![Dlaždice využití](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Chcete-li vybrat jinou dobu měřítko, použijte **upravit** možnost v pravém horním rohu grafu.

     ![Upravit graf využití](./media/storsimple-8000-device-dashboard/device-summary12.png)

     V tomto grafu můžete zobrazit metrik pro celkový počet primárního úložiště (velikost dat zapsaných hostiteli k zařízení) a celkový počet cloudového úložiště spotřebovávají zařízení v časovém intervalu.
  
     V tomto kontextu *primárního úložiště* odkazuje na celkovém množství dat zapsaných hostitele a můžete rozdělit podle typu svazku: *primární vrstvené úložiště* zahrnuje obě místně uložená data a data vrstvené na v cloudu. *Primární místně vázaný úložiště* zahrnuje jenom místně uložená data. *Cloudového úložiště*, na druhé straně je měření celkové množství dat uložených v cloudu. Toto úložiště zahrnuje vrstvený dat a zálohování. Data uložená v cloudu je s odstraněním duplicitních dat a komprimované, zatímco primární úložiště určuje velikost úložiště využít, než je s odstraněním duplicitních dat a komprimované data. (Těchto dvou čísel, kde získáte představu míry komprese můžete porovnat.) Pro obě primární a cloudového úložiště, hodnoty uvedené jsou na základě sledování četnosti nakonfigurujete. Například pokud se rozhodnete frekvencí jeden týden, pak graf zobrazuje data pro každý den předchozího týdne.

     Pokud chcete zobrazit velikost úložiště v cloudu využívat v čase, vyberte **CLOUDOVÉ úložiště používá** možnost. Pokud chcete zobrazit celkový úložiště, která byla vytvořena pro hostitele, vyberte **primární VRSTVENÉ úložiště používá** a **primární MÍSTNĚ PŘIPNUTÝ úložiště používá** možnosti. 
     Další informace najdete v tématu [použít službu StorSimple Manager zařízení k monitorování zařízení StorSimple](storsimple-monitor-device.md).


* **Kapacity** dlaždice zobrazí primárního úložiště, která je zřízený a zbývající u všech zařízení relativně k celkové úložiště k dispozici pro stejné. **Zřízení** odkazuje na velikost úložiště, která je připravená a přidělení k použití, **zbývající** odkazuje na zbývající kapacitu, kterou může být zřízen přes toto zařízení. 

    ![Dlaždice využití](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Klikněte na tuto dlaždici zobrazit, jak je zřízený kapacitu mezi vrstvami a místně vázaných svazků. **Zbývající vrstvené** kapacita je dostupné kapacity, který může být zřízen včetně cloudu, zatímco **zbývající místní** je kapacita zbývající u disků připojených k tomuto zařízení.

    ![Klikněte na graf využití](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Další kroky
* Další informace o [souhrnu okna služby StorSimple](storsimple-8000-service-dashboard.md).
* Další informace o [pomocí služby StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

