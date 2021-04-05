---
title: Kurz pro převzetí služeb při selhání virtuálních počítačů Azure do sekundární oblasti pro zotavení po havárii s Azure Site Recovery.
description: V tomto kurzu se dozvíte, jak převzít služby při selhání a znovu nastavit ochranu virtuálních počítačů Azure replikovaných do sekundární oblasti Azure pro zotavení po havárii pomocí služby Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392733"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>Kurz: převzetí služeb při selhání virtuálních počítačů Azure do sekundární oblasti

Přečtěte si, jak převzít služby při selhání virtuálních počítačů Azure, které jsou povolené pro zotavení po havárii s [Azure Site Recovery](site-recovery-overview.md), do sekundární oblasti Azure. Po převzetí služeb při selhání můžete znovu nastavit ochranu virtuálních počítačů v cílové oblasti tak, aby se replikují zpátky do primární oblasti. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Kontrola požadavků
> * Ověřit nastavení virtuálního počítače
> * Spuštění převzetí služeb při selhání do sekundární oblasti
> * Začněte replikovat virtuální počítač zpátky do primární oblasti.


> [!NOTE]
> V tomto kurzu se dozvíte, jak provést převzetí služeb při selhání virtuálních počítačů s minimálními kroky. Pokud chcete spustit převzetí služeb při selhání s úplným nastavením, přečtěte si informace o [sítích](azure-to-azure-about-networking.md)virtuálních počítačů Azure, [automatizaci](azure-to-azure-powershell.md)a [řešení potíží](azure-to-azure-troubleshoot-errors.md).



## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, měli byste mít:

1. Nastavte replikaci pro jeden nebo víc virtuálních počítačů Azure. Pokud jste to ještě neudělali, [dokončete první kurz](azure-to-azure-tutorial-enable-replication.md) v této sérii.
2. Pro replikované virtuální počítače doporučujeme [Spustit postup zotavení po havárii](azure-to-azure-tutorial-dr-drill.md) . Spuštění podrobností před spuštěním úplného převzetí služeb při selhání pomůže zajistit, že vše funguje podle očekávání, aniž by to mělo dopad na produkční prostředí. 


## <a name="verify-the-vm-settings"></a>Ověření nastavení virtuálního počítače

