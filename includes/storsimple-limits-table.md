---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175405"
---
| Identifikátor omezení | Omezení | Komentáře |
| --- | --- | --- |
| Maximální počet přihlašovacích údajů účtu úložiště |64 | |
| Maximální počet kontejnerů svazků |64 | |
| Maximální počet svazků |255 | |
| Maximální počet plánů na šablonu šířky pásma |168 |Plán každé hodiny, každý den v týdnu. |
| Maximální velikost vrstveného svazku na fyzických zařízeních |64 TB pro StorSimple 8100 a StorSimple 8600 |StorSimple 8100 a StorSimple 8600 jsou fyzická zařízení. |
| Maximální velikost vrstveného svazku na virtuálních zařízeních v Azure |30 TB pro StorSimple 8010 <br></br> 64 TB pro StorSimple 8020 |StorSimple 8010 a StorSimple 8020 jsou virtuální zařízení v Azure, která používají úložiště úrovně Standard a Premium (v uvedeném pořadí). |
| Maximální velikost místně připnutého svazku na fyzických zařízeních |9 TB pro StorSimple 8100 <br></br> 24 TB pro StorSimple 8600 |StorSimple 8100 a StorSimple 8600 jsou fyzická zařízení. |
| Maximální počet připojení iSCSI |512 | |
| Maximální počet připojení iSCSI od iniciátorů |512 | |
| Maximální počet záznamů řízení přístupu na zařízení |64 | |
| Maximální počet svazků na zásadu zálohování |24 | |
| Maximální počet uchovávaných záloh na zásadu zálohování |64 | |
| Maximální počet plánů na zásadu zálohování |10 | |
| Maximální počet snímků libovolného typu, které se dají uchovávat na jeden svazek |256 |Tato hodnota zahrnuje místní snímky a cloudové snímky. |
| Maximální počet snímků, které mohou být k dispozici na jakémkoli zařízení |10 000 | |
| Maximální počet svazků, které lze paralelně zpracovat pro zálohování, obnovení nebo klonování |16 |<ul><li>Pokud je k dispozici více než 16 svazků, zpracují se postupně, jakmile budou dostupné sloty pro zpracování.</li><li>Nové zálohy klonovaného nebo obnoveného vrstveného svazku nemůžou nastat, dokud se operace nedokončí. Pro místní svazek jsou zálohy povoleny po dobu, kdy je svazek online.</li></ul> |
| Obnovení a klonování času obnovení pro vrstvené svazky |<2 minuty |<ul><li>Svazek je k dispozici do 2 minut operace obnovení nebo klonování, bez ohledu na velikost svazku.</li><li>Výkon svazku může zpočátku pomalejší než normální, protože většina dat a metadata se pořád nacházejí v cloudu. Výkon se může zvýšit jako tok dat z cloudu do zařízení StorSimple.</li><li>Celková doba stahování metadat závisí na velikosti přiděleného svazku. Do zařízení se automaticky přiřadí metadata na pozadí rychlostí 5 minut na TB přidělených dat svazku. Tato rychlost může být ovlivněna internetovou šířkou pásma cloudu.</li><li>Operace obnovení nebo klonování je dokončena, když jsou všechna metadata v zařízení.</li><li>Operace zálohování nelze provést, dokud nebude operace obnovení nebo klonování zcela dokončena. |
| Obnovit čas obnovení pro místně připojené svazky |<2 minuty |<ul><li>Svazek je k dispozici do 2 minut operace obnovení, bez ohledu na velikost svazku.</li><li>Výkon svazku může zpočátku pomalejší než normální, protože většina dat a metadata se pořád nacházejí v cloudu. Výkon se může zvýšit jako tok dat z cloudu do zařízení StorSimple.</li><li>Celková doba stahování metadat závisí na velikosti přiděleného svazku. Do zařízení se automaticky přiřadí metadata na pozadí rychlostí 5 minut na TB přidělených dat svazku. Tato rychlost může být ovlivněna internetovou šířkou pásma cloudu.</li><li>Na rozdíl od vrstvených svazků, pokud jsou místně připojené svazky, data o svazcích se stáhnou i místně na zařízení. Operace obnovení je dokončena, když jsou všechna data svazků přenesena do zařízení.</li><li>Operace obnovení můžou být dlouhé a celková doba k dokončení obnovení bude záviset na velikosti zřízeného místního svazku, šířce pásma internetu a stávajících datech na zařízení. V průběhu operace obnovení jsou povoleny operace zálohování na místně připojeném svazku. |
| Dostupnost s úzkým obnovením |Poslední převzetí služeb při selhání | |
| Maximální propustnost čtení a zápisu klienta, pokud je obsluhována z vrstvy SSD * |920/720 MB/s s jedním síťovým rozhraním ethernetového Ethernetu |Až dvakrát pomocí funkce MPIO a dvou síťových rozhraní. |
| Maximální propustnost čtení/zápisu klienta, pokud je obsluhována z vrstvy HDD * |120/250 MB/s | |
| Maximální propustnost čtení a zápisu klienta, pokud se obsluhuje z vrstvy cloudu * |11/41 MB/s |Propustnost čtení závisí na klientech, které generují a udržují dostatečnou hloubku vstupně-výstupních front. |

&#42;maximální propustnost na vstupně-výstupní typ byl změřen pomocí scénářů 100% čtení a 100 procent zápisu. Skutečná propustnost může být nižší a závisí na kombinaci vstupně-výstupních operací a síťových podmínek.

