---
title: Omezení systému StorSimple 8000 series | Dokumentace Microsoftu
description: Popisuje omezení systému a doporučené velikosti pro komponenty řady StorSimple 8000 a připojení.
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
ms.openlocfilehash: a0053f950b36351b06d08630cbf9977f53f2ed47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319560"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Jaká jsou omezení systému řady StorSimple 8000?

## <a name="overview"></a>Přehled

StorSimple poskytuje škálovatelné a flexibilní úložiště pro vaše datové centrum. Existují však některá omezení, které je třeba vzít v úvahu při plánování, nasazení a provoz vašeho řešení StorSimple. Následující tabulka popisuje tato omezení a uvádí několik doporučení, takže můžete získat maximum z vašeho řešení StorSimple.

| Identifikátor omezení | Omezení | Komentáře |
| --- | --- | --- |
| Maximální počet přihlašovací údaje účtu úložiště |64 | |
| Maximální počet kontejnerů svazků |64 | |
| Maximální počet svazků |255 | |
| Maximální počet místně připojené svazky |32 | |
| Maximální počet plánů na šablonu šířky pásma |168 |Plán pro každou hodinu, každý den v týdnu (24 * 7). |
| Maximální velikost vrstveného svazku na fyzických zařízeních |64 TB pro 8100 a 8600 |8100 a 8600 jsou fyzické zařízení. |
| Maximální velikost vrstveného svazku na virtuální zařízení v Azure |30 TB for 8010 <br></br> 64 TB for 8020 |8010 a 8020 je virtuální zařízení v Azure, které používají úložiště úrovně Standard a Premium Storage v uvedeném pořadí. |
| Maximální velikost místně vázaný svazek na fyzických zařízeních |Velikosti 8.5 TB pro 8100 <br></br> 22.5 TB pro 8600 |8100 a 8600 jsou fyzické zařízení. |
| Maximální počet připojení iSCSI |512 | |
| Maximální počet připojení iniciátorů iSCSI |512 | |
| Maximální počet záznamy řízení přístupu podle zařízení |64 | |
| Maximální počet svazků na zásady zálohování |20 | |
| Maximální počet záloh uchovávají za plán (v zásadách zálohování) |64 | |
| Maximální počet plánů na zásady zálohování |10 | |
| Maximální počet snímků typ, který se můžou uchovávat na jeden svazek |256 |Toto číslo zahrnuje místních snímků a cloudových snímků. |
| Maximální počet snímků, které mohou být přítomny v libovolném zařízení |10 000 | |
| Maximální počet svazků, které lze zpracovat paralelní zálohování, obnovení nebo klonování |16 |<ul><li>Pokud existuje více než 16 svazků, že se provádějí postupně. Jakmile budou dostupné sloty zpracování.</li><li>Nové zálohy klonovaný nebo obnovený vrstveného svazku nelze provést, dokud se nedokončí operaci. Ale pro místní svazek, zálohování se po povolena svazek je online.</li></ul> |
| Obnovení a klonování obnovit dobu vrstvené svazky |< 2 minut |<ul><li>Svazek je k dispozici během 2 minut operace obnovení nebo klonování, bez ohledu na velikost svazku.</li><li>Výkon svazku může být zpočátku nižší než normální, protože většina dat a metadat se stále nachází v cloudu. Jako toků dat z cloudu do zařízení StorSimple může zvýšit výkon.</li><li>Celkový čas se stáhnout metadata závisí na velikosti přiděleného svazku. Metadata automaticky přenese do zařízení ve výši 5 minut na TB přidělený objem dat na pozadí. Tato sazba mohou být ovlivněny šířky pásma Internetu do cloudu.</li><li>Obnovení nebo operace klonování je dokončena po všechna metadata v zařízení.</li><li>Operace zálohování nelze provést, dokud nebude obnovení nebo operace klonování je úplně dokončená. |
| Čas obnovení místně vázaných svazků obnovení |< 2 minut |<ul><li>Svazek je k dispozici během 2 minut operace obnovení, bez ohledu na velikost svazku.</li><li>Výkon svazku může být zpočátku nižší než normální, protože většina dat a metadat se stále nachází v cloudu. Jako toků dat z cloudu do zařízení StorSimple může zvýšit výkon.</li><li>Celkový čas se stáhnout metadata závisí na velikosti přiděleného svazku. Metadata automaticky přenese do zařízení ve výši 5 minut na TB přidělený objem dat na pozadí. Tato sazba mohou být ovlivněny šířky pásma Internetu do cloudu.</li><li>Na rozdíl od vrstvených svazků pro místně připojené svazky data na svazku se také stáhne místně na zařízení. Operaci obnovení je dokončena, když do zařízení, nemůžete všechna data svazku.</li><li>Operace obnovení může být dlouhý. Celkový čas k dokončení obnovení závisí na velikosti zřízeného svazku místní, internetové šířce pásma a existující data na zařízení. Operace zálohování na místně vázaný svazek jsou povoleny, když probíhá operace obnovení. |
| Rychlost zpracování pro cloudové snímky |15 minut/TB |<ul><li>Minimální doba pro mohlo způsobit nepoužitelnost snímek připravený k nahrání za TB přidělený objem dat ve službě backup. </li><li> Celkový počet cloudových snímků čas se počítá tak, že přidáte tuto chvíli na dobu nahrávání snímků, která je ovlivněna nastavením šířky pásma Internetu do cloudu. |
| Propustnost čtení a zápis maximálního počtu klientů (když obsluhovat z vrstvy SSD) * |920/720 MB/s s jeden 10 GbE síťové rozhraní |Až 2 x pomocí funkce MPIO a dvě síťová rozhraní. |
| Propustnost čtení a zápis maximálního počtu klientů (když obsluhovat z vrstvy HDD) * |120/250 MB/s | |
| Propustnost čtení a zápis maximálního počtu klientů (když obsluhovat z cloudovou vrstvu) * pro s aktualizací Update 3 a novější ** |40/60 MB/s pro vrstvené svazky<br><br>60/80 MB/s pro vrstvené svazky s archivace možností vybírá při vytváření svazku |Propustnost čtení závisí na klientských počítačích generování a zachování dostatečná hloubky fronty vstupně-výstupních operací. <br><br>Rychlost dosáhnout závisí na rychlosti podkladové účtu úložiště používat. |

&#42;Maximální propustnost na typ vstupně-výstupní operace se měří s scénáře 100 procent zápisu a čtení 100 procent. Skutečná propustnost může být nižší a závisí na vstupně-výstupních operací kombinovat a síťové podmínky.

&#42;&#42;Může být nižší výkon čísla před aktualizací Update 3.

## <a name="next-steps"></a>Další postup
Zkontrolujte [požadavky na systém pro StorSimple](storsimple-8000-system-requirements.md).

