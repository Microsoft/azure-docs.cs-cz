---
title: Navrácení služeb po obnovení virtuálních počítačů Azure replikovaných do sekundární oblasti Azure za účelem zotavení po havárii pomocí služby Azure Site Recovery.
description: Přečtěte si, jak po obnovení virtuálních počítačů Azure pomocí služby Azure Site Recovery navracet služby po obnovení.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3910336efe50131fbd5df72039af345eb7346385
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383224"
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
> Tento kurz vám pomůže převzít služby při selhání několika virtuálních počítačů do cílové oblasti a zpátky do zdrojové oblasti s minimálními úpravami. Podrobnější informace najdete v tématu [návody k virtuálním počítačům Azure](https://docs.microsoft.com/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Než začnete

* Ujistěte se, že stav virtuálního počítače je **převzetí služeb při selhání potvrzené**.
* Ověřte, zda je primární oblast dostupná a zda je možné vytvořit nové prostředky a přistupovat k nim.
* Ujistěte se, že je povolena ochrana.

## <a name="fail-back-to-the-primary-region"></a>Navrácení služeb po obnovení do primární oblasti

Po opětovném zapnutí ochrany virtuálních počítačů můžete v případě potřeby navrátit služby po obnovení do primární oblasti.

1. V trezoru vyberte **replikované položky**a potom vyberte virtuální počítač, který se znovu zabezpečí.

    ![Navrácení služeb po obnovení primárnímu](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. V části **replikované položky**vyberte virtuální počítač a pak vyberte **převzetí služeb při selhání**.
3. V části **převzetí služeb při selhání**vyberte bod obnovení, který převezme služby při selhání:
    - **Nejnovější (výchozí)** : Zpracovává všechna data ve službě Site Recovery a poskytuje nejnižší cíl bodu obnovení (RPO).
    - **Poslední zpracovaná**: Vrátí virtuální počítač k nejnovějšímu bodu obnovení, který byl zpracován Site Recovery.
    - **Vlastní**: Převezme převzetí služeb při selhání konkrétního bodu obnovení. Tato možnost je užitečná při provádění testovacího převzetí služeb při selhání.
4. Vyberte **vypnout počítač před spuštěním převzetí služeb při selhání** , pokud chcete, aby se Site Recovery před aktivací převzetí služeb při selhání vypnul virtuální počítače v oblasti zotavení po havárii. Převzetí služeb při selhání pokračuje i v případě selhání vypnutí. 
5. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
6. Po dokončení převzetí služeb při selhání ověřte virtuální počítač tím, že se k němu přihlásíte. Bod obnovení můžete podle potřeby změnit.
7. Po ověření převzetí služeb při selhání vyberte **Potvrdit převzetí služeb při selhání**. Potvrzení odstraní všechny dostupné body obnovení. Možnost změnit bod obnovení již není k dispozici.
8. Virtuální počítač by se měl zobrazit při převzetí služeb při selhání a vrácení se zpět.

    ![Virtuální počítač v primárních a sekundárních oblastech](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

## <a name="next-steps"></a>Další postup

[Přečtěte si další informace](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o toku ochrany.
