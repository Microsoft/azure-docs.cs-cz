---
title: Virtuální počítače Hyper-V s technologiemi Rezonuje el-V z Azure pomocí Azure Site Recovery navrácení služeb po obnovení služeb při selhání
description: Jak navrácení služeb po obnovení virtuálních počítačích Hyper-V do místního webu z Azure s Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281779"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Spuštění navrácení služeb po obnovení pro virtuální počítače Hyper-V

Tento článek popisuje, jak navrácení služeb po obnovení virtuálních počítačích Azure, které byly vytvořeny po převzetí služeb při selhání virtuálních počítačích Hyper-V z místního webu do Azure, s [Azure Site Recovery](site-recovery-overview.md).

- Navrácení mísem Hyper-V virtuální počítače z Azure spuštěním plánované převzetí služeb při selhání z Azure na místní web. Pokud je směr převzetí služeb při selhání z Azure do místního, je považován za navrácení služeb po selhání.
- Vzhledem k tomu, že Azure je vysoce dostupné prostředí a virtuální počítače jsou vždy k dispozici, navrácení služeb po službách po selhání z Azure je plánovaná aktivita. Můžete naplánovat malé prostoje, aby úlohy mohly znovu začít běžet místně. 
- Plánované navrácení služeb po selhání vypne virtuální počítače v Azure a stáhne nejnovější změny. Neočekává se žádná ztráta dat.

## <a name="before-you-start"></a>Než začnete

