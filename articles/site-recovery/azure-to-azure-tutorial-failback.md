---
title: Selhání obnovení virtuálních počítačů Azure replikovaných do sekundární oblasti Azure pro zotavení po havárii pomocí služby Azure Site Recovery.
description: Zjistěte, jak převzít služby při obnovení virtuálních počítačů Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: tutorial
origin.date: 03/18/2019
ms.date: 04/22/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: c8ce05e644ad556542314b17151b808586734824
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118175"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Selhání obnovení virtuálních počítačů Azure mezi oblastmi Azure

[Azure Site Recovery](site-recovery-overview.md) služby přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrátit služby po zpět z místních počítačů a Azure virtual machines (VM).

Tento kurz popisuje, jak navrátit služby po obnovení jednoho virtuálního počítače Azure. Po převzetí služeb při selhání navrátíte služby po obnovení do primární oblasti, až bude dostupná. V tomto kurzu se naučíte:

> [!div class="checklist"]
> 
> * Navrácení služeb po obnovení virtuálního počítače Azure v sekundární oblasti.
> * Znovunastavení ochrany primárního virtuálního počítače Azure zpět do sekundární oblasti.
> 
> [!NOTE]
> 
> V tomto kurzu vám umožní převzít služby několika virtuálních počítačů v cílové oblasti a zpátky do zdrojové oblasti s minimální přizpůsobení. Další podrobné pokyny projděte si články v části "Jak na" pro virtuální počítače Azure.

## <a name="before-you-start"></a>Než začnete

> * Ujistěte se, že je virtuální počítač v **převzetí služeb při selhání potvrzeno** stavu.
> * Zkontrolujte, zda je k dispozici primární oblasti a budete moci vytvářet a přistupovat k nové prostředky v ní.
> * Ujistěte se, že je tato opětovná ochrana povolená.

## <a name="fail-back-to-the-primary-region"></a>Navrácení služeb po obnovení do primární oblasti

Po virtuální počítače se znovu zapnula, můžete navrátit služby zpět do primární oblasti podle potřeby.

1. V trezoru, klikněte na **replikované položky** a vyberte virtuální počítač, který se znovu zapnula ochrana.

    ![Navrácení služeb po obnovení primární](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Klikněte na tlačítko **testovací převzetí služeb při selhání** Pokud chcete provést test převzetí služeb při selhání zpátky do primární oblasti.
4. Vyberte bod obnovení a virtuální sítě pro testovací převzetí služeb a klikněte na tlačítko **OK**. Můžete zkontrolovat testu, které vytvořili virtuální počítač v primární oblasti.
5. Po úspěšném dokončení testovacího převzetí služeb při selhání, klikněte na tlačítko **vyčištění testovacího převzetí služeb při selhání** chcete vyčistit prostředky vytvořené v rámci zdrojové oblasti pro testovací převzetí služeb.
6. V **replikované položky**, vyberte virtuální počítač a klikněte na tlačítko **převzetí služeb při selhání**.
7. V **převzetí služeb při selhání**, vyberte bod obnovení pro převzetí služeb při selhání.
    - **Nejnovější (výchozí)**: Zpracuje všechna data ve službě Site Recovery a poskytuje nejnižší cíl bodu obnovení (RPO).
    - **Nejnovější zpracovaný**: Obnoví virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery.
    - **Vlastní**: Převezme služby při selhání do konkrétnímu bodu obnovení. Tato možnost je užitečná při provádění testovacího převzetí služeb při selhání.

8. Vyberte **před spuštěním převzetí služeb při selhání vypnout počítač** Pokud chcete, aby Site Recovery chcete pokusit před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Všimněte si, že Site Recovery nepodporuje čištění zdroji po převzetí služeb při selhání.
9. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
10. Po převzetí služeb při selhání ověřte tak, že se k němu připojíte virtuální počítač. Podle potřeby můžete změnit bod obnovení.
11. Jakmile ověříte, převzetí služeb při selhání, klikněte na tlačítko **potvrzení převzetí služeb**. Potvrzují se odstraní všechny dostupné body obnovení. Možnost změnit obnovení bodu již není k dispozici.
12. Virtuální počítač by měl zobrazit, protože převzetí služeb při selhání a navrácení služeb obnovit.

    ![Virtuální počítač v primární a sekundární oblasti](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Zotavení po havárii virtuálních počítačů zůstane v vypnutí navrácena stavu. Jedná se o účel, protože Site Recovery ukládá informace o virtuálních počítačů, které může být užitečná pro převzetí služeb při selhání z primární do sekundární oblasti později. Se vám neúčtují poplatky pro uvolnění virtuální počítače, takže by měly být neustále jsou.

## <a name="next-steps"></a>Další postup

[Další informace](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o opětovného nastavování ochrany toku.

<!--Update_Description: new articles on azure to azure tutorial failback -->
<!--ms.date: 04/22/2019-->