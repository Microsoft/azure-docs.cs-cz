---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 8b25d2395811a2197aff6d653c5038a4380021e9
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669604"
---
K virtuálnímu počítači Azure můžete připojit několik datových disků. Na základě škálovatelnosti a výkonu pro datové disky virtuálního počítače můžete určit počet a typ disku, který budete potřebovat pro splnění požadavků na výkon a kapacitu.

> [!IMPORTANT]
> Pro zajištění optimálního výkonu omezte počet vysoce využívaných disků připojených k virtuálnímu počítači, abyste zabránili možnému omezení. Pokud se všechny připojené disky vysoce nevyužívají současně, může virtuální počítač podporovat větší počet disků.

**Pro Azure Managed disks:**

Následující tabulka ukazuje výchozí a maximální počet prostředků na oblast v rámci předplatného. Neexistuje žádné omezení počtu Managed Disks, snímků a obrázků na skupinu prostředků.  

> | Resource | Výchozí omezení  | Maximální omezení |
> | --- | --- | --- |
> | Managed disks úrovně Standard | 50,000 | 50,000 |
> | Spravované disky SSD úrovně Standard | 50,000 | 50,000 |
> | Spravované disky úrovně Premium | 50,000 | 50,000 |
> | Standard_LRS snímky | 50,000 | 50,000 |
> | Standard_ZRS snímky | 50,000 | 50,000 |
> | Spravovaná image | 50,000 | 50,000 |

* **Pro účty úložiště úrovně Standard:** Účet úložiště úrovně Standard má maximální počet požadavků 20 000 IOPS. Celkový počet IOPS na všech discích virtuálního počítače v účtu úložiště úrovně Standard by neměl překročit tento limit.
  
    V závislosti na limitu četnosti požadavků můžete zhruba vypočítat počet vysoce využívaných disků podporovaných jediným účtem úložiště Standard. Například pro virtuální počítač úrovně Basic je maximální počet vysoce využitých disků přibližně 66, což je 20000/300 IOPS na disk. Maximální počet vysoce využívaných disků pro virtuální počítač úrovně Standard je přibližně 40, což je 20000/500 IOPS na disk. 

* **Pro účty Premium Storage:** Účet Premium Storage má maximální míru propustnosti 50 GB/s. Celková propustnost všech disků virtuálních počítačů by neměla překročit toto omezení.

