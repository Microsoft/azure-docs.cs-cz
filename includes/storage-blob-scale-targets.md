---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "75392260"
---
| Prostředek | Cíl        |
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

<sup>1</sup> propustnost pro jeden objekt BLOB závisí na několika faktorech, mimo jiné: souběžnost, velikost požadavku, úroveň výkonu, rychlost zdroje pro nahrávání a cíl pro stažení. Pokud chcete využít vylepšení výkonu pro [objekty blob bloku s vysokou propustností](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), nahrajte větší objekty blob nebo bloky. Konkrétně zavolejte operaci [Put BLOB](/rest/api/storageservices/put-blob) nebo [Put Block](/rest/api/storageservices/put-block) s objektem BLOB nebo velikostí bloku, který je větší než 4 MIB pro standardní účty úložiště. V případě objektu blob bloku Premium nebo Data Lake Storage Gen2 účtů úložiště použijte velikost bloku nebo objektu blob, který je větší než 256 KiB.
