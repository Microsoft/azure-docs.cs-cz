---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244565"
---
Tady jsou velikosti objektů Azure, které se dá zapsat. Ujistěte se, že všechny soubory, které jsou odeslány, odpovídají těmto limitům.

| Typ objektu Azure | Výchozí omezení                                             |
|-------------------|-----------------------------------------------------------|
| Objekt blob bloku        | ~ 4,75 TiB                                                 |
| Objekt blob stránky         | 8 TiB <br> Každý soubor nahraný ve formátu objektu blob stránky musí být zarovnán 512 bajtů (integrální násobek), jinak se nahrávání nezdaří. <br> VHD a VHDX jsou zarovnány o 512 bajtů. |
| Soubory Azure        | 1 TiB                                                      |
| Spravované disky     | 4 TiB <br> Další informace o velikosti a omezeních naleznete v těchto tématech: <li>[Cíle škálovatelnosti standardních ssd distek](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Cíle škálovatelnosti prémiových ssd dispozicí SSD](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Cíle škálovatelnosti standardních pevných disků](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Stanovení cen a fakturace spravovaných disků](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
