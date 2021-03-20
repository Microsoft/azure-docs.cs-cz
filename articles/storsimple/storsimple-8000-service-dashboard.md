---
title: Pro zařízení řady StorSimple 8000 series použít okno s přehledem služby
description: Popisuje okno s přehledem služby StorSimple a vysvětluje, jak ho použít k monitorování stavu řešení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 7b4b697b3d27b57212fc59396e1f8111e297d6cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514949"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Použití okna s přehledem služby pro zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled

Okno StorSimple Správce zařízení Service Summary poskytuje souhrnné zobrazení všech zařízení, která jsou připojená ke službě StorSimple Správce zařízení, a zvýrazňují taková zařízení, která potřebují pozornost správce systému. V tomto kurzu se seznámíte s oknem Přehled služby, vysvětlujete obsah a funkci řídicího panelu a popisujete úkoly, které můžete z této stránky provádět.

![Souhrn služby](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Příkazy pro správu

V okně s přehledem služby StorSimple uvidíte možnosti správy služby StorSimple Správce zařízení a zařízení řady StorSimple 8000, která jsou zaregistrovaná v této službě. Zobrazí se příkazy pro správu v horní části okna a na levé straně.

![Panel příkazů](./media/storsimple-8000-service-dashboard/service-summary2.png)

Pomocí těchto možností můžete provádět různé operace, například přidat sdílené složky nebo svazky nebo monitorovat různé úlohy běžící na zařízeních StorSimple.


## <a name="essentials"></a>Základy

V oblasti základy se zachycují některé důležité vlastnosti, jako je například skupina prostředků, umístění a předplatné, ve kterém byl vytvořen StorSimple Správce zařízení.

![Základy](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Shrnutí služby StorSimple Správce zařízení

* Dlaždice **výstrahy** poskytuje snímek všech aktivních výstrah napříč všemi zařízeními seskupených podle závažnosti výstrahy.

    ![Dlaždice výstrahy](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Kliknutím na dlaždici otevřete okno **výstrahy** , kde můžete kliknout na jednotlivá upozornění a zobrazit další podrobnosti o této výstraze, včetně případných doporučených akcí. Výstrahu můžete také vymazat, pokud byl problém vyřešen.

    ![Dlaždice klikněte na tlačítko výstrahy](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Na dlaždici **kapacita** se zobrazuje primární úložiště, které je zřízené a zbývající ve všech zařízeních vzhledem k celkovému úložišti dostupnému napříč všemi zařízeními. **Zřízené** služby se vztahují na velikost úložiště, která je připravená a přidělená k použití, **zbývající** znamená zbývající kapacitu, kterou je možné zřídit napříč všemi zařízeními.

    ![Dlaždice kapacity](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **Zbývající vrstvená** kapacita je dostupná kapacita, kterou je možné zřídit včetně cloudu, zatímco **zbývající místní** kapacita je zbývající na discích připojených k zařízením řady StorSimple 8000.


* V grafu **využití** uvidíte relevantní metriky pro vaše zařízení. Můžete zobrazit primární úložiště používané napříč všemi zařízeními a cloudové úložiště spotřebované zařízeními za posledních 7 dní, což je výchozí časové období. 

    ![Dlaždice využití](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Chcete-li zvolit jiné časové měřítko, použijte možnost **Upravit** v pravém horním rohu grafu.

     ![Kliknout na dlaždici využití](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Exportovat data grafu](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Dlaždice **zařízení** poskytuje souhrn počtu zařízení řady StorSimple 8000 ve vaší StorSimple Správce zařízení seskupených podle stavu zařízení. 

    ![Dlaždice zařízení](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Kliknutím na tuto dlaždici otevřete okno seznam **zařízení** a potom klikněte na jednotlivá zařízení, abyste přešli na souhrn zařízení, který je specifický pro dané zařízení. Akce specifické pro zařízení můžete také provádět z daného okna souhrnu zařízení. Pokud chcete získat další informace o okně Souhrn zařízení, klikněte na [okno Souhrn zařízení](storsimple-8000-device-dashboard.md).

    ![Dlaždice zařízení klikněte na tlačítko.](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Zobrazení protokolů aktivit

Pokud chcete zobrazit různé operace provedené v rámci StorSimple Správce zařízení, klikněte na odkaz **protokoly aktivit** na levé straně okna s přehledem služby StorSimple. Tím přejdete do okna **protokoly aktivit** , kde můžete zobrazit souhrn posledních operací, které se provedly.

![Protokoly aktivit](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o tom, jak [používat službu StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

