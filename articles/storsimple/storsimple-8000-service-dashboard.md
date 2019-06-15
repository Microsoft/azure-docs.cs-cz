---
title: Použití zařízení StorSimple řady 8000 souhrn | Dokumentace Microsoftu
description: Popisuje okně s přehledem služby StorSimple a vysvětluje, jak použít ho k monitorování stavu vašeho řešení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: c174f6ce0fb3d40af953be205a7bfcca60fbfeec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60632972"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Použijte okno s přehledem služby pro zařízení StorSimple řady 8000

## <a name="overview"></a>Přehled

Okně s přehledem služby Správce zařízení StorSimple poskytuje přehled o všech zařízeních, které jsou připojené ke službě Správce zařízení StorSimple, zvýraznění taková zařízení, která vyžadují pozornost. správce systému. Tento kurz představuje okně s přehledem služby, vysvětluje obsahu řídicího panelu a funkce a popisuje úlohy, které můžete provádět z této stránky.

![Souhrn služby](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Příkazy pro správu

V souhrnu okně služby StorSimple se zobrazí možnosti pro správu služby Správce zařízení StorSimple a zařízeních StorSimple řady 8000 k této službě zaregistrované. Příkazy pro správu se zobrazí v horní části okna a na levé straně.

![Panel příkazů](./media/storsimple-8000-service-dashboard/service-summary2.png)

Pomocí těchto možností můžete provádět různé operace, jako jsou přidání sdílených složek nebo svazků, monitorování nebo různé úlohy běžící na zařízeních StorSimple.


## <a name="essentials"></a>Základy

Oblasti essentials pojednává o některých důležité vlastnosti, jako je skupina prostředků, umístění a předplatné, ve kterém byl vytvořen Správce zařízení StorSimple.

![Základy](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Přehled služby Správce zařízení StorSimple

* **Výstrahy** dlaždice obsahuje snímek všech aktivních výstrah napříč všemi zařízeními, seskupené podle závažnosti výstrahy.

    ![Dlaždice výstrah](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Kliknutí na dlaždici otevře **výstrahy** okno, kde můžete kliknout na jednotlivé výstrahy zobrazíte další podrobnosti o výstraze, včetně všech doporučené akce. Výstrahy můžete také zrušit, pokud byl problém vyřešen.

    ![Kliknutím na dlaždici upozornění](./media/storsimple-8000-service-dashboard/service-summary8.png)

* **Kapacity** zobrazí dlaždice ukazuje primární úložiště, která je zřízená a zbývající napříč všemi zařízeními vzhledem k celkový úložiště k dispozici na všech zařízeních. **Zřízení** odkazuje na velikost úložiště, který je připravený a přidělené, **zbývající** odkazuje na zbývající kapacitu, kterou je možné zřídit napříč všemi zařízeními.

    ![Kapacity dlaždice](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **Zbývající Vrstvená** kapacita je k dispozici kapacita, která se dá zřídit včetně cloudu, zatímco **zbývající místní** je kapacita zbývající na disky připojené k StorSimple 8000 Řada zařízení.


* V **využití** grafu, můžete zobrazit důležité metriky pro vaše zařízení. Můžete zobrazit z primárního úložiště používá na všech zařízeních a cloudové úložiště spotřebovávané zařízení za posledních 7 dnů, výchozí časové období. 

    ![Dlaždice využití](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Chcete-li vybrat různé časové měřítko, použijte **upravit** možnost v pravém horním rohu grafu.

     ![Klikněte na dlaždici využití](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Exportovat data grafu](./media/storsimple-8000-service-dashboard/service-summary11.png)

* **Zařízení** dlaždice obsahuje souhrn počtu zařízení řady StorSimple 8000 v Správce zařízení StorSimple seskupených podle stavu zařízení. 

    ![Dlaždice zařízení](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Klikněte na tuto dlaždici otevřete **zařízení** seznamu okno a potom klikněte na jednotlivá zařízení k podrobnostem souhrn zařízení, která je specifická pro zařízení. Akce specifické pro zařízení můžete provést také z okno s přehledem dané zařízení. Další informace o okně s přehledem zařízení, přejděte na [okno s přehledem zařízení](storsimple-8000-device-dashboard.md).

    ![Kliknutím na dlaždici zařízení](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Zobrazení protokolů aktivit

Chcete-li zobrazit různé operace provádějí v rámci Správce zařízení StorSimple, klikněte na tlačítko **protokoly aktivit** odkazu na levé straně okno s přehledem StorSimple služby. Tím přejdete **protokoly aktivit** okno, ve kterém můžete prohlédnout souhrnné informace o nedávné prováděných operací.

![Protokoly aktivit](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Další postup

* Další informace o tom, jak [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

