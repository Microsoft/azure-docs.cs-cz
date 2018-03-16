---
title: "Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Azure replikovaných do sekundární oblasti Azure pomocí Azure Site Recovery (Preview)"
description: "Zjistěte, jak provést převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Azure replikovaných do sekundární oblasti Azure pomocí Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: dc7ead9e7d55d1b22118774e98c741991e8af2d9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions-preview"></a>Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Azure mezi oblastmi Azure (Preview)

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

Tento kurz popisuje, jak provést převzetí služeb při selhání jednoho virtuálního počítače Azure do sekundární oblasti Azure. Po převzetí služeb při selhání navrátíte služby po obnovení do primární oblasti, až bude dostupná. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Převzetí služeb při selhání virtuálního počítače Azure
> * Znovunastavení ochrany sekundárního virtuálního počítače Azure, aby se replikoval do primární oblasti
> * Navrácení služeb po obnovení sekundárního virtuálního počítače
> * Znovunastavení ochrany primárního virtuálního počítače do sekundární oblasti

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste dokončením [postupu zotavení po havárii](azure-to-azure-tutorial-dr-drill.md) zkontrolovali, že vše funguje podle očekávání.
- Před spuštěním testovacího převzetí služeb při selhání ověřte vlastnosti virtuálního počítače. Virtuální počítač musí splňovat [požadavky Azure](azure-to-azure-support-matrix.md#support-for-replicated-machine-os-versions).

## <a name="run-a-failover-to-the-secondary-region"></a>Spuštění převzetí služeb při selhání do sekundární oblasti

1. V části **Replikované položky** vyberte virtuální počítač, u kterého chcete provést převzetí služeb při selhání, a vyberte **Převzít služby při selhání**.

   ![Převzetí služeb při selhání](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:

   * **Nejnovější (výchozí):** Tato možnost zpracuje všechna data ve službě Site Recovery a zajistí nejnižší cíl bodu obnovení (RPO).
   * **Nejnovější zpracovaný:** Tato možnost vrátí virtuální počítač k nejnovějšímu uloženému bodu obnovení zpracovanému službou Site Recovery.
   * **Vlastní:** Tuto možnost použijte, pokud chcete převzít služby při selhání ke konkrétnímu bodu obnovení. Tato možnost je užitečná při provádění testovacího převzetí služeb při selhání.

3. Vyberte možnost **Před spuštěním převzetí služeb při selhání vypnout počítač**, pokud chcete, aby se služba Site Recovery pokusila před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede.

4. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.

5. Po převzetí služeb při selhání ověřte virtuální počítač tím, že se k němu připojíte. Pokud chcete u virtuálního počítače přejít k jinému bodu obnovení, můžete použít možnost **Změnit bod obnovení**.

6. Jakmile budete spokojeni s virtuálním počítačem, u kterého došlo k převzetí služeb při selhání, můžete převzetí služeb při selhání **Potvrdit**.
   Potvrzením dojde k odstranění všech bodů obnovení, které jsou ve službě k dispozici. Možnost **Změnit bod obnovení** už nebude dostupná.

## <a name="reprotect-the-secondary-vm"></a>Znovunastavení ochrany sekundárního virtuálního počítače

Po převzetí služeb při selhání virtuálního počítače pro něj musíte znovu nastavit ochranu, aby se replikoval zpět do primární oblasti.

1. Ujistěte se, že je virtuální počítač ve stavu **Převzetí služeb při selhání potvrzeno**, zkontrolujte dostupnost primární oblasti a ověřte, že v ní můžete vytvářet nové prostředky a přistupovat k nim.
2. V části **Trezor** > **Replikované položky** klikněte pravým tlačítkem na virtuální počítač, u kterého došlo k převzetí služeb při selhání, a pak vyberte **Znovu nastavit ochranu**.

   ![Kliknutí pravým tlačítkem a znovunastavení ochrany](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Všimněte si, že je již vybraný směr ochrany ze sekundární do primární oblasti.
3. Zkontrolujte informace o **skupině prostředků, síti, úložišti a skupinách dostupnosti**. Všechny prostředky označené jako (nové) se vytvoří v rámci operace znovunastavení ochrany.
4. Kliknutím na **OK** aktivujte úlohu znovunastavení ochrany. Tato úloha přidá do cílové lokality nejnovější data. Pak replikuje rozdíly do primární oblasti. Virtuální počítač je teď v chráněném stavu.

## <a name="fail-back-to-the-primary-region"></a>Navrácení služeb po obnovení do primární oblasti

Po znovunastavení ochrany virtuálních počítačů můžete podle potřeby provést navrácení služeb po obnovení do primární oblasti. Pokud to chcete provést, postupujte podle pokynů k [převzetí služeb při selhání](#run-a-failover).
