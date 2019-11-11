---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 0fda881b805eb3a967cf3b05f6c6df8c65d20730
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905776"
---
| Prostředek | Výběr cílového umístění        |
|----------|---------------|
| Maximální velikost jednoho kontejneru objektů BLOB | Stejné jako maximální kapacita účtu úložiště |
| Maximální počet bloků v objektu blob bloku nebo doplňovacím objektu BLOB | bloky 50 000 |
| Maximální velikost bloku v objektu blob bloku | 100 MiB |
| Maximální velikost objektu blob bloku | 50 000 X 100 MiB (přibližně 4,75 TiB) |
| Maximální velikost bloku v doplňovacím objektu BLOB | 4 MiB |
| Maximální velikost doplňovacího objektu BLOB | 50 000 x 4 MiB (přibližně 195 GiB) |
| Maximální velikost objektu blob stránky | 8 TiB |
| Maximální počet uložených zásad přístupu na kontejner objektů BLOB | 5 |
|Frekvence cílových požadavků pro jeden objekt BLOB | Až 500 požadavků za sekundu |
|Cílová propustnost pro objekt BLOB s jednou stránkou | Až 60 MiB za sekundu |
|Cílová propustnost pro jeden objekt blob bloku |Až do účtu úložiště – omezení pro vstup a výstup<sup>1</sup> |

<sup>1</sup> propustnost jednoho objektu závisí na několika faktorech, včetně, ale ne omezení na: souběžnost, velikost požadavku, úroveň výkonu, rychlost zdroje pro nahrávání a cíl pro stažení. Pokud chcete využít vylepšení výkonu [objektů blob bloku s vysokou propustností](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) , použijte velikost žádosti o vložení objektu BLOB nebo bloku Put > 4 MiB (> 256 KiB pro úložiště objektů blob bloku úrovně Premium nebo pro data Lake Storage Gen2).
