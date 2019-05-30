---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238603"
---
| Identifikátor omezení | Omezení | Komentáře |
| --- | --- | --- |
| Maximální počet přihlašovací údaje účtu úložiště |64 | |
| Maximální počet kontejnerů svazků |64 | |
| Maximální počet svazků |255 | |
| Maximální počet plánů na šablonu šířky pásma |168 |Plán pro každou hodinu, každý den v týdnu. |
| Maximální velikost vrstveného svazku na fyzických zařízeních |64 TB pro StorSimple 8100 a StorSimple 8600 |StorSimple 8100 a StorSimple 8600 jsou fyzické zařízení. |
| Maximální velikost vrstveného svazku na virtuální zařízení v Azure |Pro StorSimple 8010 s 30 TB <br></br> 64 TB pro StorSimple 8020 |StorSimple 8010 a StorSimple 8020 jsou virtuální zařízení v Azure, které používají úložiště úrovně Standard a Premium storage, v uvedeném pořadí. |
| Maximální velikost místně vázaný svazek na fyzických zařízeních |9 TB pro StorSimple 8100 <br></br> 24 TB pro StorSimple 8600 |StorSimple 8100 a StorSimple 8600 jsou fyzické zařízení. |
| Maximální počet připojení iSCSI |512 | |
| Maximální počet připojení iniciátorů iSCSI |512 | |
| Maximální počet záznamy řízení přístupu podle zařízení |64 | |
| Maximální počet svazků na zásady zálohování |24 | |
| Maximální počet záloh uchovávají jednotlivé zásady zálohování |64 | |
| Maximální počet plánů na zásady zálohování |10 | |
| Maximální počet snímků typ, který se můžou uchovávat na jeden svazek |256 |Toto množství zahrnuje místních snímků a cloudových snímků. |
| Maximální počet snímků, které mohou být přítomny v libovolném zařízení |10,000 | |
| Maximální počet svazků, které lze zpracovat paralelní zálohování, obnovení nebo klonování |16 |<ul><li>Pokud existuje více než 16 svazků, jejich se provádějí postupně. Jakmile budou dostupné sloty zpracování.</li><li>Nové zálohy klonovaný nebo obnovený vrstveného svazku nelze provést, dokud se nedokončí operaci. Pro místní svazek jsou povolené zálohování po svazek je online.</li></ul> |
| Obnovení a klonování obnovit dobu vrstvené svazky |< 2 minut |<ul><li>Svazek je k dispozici během 2 minut obnovení nebo operace klonování, bez ohledu na velikost svazku.</li><li>Výkon svazku může být zpočátku pomalejší než obvykle, protože většina dat a metadat se stále nachází v cloudu. Jako toků dat z cloudu do zařízení StorSimple může zvýšit výkon.</li><li>Celkový čas se stáhnout metadata závisí na velikosti přiděleného svazku. Metadata automaticky přenese do zařízení ve výši 5 minut na TB přidělený objem dat na pozadí. Tato sazba by mohly mít dopad šířky pásma Internetu do cloudu.</li><li>Obnovení nebo operace klonování je dokončena po všechna metadata v zařízení.</li><li>Operace zálohování nelze provést, dokud nebude obnovení nebo operace klonování je úplně dokončená. |
| Čas obnovení místně vázaných svazků obnovení |< 2 minut |<ul><li>Svazek je k dispozici během 2 minut operace obnovení, bez ohledu na velikost svazku.</li><li>Výkon svazku může být zpočátku pomalejší než obvykle, protože většina dat a metadat se stále nachází v cloudu. Jako toků dat z cloudu do zařízení StorSimple může zvýšit výkon.</li><li>Celkový čas se stáhnout metadata závisí na velikosti přiděleného svazku. Metadata automaticky přenese do zařízení ve výši 5 minut na TB přidělený objem dat na pozadí. Tato sazba by mohly mít dopad šířky pásma Internetu do cloudu.</li><li>Na rozdíl od vrstvených svazků Pokud existují místně připojené svazky data na svazku se také stáhne místně na zařízení. Operaci obnovení je dokončena, když do zařízení, nemůžete všechna data svazku.</li><li>Operace obnovení mohou být dlouhé a celková doba nutná k dokončení obnovení bude záviset na velikosti zřízeného svazku místní, internetové šířce pásma a existující data na zařízení. Operace zálohování na místně vázaný svazek jsou povoleny, když probíhá operace obnovení. |
| Dostupnost dynamicky obnovení |Poslední převzetí služeb při selhání | |
| Propustnost čtení a zápis maximálního počtu klientů, při obsluhovat z vrstvy SSD * |920/720 MB/s s jeden 10gigabitová ethernetová síťová karta |Až dvakrát pomocí funkce MPIO a dvě síťová rozhraní. |
| Propustnost čtení a zápis maximálního počtu klientů, při obsluhovat z vrstvy HDD * |120/250 MB/sec | |
| Propustnost čtení a zápis maximálního počtu klientů, při obsluhovat z vrstvy cloudu * |11/41 MB/sec |Propustnost čtení závisí na klientských počítačích generování a zachování dostatečná hloubky fronty vstupně-výstupních operací. |

&#42;Maximální propustnost na typ vstupně-výstupní operace se měří s scénáře 100 procent zápisu a čtení 100 procent. Skutečná propustnost může být nižší a závisí na vstupně-výstupních operací kombinovat a síťové podmínky.

