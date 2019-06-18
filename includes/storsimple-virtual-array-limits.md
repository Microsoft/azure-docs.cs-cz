---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66143293"
---
| **Identifikátor omezení** | **Limit** | **Komentář** |
| --- | --- | --- |
| Celková kapacita (včetně cloud) |Až 64 TB na virtuální zařízení |Můžete předat úplnou StorSimple Virtual Array do jiného prázdné pole. Pokud se pokusíte obnovit do stejného zařízení, ujistěte se, že máte dostatek volného místa pro dokončení této operace. Poté, co jste překročili 32 TB, nelze obnovit do stejného zařízení. |
| Maximální počet přihlašovací údaje účtu úložiště na zařízení |1 | |
| Maximální počet svazků nebo sdílených složek |16 | |
| Minimální velikost vrstvenou sdílenou složku |500 GB | |
| Minimální velikost vrstvený svazek |500 GB | |
| Maximální velikost vrstvenou sdílenou složku |20 TB | |
| Maximální velikost vrstvený svazek |5 TB | |
| Minimální velikost místně připojené sdílené složky |50 GB | |
| Minimální velikost místně vázaný svazek |50 GB | |
| Maximální velikost místně připojené sdílené složky |2 TB | |
| Maximální velikost místně vázaný svazek |200 GB | |
| Maximální počet připojení iniciátorů iSCSI |512 | |
| Maximální počet záznamy řízení přístupu podle zařízení |64 | |
| Maximální počet záloh uchovávají virtuálním zařízením v *.backups* složku pro souborový server |5 |Jedná se o nejnovější plánované (generovaný podle výchozí zásady zálohování) a ručního zálohování. |
| Maximální počet plánovaných záloh uchovávají zařízení |55 |30 denního zálohování<br>12 měsíční zálohy<br>13 roční zálohy |
| Maximální počet ruční zálohy uchovávat v zařízení |45 | |
| Maximální počet souborů na sdílenou složku souborového serveru |1 milion |Při provádění obnovení zařízení, časů obnovení jsou přímo úměrná počtu souborů v rámci sdílené složky v zařízení. |
| Maximální počet souborů na svazku pro iSCSI server |1 milion | |
| Maximální počet souborů na virtuální pole |4 miliony | |
| Obnovit čas obnovení |Rychlé obnovení |Obnovení je založen na heat mapa a závisí na velikosti svazku.<br>Operace zálohování může dojít, když probíhá operace obnovení. |

