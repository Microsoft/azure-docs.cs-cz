---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175405"
---
| Identifikátor omezení | Omezení | Komentáře |
| --- | --- | --- |
| Maximální počet přihlašovacích údajů účtu úložiště |64 | |
| Maximální počet objemových kontejnerů |64 | |
| Maximální počet svazků |255 | |
| Maximální počet plánů na šablonu šířky pásma |168 |Rozvrh na každou hodinu, každý den v týdnu. |
| Maximální velikost vrstveného svazku na fyzických zařízeních |64 TB pro StorSimple 8100 a StorSimple 8600 |StorSimple 8100 a StorSimple 8600 jsou fyzická zařízení. |
| Maximální velikost vrstveného svazku na virtuálních zařízeních v Azure |30 TB pro StorSimple 8010 <br></br> 64 TB pro StorSimple 8020 |StorSimple 8010 a StorSimple 8020 jsou virtuální zařízení v Azure, která používají standardní úložiště a úložiště Premium. |
| Maximální velikost místně vázaných svazků na fyzických zařízeních |9 TB pro StorSimple 8100 <br></br> 24 TB pro StorSimple 8600 |StorSimple 8100 a StorSimple 8600 jsou fyzická zařízení. |
| Maximální počet připojení iSCSI |512 | |
| Maximální počet připojení iSCSI od iniciátorů |512 | |
| Maximální počet záznamů řízení přístupu na zařízení |64 | |
| Maximální počet svazků na zásady zálohování |24 | |
| Maximální počet záloh uchovávaných podle zásad zálohování |64 | |
| Maximální počet plánů na zásady zálohování |10 | |
| Maximální počet snímků libovolného typu, které lze zachovat na svazek |256 |Tato částka zahrnuje místní snímky a cloudové snímky. |
| Maximální počet snímků, které mohou být přítomny v libovolném zařízení |10 000 | |
| Maximální počet svazků, které mohou být zpracovány paralelně pro zálohování, obnovení nebo klonování |16 |<ul><li>Pokud existuje více než 16 svazků, jsou zpracovány postupně jako zpracování slotů k dispozici.</li><li>Nové zálohy klonovaného nebo obnoveného vrstveného svazku nemohou nastat, dokud nebude operace dokončena. U místního svazku jsou po připojení svazku do režimu online povoleny zálohy.</li></ul> |
| Obnovení a obnovení času obnovení pro vrstvené svazky |<2 minuty |<ul><li>Svazek je k dispozici do 2 minut od operace obnovení nebo klonování bez ohledu na velikost svazku.</li><li>Výkon svazku může být zpočátku pomalejší než obvykle, protože většina dat a metadat je stále umístěna v cloudu. Výkon může zvýšit jako toky dat z cloudu do zařízení StorSimple.</li><li>Celková doba stahování metadat závisí na přidělené velikosti svazku. Metadata se automaticky přenese do zařízení na pozadí rychlostí 5 minut na TB přidělených dat svazku. Tato rychlost může být ovlivněna šířkou pásma Internetu do cloudu.</li><li>Operace obnovení nebo klonování je dokončena, když jsou v zařízení všechna metadata.</li><li>Operace zálohování nelze provést, dokud není operace obnovení nebo klonování zcela dokončena. |
| Obnovení doby obnovení místně vázaných svazků |<2 minuty |<ul><li>Svazek je k dispozici do 2 minut od operace obnovení, bez ohledu na velikost svazku.</li><li>Výkon svazku může být zpočátku pomalejší než obvykle, protože většina dat a metadat je stále umístěna v cloudu. Výkon může zvýšit jako toky dat z cloudu do zařízení StorSimple.</li><li>Celková doba stahování metadat závisí na přidělené velikosti svazku. Metadata se automaticky přenese do zařízení na pozadí rychlostí 5 minut na TB přidělených dat svazku. Tato rychlost může být ovlivněna šířkou pásma Internetu do cloudu.</li><li>Na rozdíl od vrstvených svazků, pokud existují místně vázaných svazků, data svazku se také stáhnou místně v zařízení. Operace obnovení je dokončena, jakmile byla do zařízení přenesena všechna data svazku.</li><li>Operace obnovení mohou být dlouhé a celková doba dokončení obnovení bude záviset na velikosti zřízeného místního svazku, šířce pásma Internetu a existujících datech v zařízení. Operace zálohování na místně vázaný svazek jsou povoleny, zatímco operace obnovení probíhá. |
| Dostupnost tenkého obnovení |Poslední převzetí služeb při selhání | |
| Maximální propustnost klienta pro čtení a zápis, pokud je obsluhována z vrstvy SSD* |920/720 MB/s s jedním 10gigabitovým síťovým rozhraním Ethernet |Až dvakrát s MPIO a dvěma síťovými rozhraními. |
| Maximální propustnost klienta pro čtení a zápis, pokud je obsluhována z úrovně HDD* |120/250 MB/s | |
| Maximální propustnost klienta pro čtení a zápis, pokud je obsluhována z cloudové vrstvy* |11/41 MB/s |Propustnost čtení závisí na klientech, kteří generují a udržují dostatečnou hloubku fronty vstupně-vstupně-in. |

&#42;Maximální propustnost na typ vstupně-v.a. byla měřena se 100 % scénáři čtení a 100 % zápisu. Skutečná propustnost může být nižší a závisí na kombinaci vstupně-va a podmínek sítě.

