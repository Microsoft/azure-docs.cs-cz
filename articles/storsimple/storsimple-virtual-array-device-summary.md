---
title: Souhrn okno zařízení pole virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje souhrnu okna zařízení ve Správci zařízení StorSimple a vysvětluje, jak použít jej k monitorování stavu pole virtuální zařízení StorSimple.
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
ms.openlocfilehash: 35413d597c3b6b1c7600241a78572b63f982d175
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875680"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Použití okna souhrnu zařízení ve Správci zařízení StorSimple připojené k poli virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

V okně zařízení StorSimple Manager zařízení obsahuje souhrnné zobrazení pole virtuální zařízení StorSimple, které je registrované s danou StorSimple Správce zařízení, zvýraznění těchto problémů zařízení, které vyžadují pozornost. správce systému. Tento kurz představuje souhrn okno zařízení, popisuje obsah a funkce a popisuje úlohy, které můžete provádět v tomto okně.

V okně Souhrn zařízení zobrazí následující informace:

![Řídicí panel zařízení](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Správa

V okně zařízení StorSimple zobrazí se možnosti pro správu zařízení StorSimple. Příkazy pro správu zobrazí v horní části okna a na levé straně. Pomocí těchto možností můžete přidat sdílené složky nebo svazky, nebo aktualizovat nebo převzít virtuální pole.

Oblasti essentials zaznamená některé důležité vlastnosti například stav, modelu, verzi softwaru, jakož i odkaz **webového uživatelského rozhraní** pole. Pokud jste v interní síti, můžete přímo spustit [místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md) ke správě virtuálních pole.

![Zařízení essentials](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Souhrn zařízení StorSimple

* **Výstrahy** dlaždice poskytuje snímek všechny aktivní výstrahy pro vaše virtuální pole seskupené podle závažnosti výstrah. Kliknutím na dlaždici otevřít **výstrahy** okna a potom klikněte na jednotlivé výstrahy zobrazíte další podrobnosti o této výstraze, včetně všech doporučené akce. Pokud byl problém vyřešen, zrušte výstrahy.

* **Kapacity** dlaždice zobrazí primární úložiště, které je zřízený a zbývající přes virtuální zařízení relativně k celkové úložiště k dispozici pro stejné. **Zřízení** odkazuje na velikost úložiště, která je připravená a přidělení k použití, **zbývající** odkazuje na zbývající kapacitu, kterou může být zřízen přes toto zařízení. **Zbývající vrstvené** kapacita dostupnou kapacitu, může být zřízen včetně cloudu, je při **zbývající místní** je kapacita zbývající na disky připojené k této virtuální pole.

* V **využití** grafu, můžete zobrazit primárního úložiště používané v rámci celé virtuální pole, jakož i cloudového úložiště spotřebované za posledních 7 dnů, výchozí časové období. Použití **upravit** možnost v pravém horním rohu grafu vyberte jiné časové rozmezí.

* **Sdílené složky** nebo **svazky** dlaždice obsahuje souhrn počtu sdílených složek nebo svazků v zařízení seskupené podle stavu. Kliknutím na dlaždici otevřít **sdílené složky** nebo **svazky** seznamu okno a klikněte na jednotlivé sdílené složky nebo svazku lze zobrazit nebo upravit jeho vlastnosti. Další informace najdete v tématu Jak [spravovat sdílené složky](storsimple-virtual-array-manage-shares.md) nebo [správu svazků](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Další kroky
Naučte se:
- [Spravovat sdílené složky v poli virtuální zařízení StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Správa svazků v poli virtuální zařízení StorSimple](storsimple-virtual-array-manage-volumes.md)

