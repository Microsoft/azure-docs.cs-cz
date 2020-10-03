---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.openlocfilehash: fe493ca4a2edf16bfda405e8b2c5581c0f745616
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665588"
---
| Prostředek | Cíl | Cíl (Preview) |
|-|-|-|
| Maximální velikost jednoho kontejneru objektů BLOB | Stejné jako maximální kapacita účtu úložiště |  |
| Maximální počet bloků v objektu blob bloku nebo doplňovacím objektu BLOB | bloky 50 000 |  |
| Maximální velikost bloku v objektu blob bloku | 100 MiB | 4000 MiB (Preview) |
| Maximální velikost objektu blob bloku | 50 000 X 100 MiB (přibližně 4,75 TiB) | 50 000 X 4000 MiB (přibližně 190,7 TiB) (Preview) |
| Maximální velikost bloku v doplňovacím objektu BLOB | 4 MiB |  |
| Maximální velikost doplňovacího objektu BLOB | 50 000 x 4 MiB (přibližně 195 GiB) |  |
| Maximální velikost objektu blob stránky | 8 TiB<sup>2</sup> |  |
| Maximální počet uložených zásad přístupu na kontejner objektů BLOB | 5 |  |
| Frekvence cílových požadavků pro jeden objekt BLOB | Až 500 požadavků za sekundu |  |
| Cílová propustnost pro objekt BLOB s jednou stránkou | Až 60 MiB za sekundu<sup>2</sup> |  |
| Cílová propustnost pro jeden objekt blob bloku | Až do účtu úložiště – omezení pro vstup a výstup<sup>1</sup> |  |

<sup>1</sup> propustnost pro jeden objekt BLOB závisí na několika faktorech, mimo jiné: souběžnost, velikost požadavku, úroveň výkonu, rychlost zdroje pro nahrávání a cíl pro stažení. Pokud chcete využít vylepšení výkonu pro [objekty blob bloku s vysokou propustností](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), nahrajte větší objekty blob nebo bloky. Konkrétně zavolejte operaci [Put BLOB](/rest/api/storageservices/put-blob) nebo [Put Block](/rest/api/storageservices/put-block) s objektem BLOB nebo velikostí bloku, který je větší než 4 MIB pro standardní účty úložiště. V případě objektu blob bloku Premium nebo Data Lake Storage Gen2 účtů úložiště použijte velikost bloku nebo objektu blob, který je větší než 256 KiB.

<sup>2</sup> objekty blob stránky ještě nejsou podporované v účtech s nastavením **hierarchického oboru názvů** .

Následující tabulka popisuje maximální velikost bloku a objektů BLOB povolených ve verzi služby.

| Verze služby | Maximální velikost bloku (pomocí bloku PUT) | Maximální velikost objektu BLOB (přes seznam blokovaných bloků) | Maximální velikost objektu BLOB přes jednu operaci zápisu (prostřednictvím objektu BLOB PUT) |
|-|-|-|-|
| Verze 2019-12-12 a novější | 4000 MiB (Preview) | Přibližně 190,7 TiB (4000 MiB X 50 000 bloků) (Preview) | 5000 MiB (Preview) |
| Verze 2016-05-31 až verze 2019-07-07 | 100 MiB | Přibližně 4,75 TiB (100 MiB X 50 000 bloků) | 256 MiB |
| Verze starší než 2016-05-31 | 4 MiB | Přibližně 195 GiB (4 bloky MiB X 50 000) | 64 MiB |
