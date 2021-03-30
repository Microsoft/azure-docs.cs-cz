---
title: Okno souhrnu služby Virtual Array StorSimple | Microsoft Docs
description: Popisuje okno s přehledem služby StorSimple Správce zařízení a vysvětluje, jak ho používat k monitorování stavu virtuálních polí StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 31220a8f8b012e08d46195b60a26be9cfc261be5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514517"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Pro StorSimple Správce zařízení připojená k virtuálnímu poli StorSimple použít okno s přehledem služby
## <a name="overview"></a>Přehled
Okno Souhrn služby pro StorSimple Správce zařízení poskytuje souhrnné zobrazení virtuálních polí StorSimple (označují se také jako StorSimple místní virtuální zařízení nebo virtuální zařízení), která jsou připojená k vaší službě, a zvýrazňují ty, které potřebují pozornost správce systému. V tomto kurzu se seznámíte s oknem s přehledem služby, vysvětlením obsahu a funkce a popisujete úkoly, které můžete z tohoto okna provádět.

![Řídicí panel služby](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Příkazy pro správu a základy
V okně Souhrn StorSimple se zobrazí možnosti správy služby StorSimple Správce zařízení a také virtuální pole zaregistrovaná v této službě. Zobrazí se příkazy pro správu v horní části okna a na levé straně.

Pomocí těchto možností můžete provádět různé operace, například přidat sdílené složky nebo svazky nebo monitorovat různé úlohy běžící ve virtuálních polích.

V oblasti základy se zachycují některé důležité vlastnosti, jako je například skupina prostředků, umístění a předplatné, ve kterém byl vytvořen StorSimple Správce zařízení.

## <a name="storsimple-device-manager-service-summary"></a>Shrnutí služby StorSimple Správce zařízení
* Dlaždice **výstrahy** poskytuje snímek všech aktivních výstrah napříč všemi virtuálními zařízeními seskupenými podle závažnosti výstrahy. Kliknutím na dlaždici otevřete okno **výstrahy** , kde můžete kliknout na jednotlivá upozornění a zobrazit další podrobnosti o této výstraze, včetně případných doporučených akcí. Výstrahu můžete také vymazat, pokud byl problém vyřešen.
* Na dlaždici **kapacita** se zobrazuje primární úložiště, které je zřízené a zbývající ve všech virtuálních zařízeních vzhledem k celkovému úložišti dostupnému napříč všemi virtuálními zařízeními. **Zřízené** služby se vztahují na velikost úložiště, která je připravená a přidělená k použití, **zbývající** znamená zbývající kapacitu, kterou je možné zřídit napříč všemi virtuálními zařízeními. **Zbývající vrstvená** kapacita je dostupná kapacita, kterou je možné zřídit včetně cloudu, zatímco **zbývající místní** kapacita je zbývající na discích připojených k virtuálním polím.
* V grafu **využití** uvidíte relevantní metriky pro virtuální zařízení. Můžete zobrazit primární úložiště používané napříč všemi virtuálními zařízeními a cloudové úložiště spotřebované virtuálními zařízeními za posledních 7 dní, což je výchozí časové období. Pomocí možnosti **Upravit** v pravém horním rohu grafu vyberte jiné časové měřítko.
* Dlaždice **zařízení** poskytuje souhrn počtu virtuálních polí ve StorSimple Správce zařízení seskupených podle stavu zařízení. Kliknutím na tuto dlaždici otevřete okno seznam **zařízení** a potom klikněte na jednotlivá zařízení, abyste přešli na souhrn zařízení, který je specifický pro dané zařízení. Akce specifické pro zařízení můžete také provádět z daného okna s přehledem zařízení. Pokud chcete získat další informace o okně Souhrn zařízení, klikněte na [okno Souhrn zařízení](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Zobrazení protokolů aktivit
Pokud chcete zobrazit různé operace provedené v rámci StorSimple Správce zařízení, klikněte na odkaz **protokoly aktivit** na levé straně okna s přehledem služby StorSimple. Tím přejdete do okna **protokoly aktivit** , kde můžete zobrazit souhrn posledních operací, které se provedly.

![Protokoly aktivit](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [pomocí místního webového uživatelského rozhraní spravovat virtuální pole StorSimple](storsimple-ova-web-ui-admin.md).

