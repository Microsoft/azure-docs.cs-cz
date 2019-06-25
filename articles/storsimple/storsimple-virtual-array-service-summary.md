---
title: Okno s přehledem služby StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje okně s přehledem služby Správce zařízení StorSimple a vysvětluje, jak použít ho k monitorování stavu StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 9c05bddaeb3c34400db1ec75c624ef00a85d9444
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720714"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Použití okně s přehledem služby Správce zařízení StorSimple připojené k StorSimple Virtual Array
## <a name="overview"></a>Přehled
Služba okně s přehledem pro správce zařízení StorSimple poskytuje souhrnný přehled virtuálních polí StorSimple (označované také jako StorSimple místní virtuální zařízení nebo virtuální zařízení), které jsou připojené k vaší službě, zvýraznění těch, které je třeba systému správcovská pozornost. Tento kurz představuje okně s přehledem služby, vysvětluje obsah a funkce a popisuje úlohy, které můžete provádět z tohoto okna.

![Řídicí panel služby](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Příkazy pro správu a essentials
V okně s přehledem StorSimple zobrazí se možnosti pro správu služby Správce zařízení StorSimple, jakož i virtuální pole určená k této službě zaregistrované. Příkazy pro správu se zobrazí v horní části okna a na levé straně.

Pomocí těchto možností můžete provádět různé operace, jako jsou přidání sdílených složek nebo svazků, monitorování nebo různé úlohy běžící na virtuální pole.

Oblasti essentials pojednává o některých důležité vlastnosti, jako je skupina prostředků, umístění a předplatné, ve kterém byl vytvořen Správce zařízení StorSimple.

## <a name="storsimple-device-manager-service-summary"></a>Přehled služby Správce zařízení StorSimple
* **Výstrahy** dlaždice obsahuje snímek všech aktivních výstrah napříč všemi zařízeními virtuální, seskupené podle závažnosti výstrahy. Kliknutí na dlaždici otevře **výstrahy** okno, kde můžete kliknout na jednotlivé výstrahy zobrazíte další podrobnosti o výstraze, včetně všech doporučené akce. Výstrahy můžete také zrušit, pokud byl problém vyřešen.
* **Kapacity** zobrazí dlaždice ukazuje primární úložiště, která je zřízená a zbývající všechna virtuální zařízení vzhledem k celkový úložiště k dispozici ve všech virtuálních zařízeních. **Zřízení** odkazuje na velikost úložiště, který je připravený a přidělené, **zbývající** odkazuje na zbývající kapacitu, která se dá zřídit ve všech virtuálních zařízeních. **Zbývající Vrstvená** kapacita je k dispozici kapacita, která se dá zřídit včetně cloudu, zatímco **zbývající místní** je zbývající na disky připojené k virtuální pole určená kapacita.
* V **využití** grafu, pro vaše virtuální zařízení si můžete prohlédnout důležité metriky. Můžete zobrazit z primárního úložiště použít pro všechna virtuální zařízení, stejně jako cloudové úložiště spotřebovávané virtuálních zařízení za posledních 7 dnů, výchozí časové období. Použití **upravit** možnost v pravém horním rohu grafu vyberte jiné časové měřítko.
* **Zařízení** dlaždice obsahuje souhrn počtu virtuální pole určená v Správce zařízení StorSimple seskupených podle stavu zařízení. Klikněte na tuto dlaždici otevřete **zařízení** seznamu okno a potom klikněte na jednotlivá zařízení k podrobnostem souhrn zařízení, která je specifická pro zařízení. Konkrétní akce zařízení můžete provést také z okno s přehledem dané zařízení. Další informace o okně s přehledem zařízení, přejděte na [okno s přehledem zařízení](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Zobrazení protokolů aktivit
Chcete-li zobrazit různé operace provádějí v rámci Správce zařízení StorSimple, klikněte na tlačítko **protokoly aktivit** odkazu na levé straně okno s přehledem StorSimple služby. Tím přejdete **protokoly aktivit** okno, ve kterém můžete prohlédnout souhrnné informace o nedávné prováděných operací.

![Protokoly aktivit](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Další postup
Zjistěte, jak [pomocí místního webového uživatelského rozhraní pro správu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

