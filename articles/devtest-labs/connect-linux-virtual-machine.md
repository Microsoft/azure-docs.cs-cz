---
title: Připojte se k virtuálním počítačům Linux v Azure DevTest Labs
description: Informace o tom, jak se připojit k virtuálnímu počítači se systémem Linux v testovacím prostředí (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86531270"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Připojení k virtuálnímu počítači se systémem Linux v testovacím prostředí (Azure DevTest Labs)
V tomto článku se dozvíte, jak se připojit k virtuálnímu počítači se systémem Linux v testovacím prostředí. 

## <a name="connect-to-a-linux-vm"></a>Připojení k virtuálnímu počítači s Linuxem
1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu hledání vyhledejte a vyberte **DevTest Labs**. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="Vyhledejte a vyberte DevTest Labs.":::    
1. V seznamu cvičení vyberte **testovací prostředí**.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Výběr testovacího prostředí":::            
1. Na domovské stránce testovacího prostředí vyberte ze seznamu **moje virtuální počítače** svůj virtuální počítač se systémem Linux. 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Výběr virtuálního počítače se systémem Linux":::        
5. Na stránce **Přehled** můžete zobrazit plně kvalifikovaný název domény (FQDN) nebo IP adresu virtuálního počítače. Můžete také zobrazit port, jak je znázorněno na následujícím obrázku.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="Plně kvalifikovaný název domény pro virtuální počítač":::    

    Všimněte si, že tlačítko **připojit** je šedé, i když je virtuální počítač spuštěný. To je záměrné.
6.  Pomocí SSH se připojte k VIRTUÁLNÍmu počítači Linux. Následující příklad se připojí k virtuálnímu počítači s plně kvalifikovaným názvem domény `mydtl07172452621450000.eastus.cloudapp.azure.com` s uživatelským jménem `vmuser` a portem `51637` . Zadejte heslo pro uživatele, které se má připojit k virtuálnímu počítači. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    K připojení k virtuálnímu počítači můžete použít nástroje, jako [je například výstup, nebo jakýkoli](https://www.putty.org/) jiný klient SSH. 

    Po připojení pomocí protokolu SSH můžete nainstalovat a nakonfigurovat desktopové prostředí ([desktop Xfce](https://www.xfce.org)) a vzdálenou plochu ([xrdp](http://xrdp.org)).  Podrobné informace najdete v tématu [instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači se systémem Linux v Azure](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="next-steps"></a>Další kroky
[Postupy: připojení k virtuálnímu počítači s Windows](connect-windows-virtual-machine.md)
