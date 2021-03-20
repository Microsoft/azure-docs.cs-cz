---
title: Navrácení služeb po obnovení virtuálních počítačů Hyper-V z Azure pomocí Azure Site Recovery
description: Postup při navrácení služeb virtuálních počítačů Hyper-V do místní lokality z Azure pomocí Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: ramamill
ms.openlocfilehash: a31a28728dd0521262bd0518cc49a385f4314302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87416226"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Spuštění navrácení služeb po obnovení pro virtuální počítače Hyper-V

Tento článek popisuje, jak navrátit služby po obnovení virtuálních počítačů Azure, které byly vytvořené po převzetí služeb při selhání virtuálních počítačů Hyper-V z místní lokality do Azure, s [Azure Site Recovery](site-recovery-overview.md).

- Pomocí plánovaného převzetí služeb při selhání z Azure do místní lokality dojde k selhání back-VM virtuálních počítačů s Hyper-V z Azure. Pokud je směr převzetí služeb při selhání z Azure do místního prostředí, považuje se za navrácení služeb po obnovení.
- Vzhledem k tomu, že Azure je vysoce dostupné prostředí a virtuální počítače jsou vždycky dostupné, navrácení služeb po obnovení z Azure je plánovaná aktivita. Můžete naplánovat malé výpadky, aby zatížení mohli znovu začít používat místně. 
- Plánované navrácení služeb po obnovení vypne virtuální počítače v Azure a stáhne nejnovější změny. Neočekává se žádná ztráta dat.

## <a name="before-you-start"></a>Než začnete

