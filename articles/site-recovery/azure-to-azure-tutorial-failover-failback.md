---
title: Převzetí služeb při selhání a opětovné zapnutí ochrany virtuálních počítačů Azure replikovaných do sekundární oblasti Azure pro zotavení po havárii pomocí služby Azure Site Recovery.
description: Přečtěte si, jak převzít služby při selhání a znovu nastavit ochranu virtuálních počítačů Azure replikovaných do sekundární oblasti Azure pro zotavení po havárii. Služba Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8d38aa513b0829c2626fcd4a92c40faabff1f83e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502388"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Převzetí služeb při selhání a znovunastavení ochrany virtuálních počítačů Azure mezi oblastmi

V tomto kurzu se dozvíte, jak provést převzetí služeb při selhání virtuálního počítače Azure do sekundární oblasti Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) . Po převzetí služeb při selhání znovu nastavte ochranu virtuálního počítače. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Převzetí služeb při selhání virtuálního počítače Azure
> * Znovu nastavte ochranu sekundárního virtuálního počítače Azure tak, aby se replikoval do primární oblasti.

> [!NOTE]
> Tento kurz obsahuje nejjednodušší cestu s výchozími nastaveními a minimálními úpravami. V případě složitějších scénářů použijte články v části How to pro virtuální počítače Azure.


## <a name="prerequisites"></a>Požadavky

- Než začnete, přečtěte si [Nejčastější dotazy](site-recovery-faq.md#failover) k převzetí služeb při selhání.
- Ujistěte se, že jste dokončením [postupu zotavení po havárii](azure-to-azure-tutorial-dr-drill.md) zkontrolovali, že vše funguje podle očekávání.
- Před spuštěním testovacího převzetí služeb při selhání ověřte vlastnosti virtuálního počítače. Virtuální počítač musí splňovat [požadavky Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Spuštění převzetí služeb při selhání do sekundární oblasti

1. V části **Replikované položky** vyberte virtuální počítač, u kterého chcete provést převzetí služeb při selhání, a vyberte **Převzít služby při selhání**.

   ![Snímek obrazovky znázorňující možnosti převzetí služeb při selhání pro virtuální počítač](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:

   * **Nejnovější** (výchozí): zpracovává všechna data ve službě Site Recovery a poskytuje nejnižší cíl bodu obnovení (RPO).
   * **Poslední zpracovaná**: vrátí virtuální počítač k nejnovějšímu bodu obnovení, který byl zpracován službou Site Recovery.
   * **Vlastní**: převzetí služeb při selhání pro určitý bod obnovení. Tato možnost je užitečná při provádění testovacího převzetí služeb při selhání.

3. Před spuštěním **převzetí služeb při selhání vyberte vypnout počítač** , pokud se chcete Site Recovery pokusit před aktivací převzetí služeb při selhání provést vypnutí zdrojových virtuálních počítačů. Vypnutí pomáhá zajistit žádnou ztrátu dat. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Site Recovery nevyčistit zdroj po převzetí služeb při selhání.

4. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.

5. Po převzetí služeb při selhání ověřte virtuální počítač tím, že se k němu připojíte. Pokud chcete u virtuálního počítače přejít k jinému bodu obnovení, můžete použít možnost **Změnit bod obnovení**.

6. Jakmile budete spokojeni s virtuálním počítačem, u kterého došlo k převzetí služeb při selhání, můžete převzetí služeb při selhání **Potvrdit**.
   Potvrzením dojde k odstranění všech bodů obnovení, které jsou ve službě k dispozici. Nyní už nebudete moct změnit bod obnovení.

> [!NOTE]
> Při převzetí služeb při selhání virtuálního počítače, do kterého jste po povolení replikace pro virtuální počítač přidali disk, se v bodech replikace zobrazí disky, které jsou k dispozici pro obnovení. Například pokud má virtuální počítač jeden disk a přidáte nový, body replikace, které byly vytvořeny před přidáním disku, zobrazí, že bod replikace se skládá z "1 z 2 disků".

![Snímek obrazovky s převzetím služeb při selhání s přidaným diskem](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Znovunastavení ochrany sekundárního virtuálního počítače

Po převzetí služeb při selhání virtuálního počítače pro něj musíte znovu nastavit ochranu, aby se replikoval zpět do primární oblasti.

1. Ujistěte se, že je virtuální počítač ve stavu **Převzetí služeb při selhání potvrzeno**, zkontrolujte dostupnost primární oblasti a ověřte, že v ní můžete vytvářet nové prostředky a přistupovat k nim.
2. V **trezoru**  >  **replikované položky**klikněte pravým tlačítkem na virtuální počítač, u kterého došlo k převzetí služeb při selhání, a pak vyberte **znovu zapnout ochranu**.

   ![Snímek obrazovky s možností opětovné ochrany pro virtuální počítač](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Ověřte, zda je již vybrán směr ochrany, sekundární do primární oblasti.
3. Zkontrolujte informace o **skupině prostředků, síti, úložišti a skupinách dostupnosti**. Všechny prostředky označené jako nové jsou vytvořeny jako součást operace opětovného ochrany.
4. Kliknutím na **OK** aktivujte úlohu znovunastavení ochrany. Tato úloha přidá do cílové lokality nejnovější data. Pak replikuje rozdíly do primární oblasti. Virtuální počítač je teď v chráněném stavu.

## <a name="next-steps"></a>Další kroky
- Po opětovném zapnutí ochrany se [dozvíte, jak](azure-to-azure-tutorial-failback.md) navrátit služby po obnovení do primární oblasti, až bude k dispozici.
- [Přečtěte si další informace](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o toku ochrany.
