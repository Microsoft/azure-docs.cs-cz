---
title: Limity systému řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje systémové limity a doporučené velikosti pro součásti a připojení řady StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70f2d9542082ddf7ecf1d1e7361b0ecdb14c5ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68963373"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Jaké jsou limity systému řady StorSimple 8000?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled

StorSimple poskytuje škálovatelné a flexibilní úložiště pro vaše datové centrum. Existují však některá omezení, která byste měli mít na paměti při plánování, nasazování a provozu řešení StorSimple. Následující tabulka popisuje tato omezení a poskytuje některá doporučení, abyste mohli maximálně využít řešení StorSimple.

| Identifikátor omezení | Omezení | Komentáře |
| --- | --- | --- |
| Maximální počet přihlašovacích údajů účtu úložiště |64 | |
| Maximální počet objemových kontejnerů |64 | |
| Maximální počet svazků |255 | |
| Maximální počet místně vázaných svazků |32 | |
| Maximální počet plánů na šablonu šířky pásma |168 |Rozvrh pro každou hodinu, každý den v týdnu (24 * 7). |
| Maximální velikost vrstveného svazku na fyzických zařízeních |64 TB pro 8100 a 8600 |8100 a 8600 jsou fyzická zařízení. |
| Maximální velikost vrstveného svazku na virtuálních zařízeních v Azure |30 TB za 8010 <br></br> 64 TB pro 8020 |8010 a 8020 jsou virtuální zařízení v Azure, která používají standard storage a úložiště Premium. |
| Maximální velikost místně vázaných svazků na fyzických zařízeních |8,5 TB pro 8100 <br></br> 22,5 TB pro 8600 |8100 a 8600 jsou fyzická zařízení. |
| Maximální počet připojení iSCSI |512 | |
| Maximální počet připojení iSCSI od iniciátorů |512 | |
| Maximální počet záznamů řízení přístupu na zařízení |64 | |
| Maximální počet svazků na zásady zálohování |20 | |
| Maximální počet záloh uchovávaných podle plánu (v zásadách zálohování) |64 | |
| Maximální počet plánů na zásady zálohování |10 | |
| Maximální počet snímků libovolného typu, které lze zachovat na svazek |256 |Toto číslo zahrnuje místní snímky a cloudové snímky. |
| Maximální počet snímků, které mohou být přítomny v libovolném zařízení |10 000 | |
| Maximální počet svazků, které mohou být zpracovány paralelně pro zálohování, obnovení nebo klonování |16 |<ul><li>Pokud existuje více než 16 svazků, jsou zpracovány postupně jako zpracování slotů k dispozici.</li><li>Nové zálohy klonovaného nebo obnoveného vrstveného svazku nemohou nastat, dokud nebude operace dokončena. Pro místní svazek jsou však po povoleny zálohy po online svazku.</li></ul> |
| Obnovení a obnovení času obnovení pro vrstvené svazky |< 2 minuty |<ul><li>Svazek je k dispozici do 2 minut od obnovení nebo operace klonování bez ohledu na velikost svazku.</li><li>Výkon svazku může být zpočátku pomalejší než obvykle, protože většina dat a metadat je stále umístěna v cloudu. Výkon se může zvýšit s toky dat z cloudu do zařízení StorSimple.</li><li>Celková doba stahování metadat závisí na přidělené velikosti svazku. Metadata se automaticky přenese do zařízení na pozadí rychlostí 5 minut na TB přidělených dat svazku. Tato rychlost může být ovlivněna šířkou pásma Internetu do cloudu.</li><li>Operace obnovení nebo klonování je dokončena, když jsou v zařízení všechna metadata.</li><li>Operace zálohování nelze provést, dokud není operace obnovení nebo klonování zcela dokončena. |
| Obnovení doby obnovení místně vázaných svazků |< 2 minuty |<ul><li>Svazek je k dispozici do 2 minut od operace obnovení, bez ohledu na velikost svazku.</li><li>Výkon svazku může být zpočátku pomalejší než obvykle, protože většina dat a metadat je stále umístěna v cloudu. Výkon se může zvýšit s toky dat z cloudu do zařízení StorSimple.</li><li>Celková doba stahování metadat závisí na přidělené velikosti svazku. Metadata se automaticky přenese do zařízení na pozadí rychlostí 5 minut na TB přidělených dat svazku. Tato rychlost může být ovlivněna šířkou pásma Internetu do cloudu.</li><li>Na rozdíl od vrstvených svazků se u místně vázaných svazků data svazku stahují také místně v zařízení. Operace obnovení je dokončena, jakmile byla do zařízení přenesena všechna data svazku.</li><li>Operace obnovení může být dlouhá. Celková doba dokončení obnovení závisí na velikosti zřízeného místního svazku, šířce pásma Internetu a existujících datech v zařízení. Operace zálohování na místně vázaný svazek jsou povoleny, zatímco operace obnovení probíhá. |
| Rychlost zpracování pro snímky cloudu |15 minut/TB |<ul><li>Minimální doba, aby byl snímek cloudu připraven k nahrání, na TB přidělených dat svazku v záloze. </li><li> Celkový čas snímku cloudu se vypočítá přidáním této doby k času nahrávání snímku, který je ovlivněn šířkou pásma internetu do cloudu. |
| Maximální propustnost klienta pro čtení a zápis (při obsluhě z vrstvy SSD)* |920/720 MB/s s jedním síťovým rozhraním 10 GbE |Až 2x s MPIO a dvěma síťovými rozhraními. |
| Maximální propustnost klienta pro čtení a zápis (při obsluhy z úrovně HDD)* |120/250 MB/s | |
| Maximální propustnost klienta pro čtení a zápis (při obsluhy z cloudové vrstvy)* pro aktualizaci 3 a novější** |40/60 MB/s pro vrstvené svazky<br><br>60/80 MB/s pro vrstvené svazky s možností archivace vybranou při vytváření svazku |Propustnost čtení závisí na klientech, kteří generují a udržují dostatečnou hloubku fronty vstupně-vstupně-in. <br><br>Dosažená rychlost závisí na rychlosti použitého podkladového účtu úložiště. |

&#42; Maximální propustnost na typ vstupně-v.a. byla měřena se 100 % scénáři čtení a 100 % zápisu. Skutečná propustnost může být nižší a závisí na kombinaci vstupně-va a podmínek sítě.

&#42;&#42; Výkon čísla před aktualizací 3 může být nižší.

## <a name="next-steps"></a>Další kroky
Projděte si [požadavky na systém StorSimple](storsimple-8000-system-requirements.md).

