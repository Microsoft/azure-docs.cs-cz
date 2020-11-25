---
title: Použití souhrnu zařízení ve službě StorSimple Device Manager
description: Popisuje souhrn zařízení StorSimple Device Manager služby a jeho použití k zobrazení metrik úložiště a připojených iniciátorů a zjištění sériového čísla a názvu IQN.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 34bbf4d723e46663efe77560245db74d9dea1fc4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017147"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Použití souhrnu zařízení ve službě StorSimple Device Manager

## <a name="overview"></a>Přehled
V okně Souhrn zařízení StorSimple najdete přehled informací o konkrétním zařízení StorSimple, a to na rozdíl od okna s přehledem služby, které poskytuje informace o všech zařízeních, která jsou součástí vašeho řešení Microsoft Azure StorSimple.

Okno Souhrn zařízení nabízí souhrnné zobrazení zařízení řady StorSimple 8000, které je zaregistrované v dané StorSimple Device Manager, a zvýrazní problémy zařízení, které vyžadují pozornost správce systému. V tomto kurzu se seznámíte s oknem souhrn zařízení, vysvětlujete obsah a funkci a popisujete úlohy, které můžete z tohoto okna provádět.

Okno Souhrn zařízení zobrazuje následující informace:

![Okno Souhrn zařízení](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Panel příkazů pro správu

V okně zařízení StorSimple se zobrazí možnosti správy zařízení StorSimple. Zobrazí se příkazy pro správu v horní části okna a na levé straně. Tyto možnosti použijte k přidání sdílených složek nebo svazků nebo k aktualizaci nebo převzetí služeb při selhání zařízení.

![Panel příkazů pro správu](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Základy

Oblast základy zachycuje některé důležité vlastnosti, jako je například stav, model, cílové rozhraní IQN a verze softwaru. 

![Základy zařízení](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorování

* Dlaždice **výstrahy** poskytuje snímek všech aktivních výstrah pro vaše zařízení seskupených podle závažnosti výstrahy.

    ![Dlaždice výstrah](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Kliknutím na dlaždici otevřete okno **výstrahy** a potom klikněte na jednotlivá upozornění. zobrazí se další podrobnosti o této výstraze, včetně všech doporučených akcí. Výstrahu můžete také vymazat, pokud byl problém vyřešen.

    ![Kliknout na dlaždici výstrah](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Dlaždice **stav a stav** nabízí přehled o stavu hardwarových součástí zařízení, včetně stavu zařízení. Stav zařízení může být offline, online, deaktivovaný nebo připravený k nastavení.

    ![Dlaždice stavu a stavu](./media/storsimple-8000-device-dashboard/device-summary5.png)

* Dlaždice **svazky** poskytuje souhrn počtu svazků v zařízení seskupených podle stavu.

    ![Dlaždice svazky](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kliknutím na dlaždici otevřete okno seznam **svazků** a potom klikněte na jednotlivé svazky, abyste mohli zobrazit nebo upravit jeho vlastnosti.
    
    ![Dlaždice klikněte na svazky.](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Další informace najdete v tématu [Správa svazků](storsimple-8000-manage-volumes-u2.md).

* V grafu **využití** můžete zobrazit primární úložiště použité v rámci vašeho zařízení a cloudové úložiště spotřebované za posledních 7 dní, což je výchozí časové období.

     ![Dlaždice využití](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Chcete-li zvolit jiné časové měřítko, použijte možnost **Upravit** v pravém horním rohu grafu.

     ![Upravit graf využití](./media/storsimple-8000-device-dashboard/device-summary12.png)

     V tomto grafu si můžete zobrazit metriky celkového primárního úložiště (množství dat zapsaných hostiteli na vaše zařízení) a celkovému cloudového úložiště spotřebovaného vaším zařízením v časovém intervalu.
  
     V tomto kontextu *primární úložiště* označuje celkové množství dat zapsaných hostitelem a může být rozdělené podle typu svazku: *primární vrstvené úložiště* zahrnuje místně uložená data i data vrstvená do cloudu. *Primární místně připojené úložiště* zahrnuje pouze data uložená místně. *Cloudové úložiště* je na druhé straně měřením celkového množství dat uložených v cloudu. Toto úložiště zahrnuje vrstvená data a zálohy. Data uložená v cloudu se odstraňují a komprimují, zatímco primární úložiště indikuje velikost úložiště, která se použila před odstraněním duplicit dat a jejich komprimací. (Tato dvě čísla můžete porovnat a získat tak představu o kompresní frekvenci.) V případě primárního i cloudového úložiště jsou zobrazené částky založené na frekvenci sledování, kterou nakonfigurujete. Pokud například zvolíte jednu z týdenních frekvencí, zobrazí se v grafu data pro každý den v předchozím týdnu.

     Pokud chcete zobrazit velikost cloudového úložiště spotřebovaného v průběhu času, vyberte možnost **Použití cloudového úložiště** . Pokud chcete zobrazit celkové úložiště, které je zapsané hostitelem, vyberte **primární vrstvené úložiště** , které se používá, a **primární místně připojené úložiště** . 
     Další informace najdete v tématu věnovaném [monitorování zařízení StorSimple pomocí služby StorSimple Device Manager](./storsimple-8000-monitor-device.md).


* Na dlaždici **kapacita** se zobrazuje primární úložiště, které je zřízené a zbývající v rámci zařízení vzhledem k celkovému počtu dostupných úložišť. **Zřízené** v tomto případě odkazuje na velikost úložiště, které je připraveno a přiděleno k použití, **zbývající** znamená zbývající kapacitu, kterou lze zřídit v rámci tohoto zařízení. 

    ![Dlaždice využití 2](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Kliknutím na tuto dlaždici zobrazíte informace o tom, jak je kapacita zajištěna napříč vrstvenými a místně připnuté svazky. **Zbývající vrstvená** kapacita je dostupná kapacita, kterou je možné zřídit včetně cloudu, zatímco **zbývající místní** kapacita je zbývající na discích připojených k tomuto zařízení.

    ![Kliknout na graf využití](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o okně s [přehledem služby StorSimple](storsimple-8000-service-dashboard.md).
* Přečtěte si další informace o [používání služby StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).