1. [Zkontrolujte typy navrácení služeb po obnovení,](failover-failback-overview.md#hyper-v-reprotectionfailback) které můžete použít – obnovení původního umístění a obnovení alternativního umístění.
2. Ujistěte se, že virtuální počítače Azure používají účet úložiště a ne spravované disky. Navrácení služeb po selhání virtuálních počítačů Hyper-V replikovaných pomocí spravovaných disků není podporováno.
3. Zkontrolujte, jestli je místní hostitel Hyper-V (nebo server VMM system center, pokud používáte s site recovery) spuštěný a připojený k Azure. 
4. Ujistěte se, že převzetí služeb při selhání a potvrzení jsou kompletní pro virtuální chod. Nemusíte nastavit žádné konkrétní součásti obnovení webu pro navrácení služeb po obnovení virtuálních počítačů Hyper-V z Azure.
5. Čas potřebný k dokončení synchronizace dat a spuštění místního virtuálního počítače bude záviset na řadě faktorů. Chcete-li urychlit stahování dat, můžete nakonfigurovat agenta služby Microsoft Recovery Services tak, aby používal více vláken k paralelizaci stahování. [Další informace](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Návrat z převzetí služeb při selhání do původního umístění

Pokud chcete navrácení virtuálních počítačů Hyper-V v Azure do původního místního virtuálního počítače, spusťte plánované převzetí služeb při selhání z Azure do místního webu takto:

1. V úschovně > **replikované položky**vyberte virtuální ms. Klikněte pravým tlačítkem myši na > **plánovanépřevzetí služeb při selhání**. Pokud se vám nedaří obnovit plán obnovení, vyberte název plánu a klikněte na**plánované převzetí služeb** **při** > selhání .
2. V **části Potvrdit plánované převzetí služeb při selhání**zvolte zdrojová a cílová umístění. Všimněte si směru převzetí služeb při selhání. Pokud převzetí služeb při selhání z primární pracoval podle očekávání a všechny virtuální počítače jsou v sekundárním umístění je to pouze pro informaci.
3. V **poli Synchronizace dat**vyberte jednu z možností:
    - **Synchronizovat data před převzetím služeb při selhání (synchronizovat pouze rozdílové změny)**– Tato možnost minimalizuje prostoje virtuálních aplikací při synchronizaci bez jejich vypnutí.
        - **Fáze 1**: Pořídí snímek virtuálního počítače Azure a zkopíruje ho do místního hostitele Hyper-V. Počítač pokračuje v provozu v Azure.
        - **Fáze 2**: Vypne virtuální počítač Azure tak, aby žádné nové změny dojít tam. Konečná sada rozdílových změn se přenese na místní server a spustí se místní virtuální počítač.
    - **Synchronizovat data pouze během převzetí služeb při selhání (úplné stažení)**– Tato možnost je rychlejší, protože předpokládáme, že se změnila většina disku a nechceme trávit čas výpočtem kontrolních součtů. Tato možnost neprovádí žádné výpočty kontrolního součtu.
        - Provede stahování disku. 
        - Doporučujeme použít tuto možnost, pokud jste spouštěli Azure na chvíli (měsíc nebo více) nebo pokud je odstraněn místní virtuální počítač.

4. Pouze pro Nástroj VMM, pokud je pro cloud povoleno šifrování dat, vyberte v **šifrovacím klíči**certifikát, který byl vydán, když jste povolili šifrování dat během instalace zprostředkovatele na serveru VMM.
5. Zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na kartě **Úlohy**.
6. Pokud jste vybrali možnost synchronizace dat před převzetím služeb při selhání, po dokončení počáteční synchronizace dat a jste připraveni vypnout virtuální počítače v Azure, klikněte na **úlohy** > název úlohy > **complete failover**. Tím postupujte takto:
    - Vypne počítač Azure.
    - Přenese nejnovější změny do místního virtuálního počítače.
    - Spustí místní virtuální počítač.
7. Teď se můžete přihlásit k místnímu počítači virtuálních počítače a zkontrolovat, jestli je k dispozici podle očekávání.
8. Virtuální počítač je ve stavu čeká na potvrzení čeká na vyřízení. Kliknutím na **Potvrdit** potvrďte převzetí služeb při selhání.
9. Chcete-li dokončit navrácení služeb po selhání, klikněte na **možnost Zpětná replikace** a začněte znovu replikovat místní virtuální počítač do Azure.



## <a name="fail-back-to-an-alternate-location"></a>Návrat z převzetí služeb při selhání do jiného umístění 

Zpět na místo po selhání do alternativního umístění následujícím způsobem:

1. Pokud nastavujete nový hardware, nainstalujte do počítače [podporovanou verzi systému Windows](hyper-v-azure-support-matrix.md#replicated-vms)a roli Hyper-V.
2. Vytvořte přepínač virtuální sítě se stejným názvem, který jste měli na původním serveru.
3. V části Ochrana**skupiny** > \< \< **chráněných položek** > protectedgroup protectiongroup> -> VirtualMachineName> vyberte virtuální počítač, který chcete vrátit zpět, a pak vyberte **Plánované převzetí služeb při selhání**.
4. V **potvrzení plánovaného převzetí služeb při selhání**s, zvolit vytvořit místní virtuální **počítač, pokud neexistuje**.
5. V **části Název hostitele**vyberte nový hostitelský server Hyper-V, na který chcete virtuální počítač umístit.
6. V **synchronizaci dat**doporučujeme vybrat možnost synchronizace dat před převzetím služeb při selhání. Tím se minimalizuje prostoje pro virtuální chod, protože se synchronizuje bez jejich vypnutí. Provádí následující:
    - **Fáze 1**: Pořídí snímek virtuálního počítače Azure a zkopíruje ho do místního hostitele Hyper-V. Počítač pokračuje v provozu v Azure.
    - **Fáze 2**: Vypne virtuální počítač Azure tak, aby žádné nové změny dojít tam. Konečná sada změn se přenese na místní server a spustí se místní virtuální počítač.
    
7. Klepnutím na zaškrtávané políčko zahájíte převzetí služeb při selhání (navrácení služeb po selhání).
8. Po dokončení počáteční synchronizace a jste připraveni vypnout virtuální počítač Azure, klikněte na úlohy plánované **úlohy** > \<převzetí služeb při selhání> > **dokončení převzetí služeb při selhání**. Tím se vypne počítač Azure, přenese nejnovější změny do místního virtuálního počítače a spustí ho.
9. Můžete se přihlásit k místnímu virtuálnímu počítači a ověřit, že vše funguje podle očekávání.
10. Chcete-li dokončit převzetí služeb při selhání, klepněte na **tlačítko Potvrdit.** Potvrzení odstraní virtuální počítač Azure a jeho disky a připraví místní virtuální počítač, který má být znovu chráněn.
10. Kliknutím na **Zpětnou replikaci** začněte replikovat místní virtuální počítač do Azure. Pouze rozdílové změny od vypnutí virtuálního počítače v Azure se budou replikovat.

    > [!NOTE]
    > Pokud zrušíte úlohu navrácení služeb po selhání během synchronizace dat, místní virtuální počítač bude v poškozeném stavu. Důvodem je, že synchronizace dat zkopíruje nejnovější data z disků virtuálního počítače Azure na místní datové disky a dokud synchronizace nedokončí, data disku nemusí být v konzistentním stavu. Pokud se místní virtuální počítač spustí po zrušení synchronizace dat, nemusí se spustit. V takovém případě znovu spusťte převzetí služeb při selhání k dokončení synchronizace dat.


## <a name="next-steps"></a>Další kroky
Po místní virtuální počítač je replikaci do Azure, můžete [spustit další převzetí služeb při selhání](site-recovery-failover.md) do Azure podle potřeby.
