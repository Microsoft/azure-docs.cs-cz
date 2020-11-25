---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: e7b7fae094ad15bc1732778b6a4a3259fb4dd3b5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027648"
---
| Prostředek | Standardní sdílené složky\* | Soubory ke sdílení souborů úrovně Premium |
|----------|---------------|------------------------------------------|
| Minimální velikost sdílené složky | Bez minima; průběžné platby | 100 GiB; zřízené |
| Maximální velikost sdílené složky | 100 TiB \* \* , 5 TIB | 100 TiB |
| Maximální velikost souboru ve sdílené složce | 1 TiB | 4 TiB |
| Maximální počet souborů ve sdílené složce | Bez omezení | Bez omezení |
| Maximální počet IOPS na sdílenou složku | 10 000 IOPS \* \* , 1 000 iops nebo 100 požadavků v 100 MS | 100 000 IOPS |
| Maximální počet uložených zásad přístupu na sdílení souborů | 5 | 5 |
| Cílová propustnost pro jednu sdílenou složku | až 300 MiB/s \* \* , až 60 MIB/s,  | Viz hodnoty příchozího a odchozího sdílení souborů Premium|
| Maximální počet odchozích dat pro jednu sdílenou složku | Viz standardní propustnost cíle sdílení souborů | Až 6 204 MiB/s |
| Maximální příchozí přenos dat pro jednu sdílenou složku | Viz standardní propustnost cíle sdílení souborů | Až 4 136 MiB/s |
| Maximální počet otevřených popisovačů na soubor nebo adresář | 2 000 otevřených popisovačů | 2 000 otevřených popisovačů |
| Maximální počet snímků sdílené složky | 200 snímky sdílené složky | 200 snímky sdílené složky |
| Maximální délka názvu objektu (adresářů a souborů) | 2 048 znaků | 2 048 znaků |
| Maximální součást cesty (v cestě \A\B\C\D je každé písmeno součásti) | 255 znaků | 255 znaků |
| Limit pevných odkazů (jenom NFS) | Není k dispozici | 178 |
| Maximální počet vícekanálových kanálů protokolu SMB | Není k dispozici | 4 |

\* Limity pro standardní sdílené složky se vztahují na všechny tři úrovně, které jsou dostupné pro standardní sdílené složky: transakce je optimalizovaná, horká a studená.

\*\* Výchozí nastavení pro standardní sdílené složky je 5 TiB. podrobné informace o tom, jak zvýšit úroveň standardních 100 sdílených složek, najdete v tématu [povolení a vytváření velkých sdílených složek](../articles/storage/files/storage-files-how-to-create-large-file-share.md) .
