---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175012"
---
## <a name="use-the-azure-portal"></a>Použití webu Azure Portal
1. Vyberte virtuální počítač, který chcete znovu nasadit, a pak v okně *Nastavení* vyberte tlačítko *znovu nasadit* . Možná se budete muset posunout dolů, aby se zobrazila část **Podpora a řešení potíží** , která obsahuje tlačítko pro opětovné nasazení, jako v následujícím příkladu:
   
    ![Okno virtuálního počítače Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Pro potvrzení operace vyberte tlačítko *znovu nasadit* :
   
    ![Znovu nasadit okno virtuálního počítače](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. **Stav** virtuálního počítače se změní na *aktualizaci* , protože virtuální počítač se připraví na opětovné nasazení, jak je znázorněno v následujícím příkladu:
   
    ![Aktualizace virtuálního počítače](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. **Stav** se pak změní na *spouštění* , protože se virtuální počítač spustí na novém hostiteli Azure, jak je znázorněno v následujícím příkladu:
   
    ![Spouští se virtuální počítač](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Jakmile virtuální počítač dokončí proces spuštění, **stav** se pak vrátí na *spuštěno*a indikuje, že se virtuální počítač úspěšně znovu nasadil:
   
    ![Spuštěný virtuální počítač](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

