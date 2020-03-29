---
title: Souhrnný list služby StorSimple Virtual Array | Dokumenty společnosti Microsoft
description: Popisuje okno souhrnu služby pro Správce zařízení StorSimple a vysvětluje, jak jej použít ke sledování stavu virtuálního pole StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64720714"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Použití okna souhrnu služby pro Správce zařízení StorSimple připojeného k virtuálnímu poli StorSimple
## <a name="overview"></a>Přehled
Okno souhrnu služby pro Správce zařízení StorSimple poskytuje souhrnné zobrazení virtuálních polí StorSimple (označovaných také jako místní virtuální zařízení StorSimple nebo virtuální zařízení), která jsou připojena k vaší službě, a zvýrazňuje ty, které potřebují systém pozornost správce. Tento kurz představuje okno souhrnu služby, vysvětluje obsah a funkce a popisuje úkoly, které můžete provádět z tohoto okna.

![Řídicí panel služby](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Řídicí příkazy a základní informace
V souhrnném okně StorSimple se zobrazí možnosti správy služby StorSimple Device Manager a také virtuální pole registrovaná k této službě. Zobrazí se příkazy pro správu v horní části okna a na levé straně.

Tyto možnosti slouží k provádění různých operací, jako je například přidání sdílených složek nebo svazků, nebo ke sledování různých úloh spuštěných ve virtuálních polích.

Oblast essentials zachycuje některé důležité vlastnosti, jako je například skupina prostředků, umístění a předplatné, ve kterém byl vytvořen Správce zařízení StorSimple.

## <a name="storsimple-device-manager-service-summary"></a>Souhrn služby Správce zařízení StorSimple
* Dlaždice **Výstrahy** poskytuje snímek všech aktivních výstrah napříč všemi virtuálními zařízeními seskupenými podle závažnosti výstrahy. Kliknutím na dlaždici se otevře okno **Upozornění,** kde můžete kliknutím na jednotlivé výstrahy zobrazit další podrobnosti o této výstraze, včetně všech doporučených akcí. Výstrahu můžete také vymazat, pokud byl problém vyřešen.
* Dlaždice **Kapacita** zobrazuje primární úložiště, které je zřízené a zbývající na všech virtuálních zařízeních vzhledem k celkovému úložišti, které je k dispozici na všech virtuálních zařízeních. **Zřízené** odkazuje na množství úložiště, které je připravena a přidělena pro použití, **Zbývající** odkazuje na zbývající kapacitu, která může být zřízena na všech virtuálních zařízeních. **Zbývající vrstvené kapacity** je dostupná kapacita, která může být zřízena, včetně cloudu, zatímco **zbývající místní** je kapacita zbývající na discích připojených k virtuálním polím.
* V grafu **Využití** uvidíte relevantní metriky pro vaše virtuální zařízení. Můžete zobrazit primární úložiště používané na všech virtuálních zařízeních, stejně jako cloudové úložiště spotřebované virtuálními zařízeními za posledních 7 dní, což je výchozí časové období. Pomocí volby **Upravit** v pravém horním rohu grafu zvolte jiné časové měřítko.
* Dlaždice **Zařízení** obsahuje souhrn počtu virtuálních polí ve Správci zařízení StorSimple seskupených podle stavu zařízení. Kliknutím na tuto dlaždici otevřete okno seznamu **Zařízení** a potom kliknutím na jednotlivé zařízení přejdete k podrobnostem souhrnu zařízení specifického pro dané zařízení. Můžete také provádět akce specifické pro zařízení z daného okna souhrnu zařízení. Další informace o souhrnném noži zařízení naleznete v [části Okno souhrnu zařízení](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Zobrazit protokoly aktivit
Chcete-li zobrazit různé operace prováděné ve Správci zařízení StorSimple, klepněte na odkaz **Protokoly aktivit** na levé straně okna souhrnu služby StorSimple. Tím přejdete do okna **protokoly aktivit,** kde můžete zobrazit souhrn posledních provedených operací.

![Protokoly aktivit](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [používat místní webové uživatelské prostředí ke správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

