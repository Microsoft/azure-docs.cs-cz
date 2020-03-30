---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175012"
---
## <a name="use-the-azure-portal"></a>Použití webu Azure Portal
1. Vyberte virtuální hovirtuální ho disponiál, který chcete znovu nasadit, a pak vyberte tlačítko *Znovu nasadit* v okně *Nastavení.* Možná budete muset posunout dolů, abyste viděli část **Podpora a řešení potíží,** která obsahuje tlačítko Znovu nasadit jako v následujícím příkladu:
   
    ![Okno Azure VM](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Chcete-li operaci potvrdit, vyberte tlačítko *Znovu nasadit:*
   
    ![Opětovné nasazení okna virtuálního zařízení](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. **Stav** virtuálního počítače se změní na *Aktualizace* jako virtuální počítač připravuje na opětovné nasazení, jak je znázorněno v následujícím příkladu:
   
    ![Aktualizace virtuálního zařízení](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. **Stav** se pak změní na *Spuštění* jako spuštění virtuálního počítače na novém hostiteli Azure, jak je znázorněno v následujícím příkladu:
   
    ![Spuštění virtuálního virtuálního montovna](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Po dokončení procesu spouštění virtuálního virtuálního aplikace se **stav** vrátí na *spuštěný*, což znamená, že virtuální ho byl úspěšně znovu nasazen:
   
    ![Spuštěný virtuální virtuální mísa](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

