---
title: Navrácení služeb po obnovení virtuálních počítačů Azure do primární oblasti pomocí služby Azure Site Recovery.
description: V této části najdete popis postupu při navrácení služeb virtuálních počítačů Azure do primární oblasti pomocí služby Azure Site Recovery Service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 432c92bcfa8a2e0df26adf1516f5bdc9ee73d267
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502371"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Navrácení služeb po obnovení virtuálního počítače Azure mezi oblastmi Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá k strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení místních počítačů a virtuálních počítačů Azure (VM).

V tomto kurzu se dozvíte, jak provést převzetí služeb při selhání jednoho virtuálního počítače Azure. Po převzetí služeb při selhání musíte navrátit služby po obnovení do primární oblasti, až bude k dispozici. V tomto kurzu se naučíte:

> [!div class="checklist"]
> 
> * Navrácení služeb po obnovení virtuálního počítače v sekundární oblasti.
> * Znovu nastavte ochranu primárního virtuálního počítače zpátky do sekundární oblasti.
> 
> [!NOTE]
> 
> Tento kurz vám pomůže převzít služby při selhání několika virtuálních počítačů do cílové oblasti a zpátky do zdrojové oblasti s minimálními úpravami. Podrobnější informace najdete v tématu [návody k virtuálním počítačům Azure](../virtual-machines/windows/index.yml).

## <a name="before-you-start"></a>Než začnete

* Ujistěte se, že stav virtuálního počítače je **převzetí služeb při selhání potvrzené**.
* Ověřte, zda je primární oblast dostupná a zda je možné vytvořit nové prostředky a přistupovat k nim.
* Ujistěte se, že je povolena ochrana.

## <a name="fail-back-to-the-primary-region"></a>Navrácení služeb po obnovení do primární oblasti

Po opětovném zapnutí ochrany virtuálních počítačů můžete v případě potřeby navrátit služby po obnovení do primární oblasti.

1. V trezoru vyberte **replikované položky**a potom vyberte virtuální počítač, který se znovu zabezpečí.

    ![Snímek obrazovky s navrácením služeb po obnovení na primární v Azure Portal.](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. V části **replikované položky**vyberte virtuální počítač a pak vyberte **převzetí služeb při selhání**.
3. V části **převzetí služeb při selhání**vyberte bod obnovení, který převezme služby při selhání:
    - **Nejnovější (výchozí)**: zpracovává všechna data ve službě Site Recovery a poskytuje nejnižší cíl bodu obnovení (RPO).
    - **Poslední zpracování**: vrátí virtuální počítač k nejnovějšímu bodu obnovení, který byl zpracován Site Recovery.
    - **Vlastní**: převzetí služeb při selhání pro určitý bod obnovení. Tato možnost je užitečná při provádění testovacího převzetí služeb při selhání.
4. Vyberte **vypnout počítač před spuštěním převzetí služeb při selhání** , pokud chcete, aby se Site Recovery před aktivací převzetí služeb při selhání vypnul virtuální počítače v oblasti zotavení po havárii. Převzetí služeb při selhání pokračuje i v případě selhání vypnutí. 
5. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
6. Po dokončení převzetí služeb při selhání ověřte virtuální počítač tím, že se k němu přihlásíte. Bod obnovení můžete podle potřeby změnit.
7. Po ověření převzetí služeb při selhání vyberte **Potvrdit převzetí služeb při selhání**. Potvrzení odstraní všechny dostupné body obnovení. Možnost změnit bod obnovení již není k dispozici.
8. Virtuální počítač by se měl zobrazit při převzetí služeb při selhání a vrácení se zpět.

    ![Snímek obrazovky, který ukazuje virtuální počítač v primárních a sekundárních oblastech](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Pro počítače, které používají spravované disky a verze rozšíření Site Recovery 9.28. x. x. x. x. [kumulativní aktualizace 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) Site Recovery vyčistí počítače v sekundární oblasti zotavení po havárii, po dokončení navrácení služeb po obnovení a virtuální počítače budou znovu chráněny. Nemusíte ručně odstraňovat virtuální počítače a síťové adaptéry v sekundární oblasti. Upozorňujeme, že virtuální počítače s nespravovanými disky se nevyčistí. Pokud po převzetí služeb při selhání dojde k úplnému vypnutí replikace, Site Recovery kromě virtuálních počítačů a síťových karet vyčistí disky v oblasti zotavení po havárii.

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o toku ochrany.
