---
title: Okno s přehledem zařízení StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje okně s přehledem zařízení pro správce zařízení StorSimple a vysvětluje, jak použít ho k monitorování stavu StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 9edc0b552f5c2f38e646bc4b44dd8df5c16b0457
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61408476"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Použití okně s přehledem zařízení pro správce zařízení StorSimple připojené k StorSimple Virtual Array

## <a name="overview"></a>Přehled

Zařízení okna Správce zařízení StorSimple poskytuje přehled o StorSimple Virtual Array, který je registrován s danou StorSimple Device Manager, zvýraznění problémy zařízení, které vyžadují pozornost. správce systému. Tento kurz představuje okně s přehledem zařízení, vysvětluje obsah a funkce a popisuje úlohy, které můžete provádět z tohoto okna.

Okně s přehledem zařízení zobrazí následující informace:

![Řídicí panel zařízení](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Správa

V okně zařízení StorSimple zobrazí se možnosti pro správu zařízení StorSimple. Příkazy pro správu se zobrazí v horní části okna a na levé straně. Pomocí těchto možností můžete přidat sdílené složky nebo svazky, aktualizovat nebo převzetí služeb při selhání vaše virtuální pole.

Oblasti essentials pojednává o některých důležité vlastnosti, například stav, modelu, verzi softwaru, jakož i odkaz **webového uživatelského rozhraní** pole. Pokud jste v interní síti, můžete přímo spustit [místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md) spravovat vaše virtuální pole.

![Základy zařízení](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Přehledu zařízení StorSimple

* **Výstrahy** dlaždice poskytuje snímek všechny aktivní výstrahy pro vaše virtuální pole, seskupené podle závažnosti výstrahy. Kliknutím na dlaždici otevřít **výstrahy** okno a potom klikněte na jednotlivé výstrahy zobrazíte další podrobnosti o této výstraze, včetně všech doporučené akce. Výstrahy můžete také zrušit, pokud byl problém vyřešen.

* **Kapacity** dlaždici se zobrazuje primární úložiště, která je zřízená a zbývající virtuální zařízení vzhledem k celkový úložiště k dispozici pro stejné. **Zřízení** odkazuje na velikost úložiště, který je připravený a přidělené, **zbývající** odkazuje na zbývající kapacitu, kterou je možné zřídit přes toto zařízení. **Zbývající Vrstvená** kapacita je k dispozici kapacita, která se dá zřídit včetně cloudu, zatímco **zbývající místní** je na disky připojené k této virtuální pole zbývající kapacity.

* V **využití** grafu, můžete zobrazit primární úložiště používat v rámci celého vaše virtuální pole, stejně jako cloudové úložiště spotřebované za posledních 7 dnů, výchozí časové období. Použití **upravit** možnost v pravém horním rohu grafu vyberte jiné časové měřítko.

* **Sdílené složky** nebo **svazky** dlaždice obsahuje souhrn počtu sdílených složek nebo svazků v zařízení seskupených podle stavu. Kliknutím na dlaždici otevřít **sdílené složky** nebo **svazky** seznamu okně a klikněte na jednotlivé sdílené složky nebo svazku lze zobrazit nebo upravit její vlastnosti. Další informace najdete v tématu Jak [spravovat sdílené složky](storsimple-virtual-array-manage-shares.md) nebo [Správa svazků](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Další postup
Naučte se:
- [Správa sdílených složek na StorSimple Virtual Array](storsimple-virtual-array-manage-shares.md)
    
- [Správa svazků na StorSimple Virtual Array](storsimple-virtual-array-manage-volumes.md)

