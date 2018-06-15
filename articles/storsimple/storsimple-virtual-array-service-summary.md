---
title: Souhrn okno služby pole virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje souhrnu okna služby ve Správci zařízení StorSimple a vysvětluje, jak použít jej k monitorování stavu pole virtuální zařízení StorSimple.
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
ms.openlocfilehash: 284e404c44505a98d9e0ed5abe87cd945415af56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875736"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Použití okna Souhrn služby ve Správci zařízení StorSimple připojené k poli virtuální zařízení StorSimple
## <a name="overview"></a>Přehled
Souhrn okně služby pro správce zařízení StorSimple poskytuje přehled o StorSimple virtuální pole (také označované jako StorSimple místní virtuální zařízení, nebo virtuální zařízení), které jsou připojené k vaší službě zvýraznění ty, které je třeba systém Správce pozornost. Tento kurz představuje souhrn okno služby, popisuje obsah a funkce a popisuje úlohy, které můžete provádět v tomto okně.

![Řídicí panel služby](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Příkazy pro správu a essentials
V okně Souhrn StorSimple zobrazí možnosti pro správu služby StorSimple Manager zařízení a také virtuální pole registrované k této službě. Příkazy pro správu zobrazí v horní části okna a na levé straně.

Pomocí těchto možností můžete provádět různé operace, například jako přidat sdílené složky nebo svazky nebo monitorování různé úlohy spuštěné v poli virtuální.

Oblasti essentials zaznamená některé důležité vlastnosti například skupinu prostředků, umístění a předplatné, ve kterém byla vytvořena vaše zařízení StorSimple Manager.

## <a name="storsimple-device-manager-service-summary"></a>Souhrn služby StorSimple Manager zařízení
* **Výstrahy** dlaždice poskytuje snímek všechny aktivní výstrahy v rámci všechna virtuální zařízení, seskupené podle závažnosti výstrah. Kliknutím na dlaždici otevře **výstrahy** okno, kde můžete kliknout na jednotlivé výstrahy zobrazíte další podrobnosti o výstraze, včetně všech doporučené akce. Pokud byl problém vyřešen, zrušte výstrahy.
* **Kapacity** zobrazí dlaždice zobrazuje primární úložiště, které je zřízený a zbývající napříč všechna virtuální zařízení relativně k celkové úložiště k dispozici na všech virtuálních zařízeních. **Zřízení** odkazuje na velikost úložiště, která je připravená a přidělení k použití, **zbývající** odkazuje na zbývající kapacitu, kterou může být zřízen napříč všechna virtuální zařízení. **Zbývající vrstvené** kapacita dostupnou kapacitu, může být zřízen včetně cloudu, je při **zbývající místní** je kapacita zbývající u disků připojených k virtuální pole.
* V **využití** grafu, zobrazí se příslušné metriky pro virtuální zařízení. Můžete zobrazit primárního úložiště používané v rámci celé všechna virtuální zařízení, stejně jako cloudového úložiště využívat virtuální zařízení za posledních 7 dnů, výchozí časové období. Použití **upravit** možnost v pravém horním rohu grafu vyberte jiné časové rozmezí.
* **Zařízení** dlaždice obsahuje souhrn počtu virtuální pole ve vaší StorSimple Správci zařízení seskupené podle stavu zařízení. Klikněte na tuto dlaždici otevřete **zařízení** okno a potom klikněte na k jednotlivým zařízením přejdete do souhrn mobilního zařízení, která je specifická pro zařízení. Můžete také provést konkrétní akce zařízení ze souhrnné okno daného zařízení. Další informace o souhrnu okna zařízení, přejděte na [souhrnu okna zařízení](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Zobrazit protokoly aktivity
Chcete-li zobrazit různé operace, které provádějí v rámci vašeho správce zařízení StorSimple, klikněte na tlačítko **protokoly aktivity** odkaz na levé straně vaší okna Souhrn služby StorSimple. Tím přejdete **protokoly aktivity** okno, kde se zobrazí souhrn posledních prováděných operací.

![Protokoly aktivit](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Další kroky
Zjistěte, jak [používat místní webového uživatelského rozhraní pro správu pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).