1. [Zkontrolujte typy navrácení služeb](failover-failback-overview.md#hyper-v-reprotectionfailback) po obnovení, které můžete použít – obnovení původního umístění a alternativní umístění obnovení.
2. Zajistěte, aby virtuální počítače Azure používaly účet úložiště, a ne spravované disky. Navrácení služeb po obnovení virtuálních počítačů Hyper-V replikovaných pomocí spravovaných disků se nepodporuje.
3. Ověřte, jestli je místní Hostitel Hyper-V (nebo server VMM System Center, pokud používáte s Site Recovery) spuštěný a připojený k Azure. 
4. Ujistěte se, že jsou pro virtuální počítače dokončené převzetí služeb při selhání a potvrzení. Nemusíte nastavovat žádné konkrétní součásti Site Recovery pro navrácení služeb po obnovení virtuálních počítačů Hyper-V z Azure.
5. Čas potřebný k dokončení synchronizace dat a spuštění místního virtuálního počítače bude záviset na řadě faktorů. Chcete-li urychlit stahování dat, můžete nakonfigurovat agenta Microsoft Recovery Services, aby používal více vláken k paralelizovat stahování. [Další informace](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Navrácení služeb po obnovení do původního umístění

Pokud chcete navrátit virtuální počítače Hyper-V v Azure do původního místního virtuálního počítače, spusťte plánované převzetí služeb při selhání z Azure do místní lokality následujícím způsobem:

1. V trezoru > **replikované položky** vyberte virtuální počítač. Klikněte pravým tlačítkem na virtuální počítač > **plánovaném převzetí služeb při selhání**. Pokud nakonfigurujete plán obnovení zpět, vyberte název plánu a klikněte na plánované převzetí služeb při **selhání**  >  .
2. V **potvrzení plánovaného převzetí služeb při selhání** vyberte zdrojové a cílové umístění. Poznamenejte si směr převzetí služeb při selhání. Pokud se převzetí služeb při selhání od primární práce a všech virtuálních počítačů nachází v sekundárním umístění, jedná se pouze o informace.
3. V **synchronizaci dat** vyberte některou možnost:
    - **Synchronizovat data před převzetím služeb při selhání (synchronizovat jenom rozdílové změny)**– Tato možnost minimalizuje výpadky virtuálních počítačů při synchronizaci bez jejich vypnutí.
        - **Fáze 1**: pořídí snímek virtuálního počítače Azure a zkopíruje ho na místního hostitele Hyper-V. Počítač pokračuje v běhu v Azure.
        - **Fáze 2**: ukončí virtuální počítač Azure, aby tam nedocházelo k žádným novým změnám. Poslední sada rozdílových změn se přenese na místní server a spustí se místní virtuální počítač.
    - **Synchronizovat data jenom během převzetí služeb při selhání (úplné stažení)**– Tato možnost je rychlejší, protože se předpokládají, že se většina disku změnila a nechcete ztrácet čas při výpočtu kontrolních součtů. Tato možnost neprovede žádné výpočty kontrolního součtu.
        - Provede stažení disku. 
        - Tuto možnost doporučujeme použít, pokud jste Azure spustili po dobu (za měsíc nebo více) nebo pokud je místní virtuální počítač odstraněný.

4. Pouze pro nástroj VMM, pokud je pro Cloud povoleno šifrování dat, vyberte v **šifrovacím klíči** certifikát, který byl vydán při povolení šifrování dat během instalace poskytovatele na serveru VMM.
5. Zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na kartě **Úlohy**.
6. Pokud jste vybrali možnost synchronizovat data před převzetím služeb při selhání, po dokončení počáteční synchronizace dat a když jste připraveni vypínat virtuální počítače v Azure, klikněte na **úlohy** > název úlohy > **Dokončit převzetí služeb při selhání**. Provede následující akce:
    - Vypne počítač Azure.
    - Převede nejnovější změny místního virtuálního počítače.
    - Spustí místní virtuální počítač.
7. Nyní se můžete přihlásit k místnímu počítači virtuálních počítačů a zkontrolovat, zda je k dispozici podle očekávání.
8. Virtuální počítač je ve stavu čekání na potvrzení. Kliknutím na **Potvrdit** potvrďte převzetí služeb při selhání.
9. Pokud chcete dokončit navrácení služeb po obnovení, klikněte na **Zpětná replikace** a začněte znovu replikovat místní virtuální počítač do Azure.



## <a name="fail-back-to-an-alternate-location"></a>Navrácení služeb po obnovení do alternativního umístění 

Navrácení služeb po obnovení do alternativního umístění následujícím způsobem:

1. Pokud nastavujete nový hardware, nainstalujte na počítači [podporovanou verzi Windows](hyper-v-azure-support-matrix.md#replicated-vms)a roli Hyper-V.
2. Vytvořte přepínač virtuální sítě se stejným názvem, který jste měli na původním serveru.
3. V části Skupina ochrany **chráněných položek**  >    >  \<ProtectionGroupName>  ->  \<VirtualMachineName> vyberte virtuální počítač, u kterého chcete provést navrácení služeb po obnovení a pak vyberte **plánované převzetí služeb při selhání**.
4. V **potvrzení plánovaného převzetí služeb při selhání** vyberte **vytvořit místní virtuální počítač, pokud neexistuje**.
5. V části **název hostitele** vyberte nový hostitelský server Hyper-V, na kterém chcete virtuální počítač umístit.
6. V **synchronizaci dat** doporučujeme vybrat možnost synchronizace dat před převzetím služeb při selhání. Tím se minimalizují výpadky virtuálních počítačů při synchronizaci bez jejich vypnutí. Provádí následující akce:
    - **Fáze 1**: pořizuje snímek virtuálního počítače Azure a zkopíruje ho na místního hostitele Hyper-V. Počítač pokračuje v běhu v Azure.
    - **Fáze 2**: ukončí virtuální počítač Azure, aby tam nedocházelo k žádným novým změnám. Poslední sada změn se přenese na místní server a spustí se místní virtuální počítač.
    
7. Kliknutím na značku zaškrtnutí spustíte převzetí služeb při selhání (navrácení služeb po obnovení).
8. Po dokončení počáteční synchronizace a připravení na vypnutí virtuálního počítače Azure klikněte na **úlohy**  >  \<planned failover job>  >  **Dokončit převzetí služeb při selhání**. Tím se vypne počítač Azure, přenese nejnovější změny do místního virtuálního počítače a spustí se.
9. K místnímu virtuálnímu počítači se můžete přihlásit a ověřit tak, že vše funguje podle očekávání.
10. Kliknutím na **Potvrdit** dokončete převzetí služeb při selhání. Potvrzení odstraní virtuální počítač Azure a jeho disky a připraví místní virtuální počítač, aby se znovu chránil.
10. Kliknutím na **Zpětná replikace** spustíte replikaci místního virtuálního počítače do Azure. V Azure se replikují jenom rozdílové změny, protože virtuální počítač byl vypnutý.

    > [!NOTE]
    > Pokud zrušíte úlohu navrácení služeb po obnovení při synchronizaci dat, místní virtuální počítač bude v poškozeném stavu. Synchronizace dat proto kopíruje poslední data z disků Azure VM na místní datové disky a dokud se synchronizace nedokončí, data na disku nemusejí být v konzistentním stavu. Pokud se místní virtuální počítač spustí po zrušení synchronizace dat, nemusí se spustit. V takovém případě znovu spusťte převzetí služeb při selhání, abyste dokončili synchronizaci dat.


## <a name="next-steps"></a>Další kroky
Až se místní virtuální počítač replikuje do Azure, můžete podle potřeby [Spustit jiné převzetí služeb při selhání](site-recovery-failover.md) do Azure.
