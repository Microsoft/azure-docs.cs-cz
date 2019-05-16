---
title: Selhání obnovení virtuálních počítačů Azure replikovaných do sekundární oblasti Azure pro zotavení po havárii pomocí služby Azure Site Recovery.
description: Zjistěte, jak převzít služby při obnovení virtuálních počítačů Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9bc2bd50fda34b493463898d8ef47ed2c246b7d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65751155"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Navrácení služeb po obnovení virtuálního počítače Azure mezi oblastmi Azure

[Azure Site Recovery](site-recovery-overview.md) služby přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení z místních počítačů a Azure virtual machines (VM).

Tento kurz popisuje, jak navrátit služby po obnovení jednoho virtuálního počítače Azure. Po převzetí služeb jste při selhání, musíte navrátit služby po obnovení do primární oblasti až bude k dispozici. V tomto kurzu se naučíte:

> [!div class="checklist"]
> 
> * Navrácení služeb po obnovení virtuálního počítače v sekundární oblasti.
> * Znovunastavení ochrany primárního virtuálního počítače do sekundární oblasti.
> 
> [!NOTE]
> 
> V tomto kurzu vám umožní převzít služby několika virtuálních počítačů v cílové oblasti a zpátky do zdrojové oblasti s minimální přizpůsobení. Další podrobné pokyny najdete v tématu [postupy provede na virtuálních počítačích Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Než začnete

* Ujistěte se, zda je stav virtuálního počítače **převzetí služeb při selhání potvrzeno**.
* Zkontrolujte, že primární oblast je k dispozici a že budete moci vytvářet a přistupovat k nové prostředky v ní.
* Ujistěte se, že je povolená tohoto opětovného nastavování ochrany.

## <a name="fail-back-to-the-primary-region"></a>Navrácení služeb po obnovení do primární oblasti

Po znovunastavení ochrany virtuálních počítačů, můžete navrátit služby zpět do primární oblasti podle potřeby.

1. V trezoru, vyberte **replikované položky**a potom vyberte virtuální počítač, který byl znovunastavení ochrany.

    ![Navrácení služeb po obnovení primární](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Vyberte **testovací převzetí služeb při selhání** Pokud chcete provést test převzetí služeb při selhání zpátky do primární oblasti.
4. Vyberte bod obnovení a virtuální sítě pro testovací převzetí služeb a pak vyberte **OK**. Můžete zkontrolovat testu, které vytvořili virtuální počítač v primární oblasti.
5. Po úspěšném dokončení testu převzetí služeb vyberte **vyčištění testovacího převzetí služeb při selhání** chcete vyčistit prostředky vytvořené v rámci zdrojové oblasti pro testovací převzetí služeb.
6. V **replikované položky**, vyberte virtuální počítač a potom vyberte **převzetí služeb při selhání**.
7. V **převzetí služeb při selhání**, vyberte bod obnovení pro převzetí služeb při selhání pro:
    - **Nejnovější (výchozí)**: Zpracuje všechna data ve službě Site Recovery a poskytuje nejnižší cíl bodu obnovení (RPO).
    - **Nejnovější zpracovaný**: Obnoví virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery.
    - **Vlastní**: Převezme služby při selhání do konkrétnímu bodu obnovení. Tato možnost je užitečná při provádění testovacího převzetí služeb při selhání.

8. Vyberte **před spuštěním převzetí služeb při selhání vypnout počítač** Pokud chcete, aby Site Recovery se pokusit před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání pokračovat i v případě, že vypnutí nepovede. Všimněte si, že Site Recovery nepodporuje čištění zdroji po převzetí služeb při selhání.
9. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
10. Po dokončení převzetí služeb při selhání ověřte virtuální počítač tím, se k němu připojíte. Podle potřeby můžete změnit bod obnovení.
11. Jakmile ověříte, převzetí služeb při selhání, vyberte **potvrzení převzetí služeb**. Potvrzují se odstraní všechny dostupné body obnovení. Možnost změnit obnovení bodu již není k dispozici.
12. Virtuální počítač by měl zobrazit, protože převzetí služeb při selhání a navrácení služeb obnovit.

    ![Virtuální počítač v primární a sekundární oblasti](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Zotavení po havárii virtuálních počítačů zůstane ve stavu vypnout a uvolnit. Jedná se o účel, protože Site Recovery ukládá informace o virtuálních počítačů, které může být užitečná pro převzetí služeb při selhání z primární do sekundární oblasti později. Se vám neúčtují poplatky pro uvolnění virtuální počítače, takže by měly být neustále jsou.

## <a name="next-steps"></a>Další postup

[Další informace](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o opětovného nastavování ochrany toku.
