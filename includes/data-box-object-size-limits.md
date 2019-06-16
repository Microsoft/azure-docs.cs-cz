---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244565"
---
Tady jsou velikosti Azure objekty, které je možné zapisovat. Ujistěte se, že všechny soubory, které jsou odeslány odpovídají tyto limity.

| Typ objektu Azure | Výchozí omezení                                             |
|-------------------|-----------------------------------------------------------|
| Objekt blob bloku        | ~ 4,75 TB                                                 |
| Objekt blob stránky         | 8 TiB <br> Každý soubor odeslat ve formátu objektů blob stránky musí být zarovnaná 512 bajtů (integrální více), jinak se odeslání nezdaří. <br> VHD a VHDX jsou 512 bajtů zarovnána. |
| Soubory Azure        | 1 TiB                                                      |
| Spravované disky     | 4 TiB <br> Další informace o velikosti a omezení naleznete v tématu: <li>[Cíle škálovatelnosti standardních disků SSD](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Cíle škálovatelnosti disků Premium SSD](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Cíle škálovatelnosti standardní HDD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Ceny a fakturace spravovaných disků](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
