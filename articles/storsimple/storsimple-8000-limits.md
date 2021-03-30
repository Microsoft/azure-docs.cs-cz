---
title: Omezení systému řady StorSimple 8000 | Microsoft Docs
description: Popisuje omezení systému a Doporučené velikosti pro komponenty a připojení řady StorSimple 8000.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "68963373"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Co jsou limity systému řady StorSimple 8000?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled

StorSimple zajišťuje škálovatelné a flexibilní úložiště pro vaše datové centrum. Existují však určitá omezení, která byste měli mít na paměti při plánování, nasazování a provozu řešení StorSimple. Následující tabulka popisuje tato omezení a poskytuje některá doporučení, abyste mohli využít řešení StorSimple na maximum.

| Identifikátor omezení | Omezení | Komentáře |
| --- | --- | --- |
| Maximální počet přihlašovacích údajů účtu úložiště |64 | |
| Maximální počet kontejnerů svazků |64 | |
| Maximální počet svazků |255 | |
| Maximální počet místně připojených svazků |32 | |
| Maximální počet plánů na šablonu šířky pásma |168 |Plán každé hodiny, každý den v týdnu (24 * 7). |
| Maximální velikost vrstveného svazku na fyzických zařízeních |64 TB pro 8100 a 8600 |8100 a 8600 jsou fyzická zařízení. |
| Maximální velikost vrstveného svazku na virtuálních zařízeních v Azure |30 TB pro 8010 <br></br> 64 TB pro 8020 |8010 a 8020 jsou virtuální zařízení v Azure, která používají standardní úložiště a Premium Storage v uvedeném pořadí. |
| Maximální velikost místně připnutého svazku na fyzických zařízeních |8,5 TB pro 8100 <br></br> 22,5 TB pro 8600 |8100 a 8600 jsou fyzická zařízení. |
| Maximální počet připojení iSCSI |512 | |
| Maximální počet připojení iSCSI od iniciátorů |512 | |
| Maximální počet záznamů řízení přístupu na zařízení |64 | |
| Maximální počet svazků na zásadu zálohování |20 | |
| Maximální počet záloh uchovávaných podle plánu (v zásadách zálohování) |64 | |
| Maximální počet plánů na zásadu zálohování |10 | |
| Maximální počet snímků libovolného typu, které se dají uchovávat na jeden svazek |256 |Toto číslo zahrnuje místní snímky a cloudové snímky. |
| Maximální počet snímků, které mohou být k dispozici na jakémkoli zařízení |10 000 | |
| Maximální počet svazků, které lze paralelně zpracovat pro zálohování, obnovení nebo klonování |16 |<ul><li>Pokud je k dispozici více než 16 svazků, zpracují se postupně, jakmile budou dostupné sloty pro zpracování.</li><li>Nové zálohy klonovaného nebo obnoveného vrstveného svazku nelze provést, dokud nebude operace dokončena. Pro místní svazek se ale můžou po online zálohování povolit zálohy.</li></ul> |
| Obnovení a klonování času obnovení pro vrstvené svazky |< 2 minuty |<ul><li>Svazek je k dispozici do 2 minut operace obnovení nebo klonování, bez ohledu na velikost svazku.</li><li>Výkon svazku může zpočátku pomalejší než normální, protože většina dat a metadata se pořád nacházejí v cloudu. Výkon se může zvýšit jako datový tok z cloudu do zařízení StorSimple.</li><li>Celková doba stahování metadat závisí na velikosti přiděleného svazku. Do zařízení se automaticky přiřadí metadata na pozadí rychlostí 5 minut na TB přidělených dat svazku. Tato rychlost může být ovlivněna internetovou šířkou pásma cloudu.</li><li>Operace obnovení nebo klonování je dokončena, když jsou všechna metadata v zařízení.</li><li>Operace zálohování nelze provést, dokud nebude dokončena úplná operace obnovení nebo klonování. |
| Obnovit čas obnovení pro místně připojené svazky |< 2 minuty |<ul><li>Svazek je k dispozici do 2 minut operace obnovení, bez ohledu na velikost svazku.</li><li>Výkon svazku může zpočátku pomalejší než normální, protože většina dat a metadata se pořád nacházejí v cloudu. Výkon se může zvýšit jako datový tok z cloudu do zařízení StorSimple.</li><li>Celková doba stahování metadat závisí na velikosti přiděleného svazku. Do zařízení se automaticky přiřadí metadata na pozadí rychlostí 5 minut na TB přidělených dat svazku. Tato rychlost může být ovlivněna internetovou šířkou pásma cloudu.</li><li>Na rozdíl od vrstvených svazků se pro místně připnuté svazky data o svazcích stáhnou i místně na zařízení. Operace obnovení je dokončena, když jsou všechna data svazků přenesena do zařízení.</li><li>Operace obnovení můžou být dlouhé. Celková doba k dokončení obnovení závisí na velikosti zřízeného místního svazku, na šířce pásma internetu a na stávajících datech na zařízení. V průběhu operace obnovení jsou povoleny operace zálohování na místně připojeném svazku. |
| Počet zpracování pro cloudové snímky |15 minut/TB |<ul><li>Minimální doba, po kterou je snímek cloudu připravený k nahrání, za TB přidělených dat svazku v záloze. </li><li> Celkový čas snímku v cloudu se vypočítá tak, že se tento čas přičte k času nahrávání snímku, který je ovlivněný internetovou šířkou pásma pro Cloud. |
| Maximální propustnost čtení/zápisu klienta (při obsluhování z vrstvy SSD) * |920/720 MB/s s jedním síťovým rozhraním 10 GbE |Až 2x s funkcí MPIO a dvěma síťovými rozhraními. |
| Maximální propustnost čtení/zápisu klienta (při obsluhování z vrstvy HDD) * |120/250 MB/s | |
| Maximální propustnost čtení/zápisu klienta (Pokud se obsluhuje z vrstvy cloudu) * pro Update 3 a novější * * |40/60 MB/s pro vrstvené svazky<br><br>60/80 MB/s pro vrstvené svazky s možností archivace vybraná při vytváření svazku |Propustnost čtení závisí na klientech, které generují a udržují dostatečnou hloubku vstupně-výstupních front. <br><br>Dosažená rychlost závisí na rychlosti použitého základního účtu úložiště. |

&#42; maximální propustnost na vstupně-výstupní typ byl změřen pomocí scénářů 100% čtení a 100 procent zápisu. Skutečná propustnost může být nižší a závisí na kombinacích vstupně-výstupních operací a síťových podmínek.

Hodnoty výkonu &#42;&#42; před aktualizací 3 mohou být nižší.

## <a name="next-steps"></a>Další kroky
Projděte si [požadavky na systém StorSimple](storsimple-8000-system-requirements.md).

