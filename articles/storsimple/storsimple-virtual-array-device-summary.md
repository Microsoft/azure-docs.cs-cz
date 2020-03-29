---
title: Souhrnný list zařízení StorSimple Virtual Array | Dokumenty společnosti Microsoft
description: Popisuje okno souhrnu zařízení pro Správce zařízení StorSimple a vysvětluje, jak jej použít ke sledování stavu vašeho virtuálního pole StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61408476"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Použití souhrnného okna zařízení pro Správce zařízení StorSimple připojeného k virtuálnímu poli StorSimple

## <a name="overview"></a>Přehled

Okno Zařízení Správce zařízení StorSimple poskytuje souhrnné zobrazení virtuálního pole StorSimple, které je registrováno v daném Správci zařízení StorSimple, a upozorňuje na problémy se zařízením, které vyžadují pozornost správce systému. Tento kurz představuje okno souhrnu zařízení, vysvětluje obsah a funkce a popisuje úkoly, které můžete provádět z tohoto okna.

V souhrnném okně zařízení se zobrazí následující informace:

![Řídicí panel zařízení](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Správa

V okně zařízení StorSimple se zobrazí možnosti správy zařízení StorSimple. Zobrazí se příkazy pro správu v horní části okna a na levé straně. Pomocí těchto možností můžete přidat sdílené složky nebo svazky nebo aktualizovat virtuální pole nebo je převést přes převzetí služeb při selhání.

Oblast essentials zachycuje některé důležité vlastnosti, jako je stav, model, verze softwaru a také odkaz na **webové uživatelské rozhraní** pole. Pokud jste v interní síti, můžete přímo spustit [místní webové uživatelské prostředí](storsimple-ova-web-ui-admin.md) pro správu virtuálního pole.

![Základní informace o zařízení](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>StorSimple shrnutí zařízení

* Dlaždice **Výstrahy** poskytuje snímek všech aktivních výstrah pro vaše virtuální pole seskupené podle závažnosti výstrahy. Kliknutím na dlaždici otevřete okno **Výstrahy** a kliknutím na jednotlivou výstrahu zobrazíte další podrobnosti o této výstraze, včetně doporučených akcí. Výstrahu můžete také vymazat, pokud byl problém vyřešen.

* **Dlaždice Kapacita** zobrazuje primární úložiště, které je zřízeno a zůstává napříč virtuálním zařízením vzhledem k celkovému dostupnému úložišti pro stejné. **Zřízené** odkazuje na množství úložiště, které je připravena a přidělena pro použití, **Zbývající** odkazuje na zbývající kapacitu, která může být zřízena v rámci tohoto zařízení. **Zbývající vrstvené kapacity** je dostupná kapacita, která může být zřízena, včetně cloudu, zatímco **zbývající místní** je kapacita zbývající na discích připojených k tomuto virtuálnímu poli.

* V grafu **Využití** můžete zobrazit primární úložiště používané ve virtuálním poli, stejně jako cloudové úložiště spotřebované za posledních 7 dní, což je výchozí časové období. Pomocí volby **Upravit** v pravém horním rohu grafu zvolte jiné časové měřítko.

* Dlaždice **Sdílené složky** nebo **svazky** obsahuje souhrn počtu sdílených složek nebo svazků v zařízení seskupených podle stavu. Kliknutím na dlaždici otevřete okno seznamu **Sdílené složky** nebo **svazky** a kliknutím na jednotlivou sdílenou složku nebo svazek zobrazte nebo upravte jeho vlastnosti. Další informace naleznete v tématu [Správa sdílených složek](storsimple-virtual-array-manage-shares.md) nebo [správa svazků](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Další kroky
Naučte se:
- [Správa sdílených složek ve virtuálním poli StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Správa svazků ve virtuálním poli StorSimple](storsimple-virtual-array-manage-volumes.md)

