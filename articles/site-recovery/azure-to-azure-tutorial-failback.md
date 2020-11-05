---
title: Kurz pro navrácení služeb po obnovení virtuálních počítačů Azure do primární oblasti během zotavení po havárii pomocí Azure Site Recovery.
description: V tomto kurzu se dozvíte víc o selhání back-VM virtuálních počítačů Azure do primární oblasti s Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393885"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>Kurz: navrácení služeb po obnovení virtuálního počítače Azure do primární oblasti

Po převzetí služeb při selhání virtuálním počítačem Azure do sekundární oblasti Azure použijte tento kurz k selhání virtuálního počítače v primární oblasti Azure pomocí [Azure Site Recovery](site-recovery-overview.md).  V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> 
> * Zkontrolujte požadavky.
> * Navrácení služeb po obnovení virtuálního počítače v sekundární oblasti.
> * Znovu nastavte ochranu primárních virtuálních počítačů zpátky do sekundární oblasti.
> 
> [!NOTE]
> V tomto kurzu se dozvíte, jak provést navrácení služeb po obnovení s minimálními kroky. Pokud chcete spustit převzetí služeb při selhání s úplným nastavením, přečtěte si informace o [sítích](azure-to-azure-about-networking.md)virtuálních počítačů Azure, [automatizaci](azure-to-azure-powershell.md)a [řešení potíží](azure-to-azure-troubleshoot-errors.md).



## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem, měli byste mít:

1. [Nastavte replikaci](azure-to-azure-tutorial-enable-replication.md) pro aspoň jeden virtuální počítač Azure a vyzkoušeli na něj [podrobné pokyny pro zotavení po havárii](azure-to-azure-tutorial-dr-drill.md) .
2. Převzetí služeb při [selhání virtuálního počítače](azure-to-azure-tutorial-failover-failback.md) z primární oblasti do sekundární oblasti a jeho opětovné ochrany, aby se replikoval ze sekundární oblasti na primární. 
3. Ověřte, zda je primární oblast dostupná a zda je možné vytvořit nové prostředky a přistupovat k nim.

## <a name="fail-back-to-the-primary-region"></a>Navrácení služeb po obnovení do primární oblasti

Po opětovném zapnutí ochrany virtuálních počítačů můžete v případě potřeby navrátit služby po obnovení do primární oblasti.

1. V trezoru > **replikované položky** vyberte virtuální počítač.

2. Na stránce Přehled virtuálního počítače ověřte, že je virtuální počítač v dobrém stavu a že se synchronizace dokončila před spuštěním převzetí služeb při selhání. Virtuální počítač by měl být v *chráněném* stavu.

    ![Stránka s přehledem virtuálního počítače zobrazující virtuální počítač v chráněném stavu](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. Na stránce Přehled vyberte **převzetí služeb při selhání**. Vzhledem k tomu, že v tuto chvíli neprovádíme testovací převzetí služeb při selhání, zobrazí se výzva k ověření.

    [Stránka ukazující, že ke spuštění převzetí služeb při selhání bez testovacího převzetí služeb](./media/azure-to-azure-tutorial-failback/no-test.png)

4. V části **převzetí služeb při selhání** si poznamenejte směr od sekundárního k primárnímu a vyberte bod obnovení. Virtuální počítač Azure v cíli (primární oblast) se vytvoří pomocí dat z tohoto bodu.
   - **Poslední zpracovaná** : používá nejnovější bod obnovení zpracovaný pomocí Site Recovery. Zobrazí se časové razítko. Žádná doba nestrávila zpracováním dat, takže poskytuje nízkou dobu obnovení (RTO).
   -  **Nejnovější** : zpracovává všechna data odesílaná do Site Recovery, aby bylo možné vytvořit bod obnovení pro každý virtuální počítač před převzetím služeb při selhání. Poskytuje nejnižší cíl bodu obnovení (RPO), protože všechna data jsou replikována do Site Recovery při aktivaci převzetí služeb při selhání.
   - **Nejnovější konzistentní vzhledem k aplikacím** : Tato možnost převzetí služeb při selhání virtuálních počítačů do nejnovějšího bodu obnovení konzistentního vzhledem k aplikacím. Zobrazí se časové razítko.
   - **Vlastní** : převzetí služeb při selhání konkrétním bodem obnovení. Vlastní je k dispozici jenom v případě, že převezmete služby při selhání jednoho virtuálního počítače a nepoužijete plán obnovení.

    > [!NOTE]
    > Pokud dojde k převzetí služeb při selhání virtuálního počítače, na který jste přidali disk po povolení replikace pro virtuální počítač, body replikace budou zobrazovat disky dostupné k obnovení. Například bod replikace, který byl vytvořen před přidáním druhého disku, se zobrazí jako "1 z 2 disků".

4. Vyberte **vypnout počítač před spuštěním převzetí služeb při selhání** , pokud chcete, Site Recovery se před spuštěním převzetí služeb při selhání vypnout zdrojové virtuální počítače. Vypnutí pomáhá zajistit žádnou ztrátu dat. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 

    ![Stránka nastavení převzetí služeb při selhání](./media/azure-to-azure-tutorial-failback/failover.png)    

3. Pokud chcete spustit převzetí služeb při selhání, vyberte **OK**.
4. Sledujte převzetí služeb při selhání v oznámeních.

    ![Oznámení o průběhu převzetí služeb při selhání](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![Oznámení o úspěšném převzetí služeb při selhání](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>Znovunastavení ochrany virtuálních počítačů

Po převzetí služeb při selhání virtuálních počítačů do primární oblasti je budete muset znovu zapnout, aby se znovu spustila replikace do sekundární oblasti.

1. Na stránce **Přehled** pro virtuální počítač vyberte **znovu zapnout ochranu**.

    ![Tlačítko pro opětovné zapnutí ochrany z primární oblasti](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. Zkontrolujte nastavení cíle pro primární oblast. Prostředky označené jako nové jsou vytvářeny Site Recovery jako součást operace opětovného ochrany.
3. Výběrem **OK** spusťte proces opětovné ochrany. Proces pošle počáteční data do cílového umístění a pak replikuje rozdílové informace pro virtuální počítače do cíle.

     ![Stránka ukazující nastavení replikace](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. Sledujte postup opětovného zapnutí ochrany v oznámeních. 

    ![](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [Oznámení o průběhu procesu](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png) opětovného zapnutí ochrany oznámení o průběhu
    
  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pro virtuální počítače se spravovanými disky po navrácení služeb po obnovení a po opětovném zapnutí ochrany virtuálních počítačů z primární na sekundární oblasti se Site Recovery automaticky vyčistí počítače v oblasti sekundárního zotavení po havárii. Nemusíte ručně odstraňovat virtuální počítače a síťové adaptéry v sekundární oblasti. Virtuální počítače s nespravovanými disky se nevyčistí.

Pokud po převzetí služeb při selhání dojde k úplnému vypnutí replikace, Site Recovery vyčistí počítače chráněné tímto nástrojem. V takovém případě vyčistí disky taky pro virtuální počítače, které nepoužívají spravované disky. 
 
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste neúspěšně provedli virtuální počítače ze sekundární oblasti do primární. Jedná se o poslední krok v procesu, který zahrnuje povolení replikace pro virtuální počítač, vyzkoušení postupu zotavení po havárii, převzetí služeb při selhání z primární oblasti do sekundárního a nakonec opětovného navrácení služeb po obnovení.

> [!div class="nextstepaction"]
> Teď si můžete vyzkoušet zotavení po havárii do Azure pro [místní virtuální počítač](vmware-azure-tutorial-prepare-on-premises.md) .

