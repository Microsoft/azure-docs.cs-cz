---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: e89676505bc211d01a4327f8816a048218e579fe
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829047"
---
K virtuálnímu počítači Azure můžete připojit několik datových disků. Na základě škálovatelnosti a výkonu pro datové disky virtuálního počítače můžete určit počet a typ disku, který budete potřebovat pro splnění požadavků na výkon a kapacitu.

> [!IMPORTANT]
> Pro zajištění optimálního výkonu omezte počet vysoce využívaných disků připojených k virtuálnímu počítači, abyste zabránili možnému omezení. Pokud se všechny připojené disky vysoce nevyužívají současně, může virtuální počítač podporovat větší počet disků.

**Pro Azure Managed disks:**

Následující tabulka ukazuje výchozí a maximální počet prostředků na oblast v rámci předplatného. Neexistuje žádné omezení počtu Managed Disks, snímků a obrázků na skupinu prostředků.  

> | Prostředek | Omezení |
> | --- | --- |
> | Managed disks úrovně Standard | 50,000 |
> | SSD úrovně Standard spravované disky | 50,000 |
> | Managed disks úrovně Premium | 50,000 |
> | Snímky Standard_LRS | 50,000 |
> | Snímky Standard_ZRS | 50,000 |
> | Spravovaná image | 50,000 |

**Pro účty úložiště úrovně Standard:** Účet úložiště úrovně Standard má maximální počet požadavků 20 000 IOPS. Celkový počet IOPS na všech discích virtuálního počítače v účtu úložiště úrovně Standard by neměl překročit tento limit.
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**Pro účty Premium Storage:** Účet Premium Storage má maximální míru propustnosti 50 GB/s. Celková propustnost všech disků virtuálních počítačů by neměla překročit toto omezení.

