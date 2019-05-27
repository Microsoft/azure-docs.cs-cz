---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114828"
---
| Resource | Cíl        |
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

<sup>1</sup> jeden objekt propustnost závisí na několika různými faktory, včetně, ale nikoli výhradně: souběžnosti, velikost požadavku, úroveň výkonu, rychlost zdroje pro nahrávání a cílové soubory ke stažení. Abyste mohli využívat [objektů blob bloku vysoce propustné](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) vylepšení výkonu, použijte velikost > 4 MiB (> 256 KiB pro úložiště objektů blob bloku výkon úrovně premium nebo pro Data Lake Storage Gen2) požadavku Put Blob nebo Vložit blok.
