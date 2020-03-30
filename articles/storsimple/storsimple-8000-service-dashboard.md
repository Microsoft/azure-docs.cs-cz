---
title: Použití souhrnu zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje okno souhrnu služby StorSimple a vysvětluje, jak jej použít ke sledování stavu vašeho řešení StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267648"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Použití okna souhrnu služby pro zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled

Okno souhrnu služby Správce zařízení StorSimple poskytuje souhrnné zobrazení všech zařízení, která jsou připojena ke službě StorSimple Device Manager, zvýraznění zařízení, která potřebují pozornost správce systému. Tento kurz představuje okno souhrnu služby, vysvětluje obsah řídicího panelu a funkce a popisuje úkoly, které můžete provádět z této stránky.

![Souhrn služby](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Příkazy pro správu

V okně souhrnu služby StorSimple se zobrazí možnosti správy služby StorSimple Device Manager a zařízení řady StorSimple 8000 registrovaných k této službě. Zobrazí se příkazy pro správu v horní části okna a na levé straně.

![Panel příkazů](./media/storsimple-8000-service-dashboard/service-summary2.png)

Tyto možnosti slouží k provádění různých operací, jako je například přidání sdílených složek nebo svazků, nebo ke sledování různých úloh spuštěných na zařízeních StorSimple.


## <a name="essentials"></a>Základy

Oblast essentials zachycuje některé důležité vlastnosti, jako je například skupina prostředků, umístění a předplatné, ve kterém byl vytvořen Správce zařízení StorSimple.

![Základy](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Souhrn služby Správce zařízení StorSimple

* Dlaždice **Výstrahy** poskytuje snímek všech aktivních výstrah navšech zařízeních seskupených podle závažnosti výstrahy.

    ![Dlaždice Výstrahy](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Kliknutím na dlaždici se otevře okno **Upozornění,** kde můžete kliknutím na jednotlivé výstrahy zobrazit další podrobnosti o této výstraze, včetně všech doporučených akcí. Výstrahu můžete také vymazat, pokud byl problém vyřešen.

    ![Klikněte na dlaždici upozornění](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Dlaždice **Kapacita** zobrazuje primární úložiště, které je zřízeno a zůstává na všech zařízeních vzhledem k celkovému dostupnému úložišti ve všech zařízeních. **Zřízené** odkazuje na množství úložiště, které je připravena a přidělena pro použití, **Zbývající** odkazuje na zbývající kapacitu, která může být zřízena navšech zařízeních.

    ![Dlaždice kapacity](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **Zbývající vrstvené kapacity** je dostupná kapacita, která může být zřízena, včetně cloudu, zatímco **zbývající místní** je kapacita zbývající na discích připojených k zařízením řady StorSimple 8000.


* V grafu **Využití** uvidíte příslušné metriky pro vaše zařízení. Můžete zobrazit primární úložiště používané ve všech zařízeních a cloudové úložiště spotřebované zařízeními za posledních 7 dní, což je výchozí časové období. 

    ![Dlaždice použití](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Chcete-li zvolit jiné časové měřítko, použijte volbu **Upravit** v pravém horním rohu grafu.

     ![Klikněte na dlaždici využití.](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Export dat grafu](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Dlaždice **Zařízení** obsahuje souhrn počtu zařízení řady StorSimple 8000 ve Správci zařízení StorSimple seskupených podle stavu zařízení. 

    ![Dlaždice Zařízení](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Kliknutím na tuto dlaždici otevřete okno seznamu **Zařízení** a potom kliknutím na jednotlivé zařízení přejdete k podrobnostem souhrnu zařízení specifického pro dané zařízení. Můžete také provádět akce specifické pro zařízení z daného okna souhrnu zařízení. Další informace o souhrnném noži zařízení naleznete v [části Okno souhrnu zařízení](storsimple-8000-device-dashboard.md).

    ![Klikněte na dlaždici zařízení](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Zobrazit protokoly aktivit

Chcete-li zobrazit různé operace prováděné ve Správci zařízení StorSimple, klepněte na odkaz **Protokoly aktivit** na levé straně okna souhrnu služby StorSimple. Tím přejdete do okna **protokoly aktivit,** kde můžete zobrazit souhrn posledních provedených operací.

![Protokoly aktivit](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

