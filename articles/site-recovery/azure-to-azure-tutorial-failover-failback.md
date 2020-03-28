---
title: Převzetí služeb při selhání a znovu chránit virtuální počítače Azure replikované do sekundární oblasti Azure pro zotavení po havárii se službou Azure Site Recovery.
description: Zjistěte, jak převzetí služeb při selhání a znovu chránit virtuální počítače Azure replikované do sekundární oblasti Azure pro zotavení po havárii pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9bc0d25e19ad3412e62eb3386b0faf3ae5d2a444
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68782596"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Převzetí služeb při selhání a opětovné zabezpečení virtuálních počítačích Azure mezi oblastmi

Tento kurz popisuje, jak převzetí služeb při selhání virtuálního počítače Azure (VM) do sekundární oblasti Azure se službou [Azure Site Recovery.](site-recovery-overview.md) Po převzetí selhání, znovu zamknout virtuální ho. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Převzetí služeb při selhání virtuálního počítače Azure
> * Znovu chránit sekundární virtuální počítač Azure tak, aby se replikuje do primární oblasti.

> [!NOTE]
> Tento kurz obsahuje nejjednodušší cestu s výchozím nastavením a minimální majetím. Pro složitější scénáře použijte články v části "Jak" pro virtuální počítače Azure.


## <a name="prerequisites"></a>Požadavky

- Než začnete, zkontrolujte [nejčastější dotazy týkající](site-recovery-faq.md#failover) se převzetí služeb při selhání.
- Ujistěte se, že jste dokončením [postupu zotavení po havárii](azure-to-azure-tutorial-dr-drill.md) zkontrolovali, že vše funguje podle očekávání.
- Před spuštěním testovacího převzetí služeb při selhání ověřte vlastnosti virtuálního počítače. Virtuální počítač musí splňovat [požadavky Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Spuštění převzetí služeb při selhání do sekundární oblasti

1. V části **Replikované položky** vyberte virtuální počítač, u kterého chcete provést převzetí služeb při selhání, a vyberte **Převzít služby při selhání**.

   ![Převzetí služeb při selhání](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. V **převzetí služeb při selhání**vyberte bod **obnovení,** na který chcete přepojit. Můžete použít jednu z následujících možností:

   * **Nejnovější** (výchozí): Zpracuje všechna data ve službě Obnovení webu a poskytuje nejnižší cíl bodu obnovení (RPO).
   * **Poslední zpracované**: Vrátí virtuální počítač do nejnovějšího bodu obnovení, který byl zpracován službou Site Recovery.
   * **Vlastní**: Převzetí služby při selhání do určitého bodu obnovení. Tato možnost je užitečná při provádění testovacího převzetí služeb při selhání.

3. Vyberte **Vypnout počítač před zahájením převzetí služeb při selhání,** pokud chcete, aby se obnovení webu pokusilo před spuštěním převzetí služeb při selhání provést vypnutí zdrojových virtuálních počítačů. Vypnutí pomáhá zajistit žádnou ztrátu dat. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Obnovení webu nečistí zdroj po převzetí služeb při selhání.

4. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.

5. Po převzetí služeb při selhání ověřte virtuální počítač tím, že se k němu připojíte. Pokud chcete u virtuálního počítače přejít k jinému bodu obnovení, můžete použít možnost **Změnit bod obnovení**.

6. Jakmile budete spokojeni s virtuálním počítačem, u kterého došlo k převzetí služeb při selhání, můžete převzetí služeb při selhání **Potvrdit**.
   Potvrzením dojde k odstranění všech bodů obnovení, které jsou ve službě k dispozici. Nyní nebudete moci změnit bod obnovení.

> [!NOTE]
> Při převzetí služeb při selhání virtuálního počítače, do kterého přidáte disk po povolení replikace pro virtuální počítače, replikační body se zobrazí disky, které jsou k dispozici pro obnovení. Například pokud virtuální modul má jeden disk a přidáte nový, replikační body, které byly vytvořeny před přidáním disku se zobrazí, že replikační bod se skládá z "1 ze 2 disků".

![Převzetí služeb při selhání s přidaným diskem](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Znovunastavení ochrany sekundárního virtuálního počítače

Po převzetí služeb při selhání virtuálního počítače pro něj musíte znovu nastavit ochranu, aby se replikoval zpět do primární oblasti.

1. Ujistěte se, že je virtuální počítač ve stavu **Převzetí služeb při selhání potvrzeno**, zkontrolujte dostupnost primární oblasti a ověřte, že v ní můžete vytvářet nové prostředky a přistupovat k nim.
2. V části**Replikované položky** **úložiště** > klikněte pravým tlačítkem myši na virtuální hod, který byl převzetím dat, a potom vyberte **příkaz Znovu chránit**.

   ![Kliknutí pravým tlačítkem a znovunastavení ochrany](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Ověřte, zda je již vybrán směr ochrany, sekundární k primární oblasti.
3. Zkontrolujte informace o **skupině prostředků, síti, úložišti a skupinách dostupnosti**. Všechny prostředky označené jako nové jsou vytvořeny jako součást operace znovu chránit.
4. Kliknutím na **OK** aktivujte úlohu znovunastavení ochrany. Tato úloha přidá do cílové lokality nejnovější data. Pak replikuje rozdíly do primární oblasti. Virtuální počítač je teď v chráněném stavu.

## <a name="next-steps"></a>Další kroky
- Po opětovném otekvovat [se dozvíte, jak obnovit](azure-to-azure-tutorial-failback.md) služby při selhání do primární oblasti, jakmile bude k dispozici.
- [Další informace](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o toku opětovné ochrany.
