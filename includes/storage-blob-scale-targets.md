---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392260"
---
| Prostředek | Cíl        |
|----------|---------------|
| Maximální velikost kontejneru s jedním objektem blob | Stejné jako maximální kapacita účtu úložiště |
| Maximální počet bloků v objektu blob bloku nebo objektu blob připojení | 50 000 bloků |
| Maximální velikost bloku v bloku blob | 100 MiB |
| Maximální velikost objektu blob bloku | 50 000 X 100 MiB (přibližně 4,75 TiB) |
| Maximální velikost bloku v objektu blob připojení | 4 MiB |
| Maximální velikost objektu blob připojení | 50 000 x 4 MiB (přibližně 195 GiB) |
| Maximální velikost objektu blob stránky | 8 TiB |
| Maximální počet uložených zásad přístupu na kontejner objektů blob | 5 |
|Cílová míra požadavků pro jeden objekt blob | Až 500 požadavků za sekundu |
|Cílová propustnost pro jeden objekt blob stránky | Až 60 MiB za sekundu |
|Cílová propustnost pro jeden blok blob |Až do omezení příchozího přenosu dat/odchozího přenosu<sup>dat 1</sup> |

<sup>1</sup> Propustnost pro jeden objekt blob závisí na několika faktorech, včetně, ale bez omezení na: souběžnost, velikost požadavku, úroveň výkonu, rychlost zdroje pro nahrávání a cíl pro stahování. Chcete-li využít vylepšení výkonu [objektů BLOB bloků s vysokou propustností](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), nahrajte větší objekty BLOB nebo bloky. Konkrétně volání [put objektu blob](/rest/api/storageservices/put-blob) nebo [put bloku](/rest/api/storageservices/put-block) operace s objektem blob nebo velikost bloku, který je větší než 4 MiB pro účty standardní úložiště. Pro objekt blob premium bloku nebo pro účty úložiště Storage Storage Data Lake Storage Gen2 použijte velikost bloku nebo objektu blob, který je větší než 256 KiB.
