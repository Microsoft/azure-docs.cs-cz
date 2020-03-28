---
title: Navrácení služeb po obnovení virtuálních počítačích Azure do primární oblasti se službou Azure Site Recovery.
description: Popisuje, jak obnovit služby Azure virtuální počítače do primární oblasti se službou Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c27b7bf29e5f124fdcfb886b658fd8e9d4cc48fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74091350"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Navrácení služeb po selhání virtuálního počítače Azure mezi oblastmi Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a organizuje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení místních počítačů a virtuálních počítačů Azure.

Tento kurz popisuje, jak poslužbě zpět jeden virtuální počítač Azure. Po převzetí služeb při selhání je nutné po selhání vrátit se zpět do primární oblasti, jakmile bude k dispozici. V tomto kurzu se naučíte:

> [!div class="checklist"]
> 
> * Navrácení služeb po selhání virtuálního virtuálního zařízení v sekundární oblasti.
> * Znovu připevněte primární virtuální virtuální ho zpět do sekundární oblasti.
> 
> [!NOTE]
> 
> Tento kurz vám pomůže převzetí služeb při selhání několik virtuálních stránek do cílové oblasti a zpět do zdrojové oblasti s minimální mise. Podrobnější pokyny najdete v [návodech k použití na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Než začnete

* Ujistěte se, že stav virtuálního **virtuálního měkce**převzal .
* Zkontrolujte, zda je k dispozici primární oblast a zda je možné v ní vytvářet nové prostředky a přistupovat k nim.
* Ujistěte se, že je povolena reprotection.

## <a name="fail-back-to-the-primary-region"></a>Navrácení služeb po obnovení do primární oblasti

Po virtuálních počítačích jsou znovu chráněné, můžete znovu navrátit do primární oblasti podle potřeby.

1. V úschovně vyberte **Replikované položky**a pak vyberte virtuální počítač, který byl znovu chráněný.

    ![Navrácení služeb po selhání na primární](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. V **části Replikované položky**vyberte virtuální ms a pak vyberte **převzetí služeb při selhání**.
3. V **převzetí služeb při selhání**vyberte bod obnovení, na který chcete přepojit:
    - **Nejnovější (výchozí)**: Zpracuje všechna data ve službě Site Recovery a poskytuje nejnižší cíl bodu obnovení (RPO).
    - **Poslední zpracované**: Vrátí virtuální ho na nejnovější bod obnovení, který byl zpracován site recovery.
    - **Vlastní**: Převzetí služby při selhání do určitého bodu obnovení. Tato možnost je užitečná při provádění testovacího převzetí služeb při selhání.
4. Vyberte **Vypnout počítač před zahájením převzetí služeb při selhání,** pokud chcete, aby se obnovení webu pokusilo před spuštěním převzetí služeb při selhání o vypnutí virtuálních počítačů v oblasti zotavení po havárii. Převzetí služeb při selhání pokračuje i v případě, že se vypnutí nezdaří. 
5. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
6. Po dokončení převzetí služeb při selhání ověřte virtuální ho přihlášením. Bod obnovení můžete podle potřeby změnit.
7. Po ověření převzetí služeb při selhání vyberte **Potvrdit převzetí služeb při selhání**. Potvrzení odstraní všechny dostupné body obnovení. Možnost bodu obnovení změny již není k dispozici.
8. Virtuální ho virtuálního ms by se měl zobrazit jako převzetí selhání a selhání zpět.

    ![Virtuální voje v primárních a sekundárních oblastech](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> U počítačů se spuštěním rozšíření site recovery verze 9.28.x.x a dále [kumulativní aktualizace 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) Site Recovery vyčistí počítače v sekundární oblasti zotavení po havárii po dokončení obnovení po obnovení a virtuální počítače jsou znovu chráněny. Není nutné ručně odstraňovat virtuální počítače a síťové karty v sekundární oblasti. Pokud úplně zakázat replikaci po selhání zpět, site recovery vyčistí disky v oblasti zotavení po havárii, kromě virtuálních počítačů a nic.

## <a name="next-steps"></a>Další kroky

[Další informace](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o toku opětovné ochrany.
