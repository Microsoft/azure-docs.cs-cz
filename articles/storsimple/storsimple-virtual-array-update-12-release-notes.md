---
title: Poznámky k verzi 1.2 aktualizace virtuálního pole Microsoft Azure StorSimple| Dokumenty společnosti Microsoft
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple se spuštěnou aktualizací 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66420600"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Poznámky k verzi 1.2 aktualizace virtuálního pole StorSimple

Následující poznámky k verzi identifikují důležité problémy s otevřením a vyřešené problémy pro aktualizace virtuálního pole Microsoft Azure StorSimple.

Poznámky k verzi jsou průběžně aktualizovány. Při zjištění zásadních problémů, které vyžadují řešení, sem přidáme dostupné informace. Před nasazením virtuálního pole StorSimple pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 1.2 odpovídá verzi softwaru **10.0.10311.0**.

> [!IMPORTANT]
> - Aktualizace jsou rušivé a restartujte zařízení. Pokud vstupně-in/o probíhá, zařízení dojde k prostojům. Podrobné pokyny k balíčkům použitým k instalaci této aktualizace naleznete v [článku Stažení aktualizace 1.2](#download-update-12).
> - Aktualizace 1.2 je k dispozici prostřednictvím portálu Azure pouze v případě, že vaše zařízení používá aktualizaci 1.0 nebo 1.1.

## <a name="whats-new-in-update-12"></a>Co je nového v aktualizaci 1.2

Tato aktualizace obsahuje následující opravy chyb:

- Vylepšená odolnost proti chybám při zpracování odstraněných souborů.
- Vylepšené zpracování výjimek v cestě spuštění kódu, což vede ke snížení selhání v zálohování, obnovení, čtení v cloudu a automatické rekultivaci místa.

## <a name="download-update-12"></a>Stáhnout aktualizaci 1.2

Chcete-li stáhnout tuto aktualizaci, přejděte na server [katalogu microsoft update](https://www.catalog.update.microsoft.com/Home.aspx) a stáhněte balíček KB4502035. Tento balíček obsahuje následující balíčky:

 - **KB4493446,** který obsahuje kumulativní aktualizace systému Windows pro 2012 R2 až do dubna 2019. Další informace o tom, co je součástí této kumulativní, naleznete v [dubnu měsíční kumulativní zabezpečení](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067,** což je soubor samostatného balíčku Microsoft Update WindowsTH-KB3011067-x64. Tento soubor se používá k aktualizaci softwaru zařízení.

Stáhněte si KB4502035 a postupujte podle těchto pokynů [použít aktualizaci prostřednictvím místního webového uživatelského rozhraní](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problémy opravené v aktualizaci 1.2

Následující tabulka obsahuje souhrn problémů opravených v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Odstranění| V předchozích verzích softwaru došlo k problému, kdy se použití zařízení nezměnilo, ani když byly soubory odstraněny. Tento problém je vyřešen v této verzi. Cesta kódu vrstvení byla při zpracování odstraněných souborů odolnější.|
| 2 |Zpracování výjimek| V předchozích verzích softwaru došlo k problému po restartování systému, který by mohl potenciálně vést k selhání záloh, obnovení, čtení z cloudu a automatizované rekultivaci prostoru. Tato verze obsahuje změny o tom, jak byly výjimky zpracovány v cestě po spuštění.|

## <a name="known-issues-in-update-12"></a>Známé problémy v aktualizaci 1.2

V aktualizaci 1.2 nebyly zaznamenány žádné nové problémy. Všechny problémy s poznámkou k vydání jsou přeneseny z předchozích verzí. Chcete-li zobrazit souhrn známých problémů zahrnutých z předchozích verzí, přejděte na [stránku Známé problémy v aktualizaci 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Další kroky

Stáhnout KB4502035 a [použít aktualizaci prostřednictvím místního webového uživatelského rozhraní](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Odkazy

Hledáte starší poznámku k vydání? Přejít na:
* [Poznámky k verzi 1.1 aktualizace virtuálního pole StorSimple](storsimple-virtual-array-update-11-release-notes.md)
* [Poznámky k verzi 1.0 aktualizace virtuálního pole StorSimple](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 Poznámky k verzi](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Poznámky k verzi](storsimple-virtual-array-update-05-release-notes.md)
* [Aktualizace virtuálního pole StorSimple 0.4 Poznámky k verzi](storsimple-virtual-array-update-04-release-notes.md)
* [Aktualizace virtuálního pole StorSimple 0.3 Poznámky k verzi](storsimple-ova-update-03-release-notes.md)
* [Aktualizace virtuálního pole StorSimple 0.1 a 0.2 Poznámky k verzi](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi pro všeobecné dostupnosti storSimple Virtual Array](storsimple-ova-pp-release-notes.md)
