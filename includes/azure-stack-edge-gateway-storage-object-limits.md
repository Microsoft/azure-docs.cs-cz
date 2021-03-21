---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 10d6ef2c90390f08e38726363416493f937de8f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466750"
---
Tady jsou velikosti objektů Azure, které se dají zapsat. Zajistěte, aby všechny nahrané soubory splňovaly tato omezení.

| Typ objektu Azure | Limit nahrávání                                             |
|-------------------|-----------------------------------------------------------|
| Objekt blob bloku        | 4,75 TB                                                 |
| Objekt blob stránky         | 1 TB <br> Každý soubor nahraný ve formátu objektu blob stránky musí být 512 bajtů (celočíselný násobek), jinak se nahrávání nepovede. <br> VHD a VHDX jsou zarovnané 512 bajtů. |
| Azure Files         | 1 TB <br> Každý soubor nahraný ve formátu objektu blob stránky musí být 512 bajtů (celočíselný násobek), jinak se nahrávání nepovede. <br> VHD a VHDX jsou zarovnané 512 bajtů. |

> [!IMPORTANT]
> Vytváření souborů (bez ohledu na typ úložiště) je povoleno až do 5 TB. Pokud však vytvoříte soubor, jehož velikost je větší než limit nahrávání definovaný v předchozí tabulce, soubor se nenačte. Aby bylo možné místo uvolnit, je nutné ručně odstranit soubor.
