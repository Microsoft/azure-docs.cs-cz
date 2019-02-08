---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db05ce33a9fb602d6d48d1a1606f48a7fbde246e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889619"
---
| Identifikátor omezení | Omezení | Komentáře |
| --- | --- | --- |
| Maximální počet přihlašovací údaje účtu úložiště |64 | |
| Maximální počet kontejnerů svazků |64 | |
| Maximální počet svazků |255 | |
| Maximální počet plánů na šablonu šířky pásma |168 |Plán pro každou hodinu, každý den v týdnu (24 * 7). |
| Maximální velikost vrstveného svazku na fyzických zařízeních |64 TB pro 8100 a 8600 |8100 a 8600 jsou fyzické zařízení. |
| Maximální velikost vrstveného svazku na virtuální zařízení v Azure |30 TB for 8010 <br></br> 64 TB for 8020 |8010 a 8020 je virtuální zařízení v Azure, které používají úložiště úrovně Standard a Premium Storage v uvedeném pořadí. |
| Maximální velikost místně vázaný svazek na fyzických zařízeních |9 TB for 8100 <br></br> 24 TB pro 8600 |8100 a 8600 jsou fyzické zařízení. |
| Maximální počet připojení iSCSI |512 | |
| Maximální počet připojení iniciátorů iSCSI |512 | |
| Maximální počet záznamy řízení přístupu podle zařízení |64 | |
| Maximální počet svazků na zásady zálohování |24 | |
| Maximální počet záloh uchovávají jednotlivé zásady zálohování |64 | |
| Maximální počet plánů na zásady zálohování |10 | |
| Maximální počet snímků typ, který se můžou uchovávat na jeden svazek |256 |Jedná se o místní snímky a cloudových snímků. |
| Maximální počet snímků, které mohou být přítomny v libovolném zařízení |10 000 | |
| Maximální počet svazků, které lze zpracovat paralelní zálohování, obnovení nebo klonování |16 |<ul><li>Pokud existuje více než 16 svazků, se budou zpracovávat postupně zpracování sloty jsou k dispozici.</li><li>Nové zálohy klonovaný nebo obnovený vrstveného svazku nelze provést, dokud se nedokončí operaci. Ale pro místní svazek, zálohování se po povolena svazek je online.</li></ul> |
| Obnovení a klonování obnovit dobu vrstvené svazky |< 2 minut |<ul><li>Svazek je k dispozici během 2 minut operace obnovení nebo klonování, bez ohledu na velikost svazku.</li><li>Výkon svazku může být zpočátku nižší než normální, protože většina dat a metadat se stále nachází v cloudu. Jako toků dat z cloudu do zařízení StorSimple může zvýšit výkon.</li><li>Celkový čas se stáhnout metadata závisí na velikosti přiděleného svazku. Metadata automaticky přenese do zařízení ve výši 5 minut na TB přidělený objem dat na pozadí. Tato sazba mohou být ovlivněny šířky pásma Internetu do cloudu.</li><li>Obnovení nebo operace klonování je dokončena po všechna metadata v zařízení.</li><li>Operace zálohování nelze provést, dokud nebude obnovení nebo operace klonování je úplně dokončená. |
| Čas obnovení místně vázaných svazků obnovení |< 2 minut |<ul><li>Svazek je k dispozici během 2 minut operace obnovení, bez ohledu na velikost svazku.</li><li>Výkon svazku může být zpočátku nižší než normální, protože většina dat a metadat se stále nachází v cloudu. Jako toků dat z cloudu do zařízení StorSimple může zvýšit výkon.</li><li>Celkový čas se stáhnout metadata závisí na velikosti přiděleného svazku. Metadata automaticky přenese do zařízení ve výši 5 minut na TB přidělený objem dat na pozadí. Tato sazba mohou být ovlivněny šířky pásma Internetu do cloudu.</li><li>Na rozdíl od vrstvených svazků, v případě místně připojené svazky data na svazku se také stáhne místně na zařízení. Operaci obnovení je dokončena, když do zařízení, nemůžete všechna data svazku.</li><li>Operace obnovení může být dlouhý a celkový čas k dokončení obnovení bude záviset na velikosti zřízeného svazku místní, internetové šířce pásma a existující data na zařízení. Operace zálohování na místně vázaný svazek jsou povoleny, když probíhá operace obnovení. |
| Dostupnost dynamicky obnovení |Poslední převzetí služeb při selhání | |
| Propustnost čtení a zápis maximálního počtu klientů (když obsluhovat z vrstvy SSD) * |920/720 MB/s s jeden 10GbE síťové rozhraní |Až 2 x pomocí funkce MPIO a dvě síťová rozhraní. |
| Propustnost čtení a zápis maximálního počtu klientů (když obsluhovat z vrstvy HDD) * |120/250 MB/s | |
| Propustnost čtení a zápis maximálního počtu klientů (když obsluhovat z cloudovou vrstvu) * |11/41 MB/s |Propustnost čtení závisí na klientských počítačích generování a zachování dostatečná hloubky fronty vstupně-výstupních operací. |

&#42;Maximální propustnost na typ vstupně-výstupní operace se měří s scénáře 100 procent zápisu a čtení 100 procent. Skutečná propustnost může být nižší a závisí na vstupně-výstupních operací kombinovat a síťové podmínky.

