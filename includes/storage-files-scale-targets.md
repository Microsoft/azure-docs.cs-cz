---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536423"
---
| Prostředek | Standardní sdílené složky | Sdílené složky Premium |
|----------|---------------|------------------------------------------|
| Minimální velikost sdílené složky | Žádné minimum; platit průběžně | 100 GIB; Zřízena |
| Maximální velikost sdílené složky | 100 TiB*, 5 TiB | 100 TiB |
| Maximální velikost souboru ve sdílené složce | 1 TiB | 1 TiB |
| Maximální počet souborů ve sdílené složce | Bez omezení | Bez omezení |
| Maximální viposlužby na akcii | 10 000 IOP*, 1 000 IOPS | 100 000 IOPS |
| Maximální počet uložených zásad přístupu na sdílenou složku | 5 | 5 |
| Cílová propustnost pro jednu sdílenou složku | až 300 MiB/s*, Až 60 MiB/s ,  | Viz hodnoty sdílení a odchozího přenosu souborů premium|
| Maximální odchozí přenos pro jednu sdílenou složku | Zobrazení standardní propustnost cíle sdílení souborů | Až 6 204 MiB/s |
| Maximální příchozí přenos dat pro jednu sdílenou složku | Zobrazení standardní propustnost cíle sdílení souborů | Až 4 136 MiB/s |
| Maximální počet otevřených popisovačů na soubor | 2 000 otevřených úchytů | 2 000 otevřených úchytů |
| Maximální počet snímků sdílené složky | 200 snímků sdílené složky | 200 snímků sdílené složky |
| Maximální délka názvu objektu (adresářů a souborů) | 2 048 znaků | 2 048 znaků |
| Maximální cesta komponenty (v cestě \A\B\C\D, každé písmeno je součást) | 255 znaků | 255 znaků |

\*Výchozí u standardních sdílených složek souborů je 5 TiB, najdete [v tématu Povolit a vytvořit velké sdílené složky](../articles/storage/files/storage-files-how-to-create-large-file-share.md) pro podrobnosti o tom, jak zvýšit standardní sdílené složky měřítko až 100 TiB.
