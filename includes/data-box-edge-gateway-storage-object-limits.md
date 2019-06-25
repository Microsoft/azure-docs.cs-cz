---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175460"
---
Tady jsou velikosti Azure objekty, které je možné zapisovat. Ujistěte se, že všechny soubory, které jsou odeslány odpovídají tyto limity.

| Typ objektu Azure | Limit pro nahrávání                                             |
|-------------------|-----------------------------------------------------------|
| Objekt Blob bloku        | ~ 4,75 TB                                                 |
| Page Blob         | 1 TB <br> Každý soubor odeslat ve formátu objektů Blob stránky musí být zarovnaná 512 bajtů (integrální více), jinak se odeslání nezdaří. <br> VHD a VHDX jsou 512 bajtů zarovnána. |
| Soubory Azure         | 1 TB <br> Každý soubor odeslat ve formátu objektů Blob stránky musí být zarovnaná 512 bajtů (integrální více), jinak se odeslání nezdaří. <br> VHD a VHDX jsou 512 bajtů zarovnána. |

> [!IMPORTANT]
> Vytváření souborů (bez ohledu na typ úložiště) je povoleno maximálně 5 TB. Nicméně pokud vytvoříte soubor, jehož velikost je větší než limit nahrávání definovaný v předchozí tabulce, soubor získat nenahrály. Budete muset uvolnit místo soubor odstraňte ručně.