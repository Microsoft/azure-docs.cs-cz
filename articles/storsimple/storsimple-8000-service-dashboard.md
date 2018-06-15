---
title: Souhrn zařízení řady StorSimple 8000 použití | Microsoft Docs
description: Popisuje souhrnu okna služby StorSimple a vysvětluje, jak použít jej k monitorování stavu vašeho řešení StorSimple.
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
ms.openlocfilehash: d987a4ae170f21532a886552cbe1eb5a0d25fc3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874826"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Pro zařízení řady StorSimple 8000 použít okno Souhrn služby

## <a name="overview"></a>Přehled

V okně Souhrn služby StorSimple Manager zařízení poskytuje přehled o všech zařízení, které jsou připojené ke službě StorSimple Manager zařízení zvýraznění těchto zařízení, které vyžadují pozornost. správce systému. Tento kurz představuje souhrn okno služby, popisuje obsah řídicího panelu a funkce a popisuje úlohy, které můžete provést z této stránky.

![Souhrn služby](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Příkazy pro správu

V souhrnu okně služby StorSimple zobrazí možnosti pro správu služby StorSimple Manager zařízení a řadu zařízení StorSimple 8000 registrované k této službě. Příkazy pro správu zobrazí v horní části okna a na levé straně.

![Panel příkazů](./media/storsimple-8000-service-dashboard/service-summary2.png)

Pomocí těchto možností můžete provádět různé operace, například jako přidat sdílené složky nebo svazky nebo monitorování různé úlohy běžící na zařízení StorSimple.


## <a name="essentials"></a>Základy

Oblasti essentials zaznamená některé důležité vlastnosti například skupinu prostředků, umístění a předplatné, ve kterém byla vytvořena vaše zařízení StorSimple Manager.

![Základy](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Souhrn služby StorSimple Manager zařízení

* **Výstrahy** dlaždice obsahuje snímek všech aktivních výstrah ve všech zařízeních, seskupené podle závažnosti výstrah.

    ![Dlaždice výstrah](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Kliknutím na dlaždici otevře **výstrahy** okno, kde můžete kliknout na jednotlivé výstrahy zobrazíte další podrobnosti o výstraze, včetně všech doporučené akce. Pokud byl problém vyřešen, zrušte výstrahy.

    ![Klikněte na dlaždici výstrahy](./media/storsimple-8000-service-dashboard/service-summary8.png)

* **Kapacity** zobrazí dlaždice zobrazuje primární úložiště, které je zřízený a zbývající ve všech zařízeních relativně k celkové úložiště k dispozici ve všech zařízeních. **Zřízení** odkazuje na velikost úložiště, která je připravená a přidělení k použití, **zbývající** odkazuje na zbývající kapacitu, kterou může být zřízen ve všech zařízeních.

    ![Kapacity dlaždice](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **Zbývající vrstvené** kapacita je dostupné kapacity, který může být zřízen včetně cloudu, zatímco **zbývající místní** je kapacita zbývající u disků připojených k řadu zařízení StorSimple 8000.


* V **využití** grafu, můžete zobrazit relevantní metriky pro vaše zařízení. Můžete zobrazit primárního úložiště používá na všech zařízeních a cloudového úložiště využívat zařízení za posledních 7 dnů, výchozí časové období. 

    ![Dlaždice využití](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Chcete-li vybrat jinou dobu měřítko, použijte **upravit** možnost v pravém horním rohu grafu.

     ![Klikněte na dlaždici využití](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Export dat grafu](./media/storsimple-8000-service-dashboard/service-summary11.png)

* **Zařízení** dlaždice obsahuje souhrn počtu řadu zařízení StorSimple 8000 ve vaší StorSimple Správci zařízení seskupené podle stavu zařízení. 

    ![Dlaždice zařízení](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Klikněte na tuto dlaždici otevřete **zařízení** okno a potom klikněte na k jednotlivým zařízením přejdete do souhrn mobilního zařízení, která je specifická pro zařízení. Můžete také provést akce konkrétní zařízení ze souhrnné okno daného zařízení. Další informace o souhrnu okna zařízení, přejděte na [souhrnu okna zařízení](storsimple-8000-device-dashboard.md).

    ![Klikněte na dlaždici zařízení](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Zobrazit protokoly aktivity

Chcete-li zobrazit různé operace, které provádějí v rámci vašeho správce zařízení StorSimple, klikněte na tlačítko **protokoly aktivity** odkaz na levé straně vaší okna Souhrn služby StorSimple. Tím přejdete **protokoly aktivity** okno, kde se zobrazí souhrn posledních prováděných operací.

![Protokoly aktivit](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

