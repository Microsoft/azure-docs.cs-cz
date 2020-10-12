---
title: Připojte se k virtuálním počítačům s Windows v Azure DevTest Labs
description: Informace o tom, jak se připojit k virtuálnímu počítači s Windows v testovacím prostředí (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86540679"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>Připojení k virtuálnímu počítači s Windows v testovacím prostředí (Azure DevTest Labs)
V tomto článku se dozvíte, jak se připojit k virtuálnímu počítači s Windows v testovacím prostředí. 

## <a name="connect-to-a-windows-vm"></a>Připojení k virtuálnímu počítači s Windows
1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na panelu hledání vyhledejte a vyberte **DevTest Labs**. 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="Vyhledejte a vyberte DevTest Labs.":::    
1. V seznamu cvičení vyberte **testovací prostředí**.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="Vyhledejte a vyberte DevTest Labs.":::            
1. Na domovské stránce testovacího prostředí vyberte svůj virtuální počítač s Windows ze seznamu **moje virtuální počítače** . 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="Vyhledejte a vyberte DevTest Labs.":::                
1. Na stránce **virtuálního počítače** pro váš virtuální počítač vyberte **připojit** na panelu nástrojů. Pokud je virtuální počítač zastavený, vyberte **Spustit** jako první a spusťte virtuální počítač.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="Vyhledejte a vyberte DevTest Labs.":::                    
1. Pokud používáte prohlížeč Edge, zobrazí se v dolní části prohlížeče odkaz na **stažený soubor RDP** . 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="Vyhledejte a vyberte DevTest Labs.":::                        
1. Otevřete soubor RDP a zadejte přihlašovací údaje k VIRTUÁLNÍmu počítači, které jste zadali při vytváření virtuálního počítače. Nyní byste měli být připojeni k virtuálnímu počítači s Windows. 

## <a name="next-steps"></a>Další kroky
[Postupy: připojení k virtuálnímu počítači se systémem Linux](connect-linux-virtual-machine.md)
