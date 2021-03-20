---
title: Kurz spuštění postupu zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
description: V tomto kurzu pomocí Site Recovery spustíte postup zotavení po havárii virtuálního počítače Azure v jiné oblasti.
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395574"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>Kurz: spuštění postupu zotavení po havárii pro virtuální počítače Azure

Naučte se, jak spustit postup zotavení po havárii do jiné oblasti Azure, pro virtuální počítače Azure, které se replikují pomocí [Azure Site Recovery](site-recovery-overview.md). V tomto článku:

> [!div class="checklist"]
> * Ověření požadavků
> * Před přechodem Projděte si nastavení virtuálního počítače.
> * Spuštění testovacího převzetí služeb při selhání
> * Vyčistit po přechodu k podrobnostem


> [!NOTE]
> V tomto kurzu najdete minimální kroky pro spuštění postupu zotavení po havárii. Pokud chcete spustit podrobnou analýzu s úplným testováním infrastruktury, přečtěte si informace o [sítích](azure-to-azure-about-networking.md)virtuálních počítačů Azure, [automatizaci](azure-to-azure-powershell.md)a [řešení potíží](azure-to-azure-troubleshoot-errors.md).

## <a name="prerequisites"></a>Předpoklady

Než začnete tento kurz, musíte povolit zotavení po havárii pro jeden nebo několik virtuálních počítačů Azure. Provedete to [provedením prvního kurzu](azure-to-azure-tutorial-enable-replication.md) v této řadě.

## <a name="verify-vm-settings"></a>Ověřit nastavení virtuálního počítače

1. V trezoru > **replikované položky** vyberte virtuální počítač.

    ![Možnost otevření stránky pro zotavení po havárii ve vlastnostech virtuálního počítače](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. Na stránce **Přehled** ověřte, že je virtuální počítač chráněný a v pořádku.
3. Při spuštění testovacího převzetí služeb při selhání vyberete virtuální síť Azure v cílové oblasti. Virtuální počítač Azure vytvořený po převzetí služeb při selhání je umístěný v této síti. 

    - V tomto kurzu při spuštění testovacího převzetí služeb při selhání vybereme existující síť.
    - Pro procházení doporučujeme zvolit neprodukční síť, aby IP adresy a síťové součásti zůstaly dostupné v produkčních sítích.
   - Můžete také předem nakonfigurovat nastavení sítě, které se má použít pro testovací převzetí služeb při selhání. Mezi podrobná nastavení, která můžete přiřadit pro každou síťovou kartu, patří podsíť, soukromá IP adresa, veřejná IP adresa, nástroj pro vyrovnávání zatížení a skupina zabezpečení sítě. Tuto metodu nepoužíváme, ale další informace najdete v [tomto článku](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations) .


## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání


1. Na stránce **Přehled** vyberte **Test převzetí služeb při selhání**.

    
    ![Tlačítko Test převzetí služeb při selhání pro replikovanou položku](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. V části **testovací převzetí služeb při selhání** vyberte bod obnovení. Virtuální počítač Azure v cílové oblasti se vytvoří pomocí dat z tohoto bodu obnovení.
  
   - **Poslední zpracovaná**: používá nejnovější bod obnovení zpracovaný pomocí Site Recovery. Zobrazí se časové razítko. Žádná doba nestrávila zpracováním dat, takže poskytuje nízkou dobu obnovení (RTO).
   -  **Nejnovější**: zpracovává všechna data odesílaná do Site Recovery, aby bylo možné vytvořit bod obnovení pro každý virtuální počítač před převzetím služeb při selhání. Poskytuje nejnižší cíl bodu obnovení (RPO), protože všechna data jsou replikována do Site Recovery při aktivaci převzetí služeb při selhání.
   - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost převzetí služeb při selhání virtuálních počítačů do nejnovějšího bodu obnovení konzistentního vzhledem k aplikacím. Zobrazí se časové razítko.
   - **Vlastní**: převzetí služeb při selhání konkrétním bodem obnovení. Vlastní je k dispozici jenom v případě, že převezmete služby při selhání jednoho virtuálního počítače a nepoužijete plán obnovení.

3. V **Azure Virtual Network** vyberte cílovou síť, ve které se virtuální počítače Azure vytvoří po převzetí služeb při selhání. Pokud je to možné, vyberte jinou neprodukční síť, nikoli síť vytvořenou při povolení replikace.

    ![Stránka nastavení testovacího převzetí služeb při selhání](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. Pokud chcete spustit převzetí služeb při selhání, vyberte **OK**.
5. Sledujte testovací převzetí služeb při selhání v oznámeních.

    ![Oznámení o ](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![ úspěšnosti oznámení o průběhu](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. Po dokončení převzetí služeb při selhání se virtuální počítač Azure, který se vytvoří v cílové oblasti, zobrazí v **Virtual Machines** Azure Portal. Ujistěte se, že je virtuální počítač spuštěný a správně správný a připojený k síti, kterou jste vybrali.

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Na stránce **základy** vyberte **vyčistit testovací převzetí služeb při selhání**.

    ![Tlačítko pro spuštění procesu čištění](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. V části testovat poznámky k **Vyčištění pro převzetí služeb při selhání**  >  záznam a uložte všechny poznámky spojené s testovacím převzetím služeb 
3. Vyberte **testování bylo dokončeno** , aby se odstranily virtuální počítače vytvořené během testovacího převzetí služeb při selhání.

    ![Stránka s možnostmi vyčištění](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. Monitorovat průběh čištění v oznámeních.

    ![](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![ Oznámení o úspěšném Vymazání oznámení o průběhu čištění](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste spustili postup zotavení po havárii, ve kterém zjistíte, že převzetí služeb při selhání funguje podle očekávání. Nyní můžete vyzkoušet úplné převzetí služeb při selhání.

> [!div class="nextstepaction"]
> [Spuštění produkčního převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md)
