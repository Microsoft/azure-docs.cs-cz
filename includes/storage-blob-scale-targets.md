---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/11/2019
ms.author: tamram
ms.openlocfilehash: b3e2f018a3f1ba2563ba8cf2df6dfd4959be592e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59736986"
---
| Prostředek | Cíl        |
|----------|---------------|
| Maximální velikost kontejneru jeden objekt blob | Stejná jako maximální účet kapacita úložiště |
| Maximální počet objektů blob bloků v bloku nebo doplňovacího objektu blob | 50 000 bloků |
| Maximální velikost bloku do objektu BLOB bloku | 100 MiB |
| Maximální velikost objektu blob bloku | 50 000 × 100 MiB (přibližně 4,75 TB) |
| Maximální velikost blok v doplňovacím objektu blob | 4 MiB |
| Maximální velikost doplňovacího objektu BLOB | 50 000 × 4 MiB (přibližně 195 GB) |
| Maximální velikost objektu blob stránky | 8 TiB |
| Maximální počet uložené zásady přístupu na kontejner objektů blob | 5 |
|Nastavte propustnost pro jeden objekt blob |Až do omezení příchozí a odchozí přenos účtu úložiště<sup>1</sup> |

<sup>1</sup> jeden objekt propustnost závisí na několika různými faktory, včetně, ale nikoli výhradně: souběžnosti, velikost operace, úroveň výkonu, rychlost zdroje pro nahrávání a cílové soubory ke stažení.