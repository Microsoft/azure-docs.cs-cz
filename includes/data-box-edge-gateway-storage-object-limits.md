---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175460"
---
Tady jsou velikosti objektů Azure, které se dají zapsat. Zajistěte, aby všechny nahrané soubory splňovaly tato omezení.

| Typ objektu Azure | Limit nahrávání                                             |
|-------------------|-----------------------------------------------------------|
| Objekt blob bloku        | ~ 4,75 TB                                                 |
| Objekt blob stránky         | 1 TB <br> Každý soubor nahraný ve formátu objektu blob stránky musí být 512 bajtů (celočíselný násobek), jinak se nahrávání nepovede. <br> VHD a VHDX jsou zarovnané 512 bajtů. |
| Soubory Azure         | 1 TB <br> Každý soubor nahraný ve formátu objektu blob stránky musí být 512 bajtů (celočíselný násobek), jinak se nahrávání nepovede. <br> VHD a VHDX jsou zarovnané 512 bajtů. |

> [!IMPORTANT]
> Vytváření souborů (bez ohledu na typ úložiště) je povoleno až do 5 TB. Pokud však vytvoříte soubor, jehož velikost je větší než limit nahrávání definovaný v předchozí tabulce, soubor se nenačte. Aby bylo možné místo uvolnit, je nutné ručně odstranit soubor.