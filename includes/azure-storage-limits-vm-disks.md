---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334601"
---
K virtuálnímu počítači Azure můžete připojit několik datových disků. Na základě škálovatelnosti a výkonu cíle pro datové disky virtuálního počítače, můžete určit počet a typ disku, které potřebujete ke splnění požadavků na výkon a kapacitu.

> [!IMPORTANT]
> Chcete-li dosáhnout optimálního výkonu, omezte počet vysoce využitých disků připojených k virtuálnímu počítači, abyste se vyhnuli možnému omezení. Pokud všechny připojené disky nejsou vysoce využity ve stejnou dobu, virtuální počítač může podporovat větší počet disků.

**Pro spravované disky Azure:**

Následující tabulka znázorňuje výchozí a maximální limity počtu prostředků na oblast na předplatné. Počet spravovaných disků, snímků a bitových kopií na skupinu prostředků není nijak omezen.  

> | Prostředek | Omezení |
> | --- | --- |
> | Standardní spravované disky | 50 000 |
> | Disky spravované standardním SSD disky | 50 000 |
> | Prémiové spravované disky | 50 000 |
> | Standard_LRS snímky | 50 000 |
> | Standard_ZRS snímky | 50 000 |
> | Spravovaná bitová kopie | 50 000 |

* **Pro účty úložiště standard:** Účet úložiště Standard má maximální celkovou míru požadavků 20 000 VOPS. Celkový počet vité ops na všech vašich discích virtuálních počítačů v účtu standardního úložiště by neměl tento limit překročit.
  
    Můžete zhruba vypočítat počet vysoce využitých disků podporovaných jedním účtem úložiště Standard na základě limitu rychlosti požadavku. Například pro virtuální počítače úrovně Basic je maximální počet vysoce využívaných disků přibližně 66, což je 20 000/300 vstupně-výkonů na disk. Maximální počet vysoce využívaných disků pro virtuální počítače úrovně Standard je přibližně 40, což je 20 000/500 VOPS na disk. 

* **Pro účty úložiště Premium:** Účet úložiště Premium má maximální celkovou propustnost 50 Gb/s. Celková propustnost všech disků virtuálních počítačů by neměla překročit toto omezení.

