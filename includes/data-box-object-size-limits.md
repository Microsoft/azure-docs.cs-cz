---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "66244565"
---
Tady jsou velikosti objektů Azure, které se dají zapsat. Zajistěte, aby všechny nahrané soubory splňovaly tato omezení.

| Typ objektu Azure | Výchozí omezení                                             |
|-------------------|-----------------------------------------------------------|
| Objekt blob bloku        | ~ 4,75 TiB                                                 |
| Objekt blob stránky         | 8 TiB <br> Každý soubor nahraný ve formátu objektu blob stránky musí být 512 bajtů (celočíselný násobek), jinak se nahrávání nepovede. <br> VHD a VHDX jsou zarovnané 512 bajtů. |
| Soubory Azure        | 1 TiB                                                      |
| Spravované disky     | 4 TiB <br> Další informace o velikosti a omezeních najdete v těchto tématech: <li>[Cíle škálovatelnosti úrovně Standard SSD](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Cíle škálovatelnosti Premium SSD](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Cíle škálovatelnosti úrovně Standard HDD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Ceny a fakturace spravovaných disků](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
