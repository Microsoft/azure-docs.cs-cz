---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175038"
---
| **Identifikátor omezení** | **Počtu** | **Komentáře** |
| --- | --- | --- |
| Celková kapacita (včetně cloudu) |Až 64 TB na virtuální zařízení |U celého StorSimple virtuálního pole můžete převzít služby v jiném prázdném poli. Pokud se pokusíte provést obnovení do stejného zařízení, ujistěte se, že na zařízení máte dostatek místa k dokončení této operace. Po překročení 32 TB nebude možné obnovit stejné zařízení. |
| Maximální počet přihlašovacích údajů účtu úložiště na zařízení |1 | |
| Maximální počet svazků/sdílených složek |16 | |
| Minimální velikost vrstveného sdílení |500 GB | |
| Minimální velikost vrstveného svazku |500 GB | |
| Maximální velikost vrstvené sdílené složky |20 TB | |
| Maximální velikost vrstveného svazku |5 TB | |
| Minimální velikost místně připnuté sdílené složky |50 GB | |
| Minimální velikost místně připnutého svazku |50 GB | |
| Maximální velikost místně připnuté sdílené složky |2 TB | |
| Maximální velikost místně připnutého svazku |200 GB | |
| Maximální počet připojení iSCSI od iniciátorů |512 | |
| Maximální počet záznamů řízení přístupu na zařízení |64 | |
| Maximální počet záloh uchovávaných virtuálním zařízením ve složce *.* backups pro souborový server |5 |To zahrnuje nejnovější naplánované (vygenerované výchozí zásady zálohování) a ruční zálohování. |
| Maximální počet plánovaných záloh uchovávaných zařízením |55 |30 denních záloh<br>12 měsíčních záloh<br>13 ročních záloh |
| Maximální počet ručních záloh uchovávaných zařízením |45 | |
| Maximální počet souborů na sdílenou složku pro souborový server |1 milion |Při obnovení zařízení jsou časy obnovení úměrné počtu souborů ve všech sdílených složkách na zařízení. |
| Maximální počet souborů na svazek pro server iSCSI |1 milion | |
| Maximální počet souborů na virtuální pole |4 000 000 | |
| Obnovit čas obnovení |Rychlé obnovení |Obnovení je založené na Heat mapě a závisí na velikosti svazku.<br>Operace zálohování se můžou vyskytnout během probíhající operace obnovení. |

