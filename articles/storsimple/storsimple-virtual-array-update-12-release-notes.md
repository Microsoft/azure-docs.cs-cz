---
title: Zpráva k vydání verze Microsoft Azure StorSimple Virtual Array Update 1,2 | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple s aktualizací 1,2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960185"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Zpráva k vydání verze pro StorSimple Virtual Array Update 1,2

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple aktualizace virtuálních polí.

Poznámky k verzi se průběžně aktualizují. Při zjištění zásadních problémů, které vyžadují řešení, sem přidáme dostupné informace. Před nasazením StorSimple virtuálního pole pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 1,2 odpovídá **10.0.10311.0** verze softwaru.

> [!IMPORTANT]
> - Aktualizace jsou rušivé a restartují vaše zařízení. Pokud probíhá vstupně-výstupní operace, dojde k výpadku zařízení. Podrobné pokyny k balíčkům, které se používají k instalaci této aktualizace, najdete v článku [Stažení aktualizace 1,2](#download-update-12).
> - Aktualizace 1,2 je k dispozici prostřednictvím Azure Portal jenom v případě, že je na zařízení spuštěná aktualizace 1,0 nebo 1,1.

## <a name="whats-new-in-update-12"></a>Co je nového v aktualizaci 1,2

Tato aktualizace obsahuje následující opravy chyb:

- Vylepšená odolnost při zpracovávání odstraněných souborů.
- Vylepšené zpracování výjimek ve spouštěcí cestě kódu, což vede k menším chybám při zálohování, obnovení, čtení cloudu a automatizovanému opětovnému získávání místa.

## <a name="download-update-12"></a>Stažení aktualizace 1,2

Pokud chcete tuto aktualizaci stáhnout, navštivte server [katalogu Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) a Stáhněte si balíček KB4502035. Tento balíček obsahuje následující balíčky:

 - **KB4493446** , která obsahuje kumulativní aktualizace Windows pro 2012 R2 až do dubna 2019. Další informace o tom, co je zahrnuté v této kumulativní aktualizaci, najdete v [měsíci kumulativních zabezpečení v dubnu](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** , což je soubor samostatného balíčku Microsoft Update WINDOWSTH-KB3011067-x64. Tento soubor slouží k aktualizaci softwaru zařízení.

Stáhněte si KB4502035 a postupujte podle těchto pokynů, abyste [aktualizaci použili prostřednictvím místního webového uživatelského rozhraní](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problémy opravené v aktualizaci 1,2

Následující tabulka poskytuje souhrn chyb opravených v této verzi.

| No. | Funkce | Problém |
| --- | --- | --- |
| 1 |Odstranění| V předchozích verzích softwaru došlo k potížím, pokud se použití zařízení nezměnilo ani když byly soubory smazány. Tento problém je opravený v této verzi. Při zpracování odstraněných souborů byla cesta k vrstvení kódu pružnější.|
| 2 |Ošetření výjimek| V předchozích verzích softwaru došlo k potížím po restartování systému, které by mohlo vést k chybám při zálohování, obnovení, čtení z cloudu a automatizovanému opakovanému získávání místa. Tato verze obsahuje změny, jak byly výjimky zpracovány v cestě při spuštění.|

## <a name="known-issues-in-update-12"></a>Známé problémy v aktualizaci 1,2

V aktualizaci 1,2 se neuvedly žádné nové problémy. Všechny problémy zaznamenané vydáním se přenesou z předchozích verzí. Pokud chcete zobrazit souhrn známých problémů zahrnutých z předchozích verzí, přejděte na [známé problémy v aktualizaci 1,1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Další kroky

Stáhněte si KB4502035 a [použijte aktualizaci prostřednictvím místního webového uživatelského rozhraní](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Reference

Hledáte starší poznámku k verzi? Přejít na:
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 1,1](storsimple-virtual-array-update-11-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 1,0](storsimple-virtual-array-update-1-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,6](storsimple-virtual-array-update-06-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,5](storsimple-virtual-array-update-05-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,4](storsimple-virtual-array-update-04-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,3](storsimple-ova-update-03-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,1 a 0,2](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi pro StorSimple Virtual Array General Availability](./storsimple-virtual-array-update-06-release-notes.md)