---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: d96f400332b7953b34a157b3b52cf00bb20db76e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012492"
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

<sup>1</sup> jeden objekt propustnost závisí na několika různými faktory, včetně, ale nikoli výhradně: souběžnosti, velikost požadavku, úroveň výkonu, rychlost zdroje pro nahrávání a cílové soubory ke stažení. Abyste mohli využívat [objektů blob bloku vysoce propustné](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) vylepšení výkonu, použijte velikost > 4 MiB (> 256 MiB pro úložiště objektů blob bloku výkon úrovně premium nebo pro Data Lake Storage Gen2) požadavku Put Blob nebo Vložit blok.