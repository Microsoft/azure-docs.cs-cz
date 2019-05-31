---
title: Zpráva k vydání verze Microsoft Azure StorSimple Virtual Array aktualizace 1.2 | Dokumentace Microsoftu
description: Popisuje důležité otevřených problémů a jejich řešení pro StorSimple Virtual Array spuštění aktualizace 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420600"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Poznámky k verzi StorSimple Virtual Array aktualizace 1.2

Následující zpráva k vydání verze Identifikujte kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple Virtual Array aktualizace.

Zpráva k vydání verze se průběžně aktualizují. Při zjištění zásadních problémů vyžadujících alternativní řešení, se přidají. Před nasazením StorSimple Virtual Array, pečlivě si prostudujte informace obsažené v poznámkách k verzi.

Aktualizace 1.2 odpovídá verzi softwaru **10.0.10311.0**.

> [!IMPORTANT]
> - Aktualizace se jedná o narušující a restartujte zařízení. Pokud probíhají vstupně-výstupních operací, zařízení způsobí výpadek. Podrobné pokyny pro balíčky používané k instalaci této aktualizace, přejděte na [stáhnout aktualizace 1.2](#download-update-12).
> - Aktualizace 1.2 je prostřednictvím portálu Azure portal k dispozici pouze v případě, že vaše zařízení se systémem Update 1.0 nebo 1.1.

## <a name="whats-new-in-update-12"></a>Co je nového v aktualizaci 1.2

Tato aktualizace obsahuje následující opravy:

- Lepší odolnost proti chybám při zpracování odstraněných souborů.
- Vylepšené zpracování výjimek v cestě spuštění kódu, což vede k selhání zálohování, obnovení, cloudových čtení nižší a automatizované recyklace místa.

## <a name="download-update-12"></a>Stažení aktualizace 1.2

Stáhněte si tuto aktualizaci, přejděte [katalogu služby Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) serveru a balíček ke stažení KB4502035. Tento balíček obsahuje následující balíčky:

 - **KB4493446** , který obsahuje kumulativní aktualizace Windows 2012 R2 až. dubna 2019. Další informace o co je zahrnuté v této kumulativní, přejděte na [měsíční kumulativní dubna](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** Microsoft Update samostatného balíčku souboru WindowsTH-KB3011067 x64. Tento soubor slouží k aktualizaci softwaru zařízení.

Stáhnout KB4502035 a postupujte podle těchto pokynů a [aktualizace prostřednictvím místního webového uživatelského rozhraní](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Chyby opravené v aktualizace 1.2

Následující tabulka obsahuje souhrn chyby opravené v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Odstranění| V předchozích verzích softwaru došlo k problému při použití zařízení nezměnili i v případě, že soubory se odstranily. Tento problém je vyřešen v této verzi. Vrstvení cesta kódu byla provedena odolnější, při zpracování odstraněných souborů.|
| 2 |Zpracování výjimek| V předchozích verzích softwaru došlo k nějakému problému po restartování systému, který může potenciálně vést k selhání v zálohování, obnovení, čtení z cloudu a automatizované recyklace místa. Tato verze obsahuje změny byly zpracování výjimek v cestě po spuštění.|

## <a name="known-issues-in-update-12"></a>Známé problémy v aktualizace 1.2

Žádné nové problémy nebyly uvedené verze 1.2 aktualizace. Všechny verze uvedené problémy, se přenesou z předchozích verzí. Chcete-li zobrazit souhrn známých problémů, které jsou zahrnuty z předchozích verzí, přejděte na [známé problémy v aktualizaci 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Další postup

Stáhněte si KB4502035 a [aktualizace prostřednictvím místního webového uživatelského rozhraní](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Odkazy

Hledáte starší poznámku k verzi? Přejít na:
* [Poznámky k verzi 1.1 StorSimple Virtual Array Update](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1.0 poznámky](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0,6 poznámky](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 poznámky](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array aktualizace 0.4 poznámky](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 poznámky](storsimple-ova-update-03-release-notes.md)
* [Poznámky k verzi StorSimple Virtual Array Update 0.1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array obecné dostupnosti zpráva k vydání verze](storsimple-ova-pp-release-notes.md)
