---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175460"
---
Tady jsou velikosti objektů Azure, které se dá zapsat. Ujistěte se, že všechny soubory, které jsou odeslány, odpovídají těmto limitům.

| Typ objektu Azure | Limit nahrávání                                             |
|-------------------|-----------------------------------------------------------|
| Objekt blob bloku        | ~ 4,75 TB                                                 |
| Objekt blob stránky         | 1 TB <br> Každý soubor nahraný ve formátu objektu Blob stránky musí mít zarovnané 512 bajtů (integrální násobek), jinak se nahrávání nezdaří. <br> VHD a VHDX jsou zarovnány o 512 bajtů. |
| Soubory Azure         | 1 TB <br> Každý soubor nahraný ve formátu objektu Blob stránky musí mít zarovnané 512 bajtů (integrální násobek), jinak se nahrávání nezdaří. <br> VHD a VHDX jsou zarovnány o 512 bajtů. |

> [!IMPORTANT]
> Vytváření souborů (bez ohledu na typ úložiště) je povoleno až do 5 TB. Pokud však vytvoříte soubor, jehož velikost je větší než limit pro odesílání definovaný v předchozí tabulce, soubor se nenahraje. Chcete-li uvolnit místo, musíte soubor odstranit ručně.