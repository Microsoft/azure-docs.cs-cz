---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175038"
---
| **Identifikátor omezení** | **Limit** | **Komentáře** |
| --- | --- | --- |
| Celková kapacita (včetně cloudu) |Až 64 TB na jedno virtuální zařízení |Můžete přepojit celé storsimple virtuální pole do jiného prázdného pole. Pokud se pokusíte obnovit stejné zařízení, ujistěte se, že máte dostatek místa na zařízení k dokončení této operace. Po překročení 32 TB nelze obnovit do stejného zařízení. |
| Maximální počet přihlašovacích údajů účtu úložiště na zařízení |1 | |
| Maximální počet svazků/sdílených složek |16 | |
| Minimální velikost vrstvené sdílené složky |500 GB | |
| Minimální velikost vrstveného svazku |500 GB | |
| Maximální velikost vrstvené sdílené složky |20 TB | |
| Maximální velikost vrstveného svazku |5 TB | |
| Minimální velikost místně připnuté sdílené složky |50 GB | |
| Minimální velikost místně vázanýho svazku |50 GB | |
| Maximální velikost místně připnuté sdílené složky |2 TB | |
| Maximální velikost místně vázanýho svazku |200 GB | |
| Maximální počet připojení iSCSI od iniciátorů |512 | |
| Maximální počet záznamů řízení přístupu na zařízení |64 | |
| Maximální počet záloh uchovávaných virtuálním zařízením ve složce *Zálohy* pro souborový server |5 |To zahrnuje nejnovější naplánované (generované výchozí zásady zálohování) a ruční zálohování. |
| Maximální počet plánovaných záloh uchovávaných zařízením |55 |30 denních záloh<br>12 měsíčních záloh<br>13 ročních záloh |
| Maximální počet ručních záloh uchovávaných zařízením |45 | |
| Maximální počet souborů na sdílenou složku pro souborový server |1 milion |Při provádění obnovení zařízení jsou doby obnovení úměrné počtu souborů ve všech sdílených složkách v zařízení. |
| Maximální počet souborů na svazek pro server iSCSI |1 milion | |
| Maximální počet souborů na virtuální pole |4 miliony | |
| Obnovit čas obnovení |Rychlé obnovení |Obnovení je založeno na tepelné mapě a závisí na velikosti svazku.<br>Operace zálohování může dojít v průběhu operace obnovení. |