1. V trezoru > **replikované položky** vyberte virtuální počítač.

    ![Možnost otevření vlastností virtuálního počítače na stránce Přehled](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. Na stránce **Přehled** virtuálního počítače ověřte, že je virtuální počítač chráněný a v pořádku, než spustíte převzetí služeb při selhání.
    ![Stránka pro ověření vlastností a stavu virtuálního počítače](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. Před převzetím služeb při selhání ověřte, že:
    - Na virtuálním počítači běží podporovaný operační systém [Windows](azure-to-azure-support-matrix.md#windows) nebo [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) .
    - Virtuální počítač splňuje požadavky na [výpočetní](azure-to-azure-support-matrix.md#replicated-machines---compute-settings)prostředky, [úložiště](azure-to-azure-support-matrix.md#replicated-machines---storage)a [sítě](azure-to-azure-support-matrix.md#replicated-machines---networking) .

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání


1. Na stránce **Přehled** virtuálního počítače vyberte **převzetí služeb při selhání**.

    ![Tlačítko převzetí služeb při selhání pro replikovanou položku](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. V části **převzetí služeb při selhání** vyberte bod obnovení. Virtuální počítač Azure v cílové oblasti se vytvoří pomocí dat z tohoto bodu obnovení.
  
   - **Poslední zpracovaná**: používá nejnovější bod obnovení zpracovaný pomocí Site Recovery. Zobrazí se časové razítko. Žádná doba nestrávila zpracováním dat, takže poskytuje nízkou dobu obnovení (RTO).
   -  **Nejnovější**: zpracovává všechna data odesílaná do Site Recovery, aby bylo možné vytvořit bod obnovení pro každý virtuální počítač před převzetím služeb při selhání. Poskytuje nejnižší cíl bodu obnovení (RPO), protože všechna data jsou replikována do Site Recovery při aktivaci převzetí služeb při selhání.
   - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost převzetí služeb při selhání virtuálních počítačů do nejnovějšího bodu obnovení konzistentního vzhledem k aplikacím. Zobrazí se časové razítko.
   - **Vlastní**: převzetí služeb při selhání konkrétním bodem obnovení. Vlastní je k dispozici jenom v případě, že převezmete služby při selhání jednoho virtuálního počítače a nepoužijete plán obnovení.

    > [!NOTE]
    > Pokud jste po povolení replikace přidali disk k virtuálnímu počítači, body replikace zobrazí disky dostupné k obnovení. Například bod replikace vytvořený před přidáním druhého disku se zobrazí jako "1 z 2 disků".

4. Vyberte **vypnout počítač před spuštěním převzetí služeb při selhání** , pokud chcete, aby Site Recovery před spuštěním převzetí služeb při selhání vypnout zdrojové virtuální počítače. Vypnutí pomáhá zajistit žádnou ztrátu dat. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 

    ![Stránka nastavení převzetí služeb při selhání](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. Pokud chcete spustit převzetí služeb při selhání, vyberte **OK**.
4. Sledujte převzetí služeb při selhání v oznámeních.

    ![Oznámení o ](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![ úspěšnosti oznámení o průběhu](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. Po převzetí služeb při selhání se virtuální počítač Azure vytvořený v cílové oblasti zobrazí v **Virtual Machines**. Ujistěte se, že je virtuální počítač spuštěný a má odpovídající velikost. Pokud chcete pro virtuální počítač použít jiný bod obnovení, vyberte na stránce **základy** možnost **změnit bod obnovení**.
6. Až budete s virtuálním počítačem pro převzetí služeb při selhání spokojeni, na stránce Přehled vyberte **Potvrdit** a dokončete převzetí služeb při selhání.

    ![Tlačítko Potvrdit změny](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. V **potvrzení** kliknutím na **OK** potvrďte. Příkaz commit odstraní všechny dostupné body obnovení pro virtuální počítač v Site Recovery a nebudete moct změnit bod obnovení.

8. Sledujte průběh potvrzení v oznámeních.

    ![](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![ Oznámení o úspěšnosti potvrzení oznámení o průběhu potvrzení](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. Site Recovery po převzetí služeb při selhání nevyčistit zdrojový virtuální počítač. To je nutné provést ručně.


## <a name="reprotect-the-vm"></a>Znovu zapnout ochranu virtuálního počítače

Po převzetí služeb při selhání znovu nastavte ochranu virtuálního počítače v sekundární oblasti, aby se replikoval zpátky do primární oblasti. 

1. Než začnete, ujistěte se, že **stav** virtuálního počítače je *převzetí služeb při selhání* .
2. Ověřte, že máte k dispozici přístup k primární oblasti a že máte oprávnění k vytváření virtuálních počítačů v ní.
3. Na stránce **Přehled** virtuálního počítače vyberte **znovu zapnout ochranu**.

   ![Tlačítko pro povolení opětovného zapnutí ochrany virtuálního počítače pro virtuální počítač.](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. V části **znovu nastavit ochranu** ověřte směr replikace (sekundární do primární oblasti) a zkontrolujte nastavení cíle pro primární oblast. Prostředky označené jako nové jsou vytvářeny Site Recovery jako součást operace opětovného ochrany.

     ![Stránka nastavení ochrany](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. Výběrem **OK** spusťte proces opětovné ochrany. Proces pošle počáteční data do cílového umístění a pak replikuje rozdílové informace pro virtuální počítače do cíle.
7. Sledujte postup opětovné ochrany v oznámeních. 

    ![](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png)Oznámení o úspěšném dokončení ochrany při opětovném zapnutí ochrany oznámení o průběhu ![](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>Další kroky

V tomto kurzu převezmete služby při selhání z primární oblasti na sekundární a znovu spustíte replikaci virtuálních počítačů zpátky do primární oblasti. Nyní můžete provést navrácení služeb po obnovení ze sekundární oblasti na primární.

> [!div class="nextstepaction"]
> [Navrácení služeb po obnovení do primární oblasti](azure-to-azure-tutorial-failback.md)
