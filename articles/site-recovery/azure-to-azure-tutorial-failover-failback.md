---
title: Převzetí služeb při selhání a znovunastavení ochrany virtuálních počítačů Azure replikovaných do sekundární oblasti Azure pro zotavení po havárii pomocí služby Azure Site Recovery.
description: Zjistěte, jak převzetí služeb při selhání a znovunastavení ochrany virtuálních počítačů Azure replikovaných do sekundární oblasti Azure pro zotavení po havárii pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/29/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bf2ded7a98d608780bc447c5944e7d1f4d9b698b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926414"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Převzetí služeb při selhání a znovunastavení ochrany virtuálních počítačů Azure mezi oblastmi

Tento kurz popisuje, jak převzít služby při selhání virtuálním počítači Azure (VM) do sekundární oblasti Azure pomocí [Azure Site Recovery](site-recovery-overview.md) služby. Po přes, znovunastavení ochrany virtuálního počítače. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Převzetí služeb při selhání virtuálního počítače Azure
> * Znovunastavení ochrany sekundárního virtuálního počítače Azure, aby se replikoval do primární oblasti.

> [!NOTE]
> Tento kurz obsahuje nejjednodušší cesta s výchozím nastavením a minimální přizpůsobení. Pro složitější scénáře použijte články v části "Jak na" pro virtuální počítače Azure.

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste dokončením [postupu zotavení po havárii](azure-to-azure-tutorial-dr-drill.md) zkontrolovali, že vše funguje podle očekávání.
- Před spuštěním testovacího převzetí služeb při selhání ověřte vlastnosti virtuálního počítače. Virtuální počítač musí splňovat [požadavky Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Spuštění převzetí služeb při selhání do sekundární oblasti

1. V části **Replikované položky** vyberte virtuální počítač, u kterého chcete provést převzetí služeb při selhání, a vyberte **Převzít služby při selhání**.

   ![Převzetí služeb při selhání](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:

   * **Nejnovější** (výchozí): Zpracuje všechna data ve službě Site Recovery a poskytuje nejnižší cíl bodu obnovení (RPO).
   * **Nejnovější zpracovaný**: Obnoví virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery.
   * **Vlastní**: Převezme služby při selhání do konkrétnímu bodu obnovení. Tato možnost je užitečná při provádění testovacího převzetí služeb při selhání.

3. Vyberte **před spuštěním převzetí služeb při selhání vypnout počítač** Pokud chcete, aby Site Recovery chcete pokusit před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Site Recovery čištění zdroji po převzetí služeb při selhání.

4. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.

5. Po převzetí služeb při selhání ověřte virtuální počítač tím, že se k němu připojíte. Pokud chcete u virtuálního počítače přejít k jinému bodu obnovení, můžete použít možnost **Změnit bod obnovení**.

6. Jakmile budete spokojeni s virtuálním počítačem, u kterého došlo k převzetí služeb při selhání, můžete převzetí služeb při selhání **Potvrdit**.
   Potvrzením dojde k odstranění všech bodů obnovení, které jsou ve službě k dispozici. Nyní nebudete moct změnit bod obnovení.

> [!NOTE]
> Po převzetí služeb při selhání virtuálního počítače, do níž přidáváte na disk po povolení replikace pro virtuální počítač, se zobrazí body replikace disky, které jsou k dispozici pro obnovení. Například pokud přidáte nový virtuální počítač má jeden disk, replikace body, které byly vytvořeny před přidáním disku se zobrazí, bodu replikace se skládá z "1 2 disky".

![Převzetí služeb při selhání s přidaný disk](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Znovunastavení ochrany sekundárního virtuálního počítače

Po převzetí služeb při selhání virtuálního počítače pro něj musíte znovu nastavit ochranu, aby se replikoval zpět do primární oblasti.

1. Ujistěte se, že je virtuální počítač ve stavu **Převzetí služeb při selhání potvrzeno**, zkontrolujte dostupnost primární oblasti a ověřte, že v ní můžete vytvářet nové prostředky a přistupovat k nim.
2. V části **Trezor** > **Replikované položky** klikněte pravým tlačítkem na virtuální počítač, u kterého došlo k převzetí služeb při selhání, a pak vyberte **Znovu nastavit ochranu**.

   ![Kliknutí pravým tlačítkem a znovunastavení ochrany](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Ověřte, že směr ochrany ze sekundární do primární oblasti, je už vybraná.
3. Zkontrolujte informace o **skupině prostředků, síti, úložišti a skupinách dostupnosti**. Všechny prostředky označené jako nové jsou vytvořeny v rámci operace znovunastavení ochrany.
4. Kliknutím na **OK** aktivujte úlohu znovunastavení ochrany. Tato úloha přidá do cílové lokality nejnovější data. Pak replikuje rozdíly do primární oblasti. Virtuální počítač je teď v chráněném stavu.

## <a name="next-steps"></a>Další postup
- Po znovunastavení ochrany, [zjistěte, jak](azure-to-azure-tutorial-failback.md) zpět do primární oblasti selžou, až bude k dispozici.
- [Další informace](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o opětovného nastavování ochrany toku.
