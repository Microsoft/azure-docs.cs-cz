---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 2936fd318f08c74675f7e8b382c861f4a28319fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386314"
---
Počet datových disků můžete připojit k virtuálnímu počítači Azure. Založené na škálovatelnost a výkonnostní cíle pro datové disky Virtuálního počítače, můžete určit počet a typ disku, který je potřeba splnit vaše požadavky na kapacitu a výkon.

> [!IMPORTANT]
> Pro optimální výkon omezte počet vysoce využívaných disků připojených k virtuálnímu počítači, abyste zabránili možnému omezení. Pokud všech připojených discích nejsou využívá vysoce ve stejnou dobu, virtuální počítač může podporovat větší počet disků.

**Azure managed disks:**

Následující tabulka ukazuje výchozí a maximální počet prostředků na oblast a předplatné

> | Prostředek | Výchozí omezení  | Maximální omezení |
> | --- | --- | --- |
> | Spravované disky úrovně Standard | 25,000 | 50,000 |
> | Spravované disky SSD úrovně Standard | 25,000 | 50,000 |
> | Spravované disky úrovně Premium | 25,000 | 50,000 |
> | Standard_LRS snímky | 25,000 | 50,000 |
> | Standard_ZRS snímky | 25,000 | 50,000 |
> | Spravované image | 25,000 | 50,000 |

* **Pro účty úložiště úrovně Standard:** Účet úložiště úrovně Standard se maximální celkovou frekvenci požadavků 20 000 IOPS. Celkový počet IOPS na všech disků virtuálního počítače v účtu úložiště úrovně Standard by neměl překročit toto omezení.
  
    Si můžete přibližně spočítat počet vysoce využívaných disků podporuje jeden standardní účet úložiště podle omezení frekvence požadavků. Například pro základní úroveň virtuálního počítače, maximální počet vysoce využívaných disků je asi 66, což je 20 000/300 IOPS na disk. Maximální počet vysoce využívaných disků pro virtuální počítač úrovně Standard je přibližně 40, což je 20 000/500 IOPS na disk. 

* **Pro účty služby Premium storage:** Účet Premium storage má maximální celkovou propustnost 50 GB/s. Celková propustnost všech disků virtuálních počítačů by neměla překročit toto omezení.

