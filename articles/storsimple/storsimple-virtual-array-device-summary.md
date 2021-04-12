---
title: Okno souhrnu zařízení StorSimple Virtual Array | Microsoft Docs
description: Popisuje okno Souhrn zařízení StorSimple Správce zařízení a vysvětluje, jak ho používat k monitorování stavu virtuálních polí StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 1ab72788c768568366f2627055015c74028330b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005873"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Pro StorSimple Správce zařízení připojená k virtuálnímu poli StorSimple použít okno Souhrn zařízení

## <a name="overview"></a>Přehled

Okno StorSimple Správce zařízení zařízení nabízí souhrnný pohled na virtuální pole StorSimple, které je zaregistrované s daným StorSimple Správce zařízení, což zvýrazní problémy zařízení, které potřebují pozornost správce systému. V tomto kurzu se seznámíte s oknem souhrn zařízení, vysvětlujete obsah a funkci a popisujete úlohy, které můžete z tohoto okna provádět.

Okno Souhrn zařízení zobrazuje následující informace:

![Řídicí panel zařízení](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Správa

V okně zařízení StorSimple se zobrazí možnosti správy zařízení StorSimple. Zobrazí se příkazy pro správu v horní části okna a na levé straně. Tyto možnosti použijte k přidání sdílených složek nebo svazků nebo k aktualizaci nebo převzetí služeb při selhání ve virtuálním poli.

Oblast základy zachycuje některé důležité vlastnosti, jako je například stav, model, verze softwaru a odkaz na **webové uživatelské rozhraní** pole. Pokud pracujete v interní síti, můžete přímo spustit [místní webové uživatelské rozhraní](storsimple-ova-web-ui-admin.md) , abyste mohli spravovat své virtuální pole.

![Základy zařízení](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Souhrn zařízení StorSimple

* Dlaždice **výstrahy** poskytuje snímek všech aktivních výstrah pro virtuální pole seskupených podle závažnosti výstrahy. Kliknutím na dlaždici otevřete okno **výstrahy** a potom klikněte na jednotlivá upozornění. zobrazí se další podrobnosti o této výstraze, včetně všech doporučených akcí. Výstrahu můžete také vymazat, pokud byl problém vyřešen.

* Na dlaždici **kapacita** se zobrazuje primární úložiště, které je zřízené a zbývající ve virtuálním zařízení vzhledem k celkovému počtu dostupných úložišť. **Zřízené** v tomto případě odkazuje na velikost úložiště, které je připraveno a přiděleno k použití, **zbývající** znamená zbývající kapacitu, kterou lze zřídit v rámci tohoto zařízení. **Zbývající vrstvená** kapacita je dostupná kapacita, kterou je možné zřídit včetně cloudu, zatímco **zbývající místní** kapacita je zbývající na discích připojených k tomuto virtuálnímu poli.

* V grafu **využití** můžete zobrazit primární úložiště používané ve vašem virtuálním poli a také cloudové úložiště spotřebované za posledních 7 dní, což je výchozí časové období. Pomocí možnosti **Upravit** v pravém horním rohu grafu vyberte jiné časové měřítko.

* Dlaždice **sdílené složky** nebo **svazky** obsahuje souhrn počtu sdílených složek nebo svazků v zařízení seskupených podle stavu. Kliknutím na dlaždici otevřete okno seznam **sdílených složek**  nebo **svazků** a potom kliknutím na jednotlivou sdílenou složku nebo svazek můžete zobrazit nebo změnit její vlastnosti. Další informace najdete v tématu [Správa sdílených složek](storsimple-virtual-array-manage-shares.md) nebo [Správa svazků](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Další kroky
Naučte se:
- [Správa sdílených složek ve virtuálním poli StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Správa svazků ve virtuálním poli StorSimple](storsimple-virtual-array-manage-volumes.md)

