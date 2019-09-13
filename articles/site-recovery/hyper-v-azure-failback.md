---
title: Spuštění navrácení služeb po havárii během havárie virtuálních počítačů Hyper-v z Azure do místního prostředí | Microsoft Docs
description: Naučte se při zotavení po havárii do Azure pomocí služby Azure Site Recovery navrátit virtuální počítače Hyper-V do místní lokality.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 07ecc8547ab155600bccfd1ad8f1ecbb58a18fa3
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931851"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Spuštění navrácení služeb po obnovení pro virtuální počítače Hyper-V

Tento článek popisuje, jak navrácení služeb po obnovení virtuálních počítačů Hyper-V chráněných pomocí Site Recovery.

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste si přečetli podrobnosti o [různých typech navrácení služeb po obnovení](concepts-types-of-failback.md) a odpovídajících aspektech.
- Zajistěte, aby byl server VMM nebo hostitelský server Hyper-V připojený k Azure.
- Měli byste provést **potvrzení** na virtuálním počítači.
- Ujistěte se, že používáte účet úložiště pro replikaci a ne spravované disky. Navrácení služeb po obnovení virtuálních počítačů Hyper-V replikovaných pomocí správy disků se nepodporuje.

## <a name="perform-failback"></a>Provést navrácení služeb po obnovení
Po převzetí služeb při selhání z primárního do sekundárního umístění se replikované virtuální počítače nechrání pomocí Site Recovery a sekundární umístění teď funguje jako aktivní umístění. K navrácení služeb po obnovení virtuálních počítačů v plánu obnovení spusťte plánované převzetí služeb při selhání ze sekundární lokality na primární, a to následujícím způsobem. 
1. Vyberte **plány** > obnovení*recoveryplan_name*. Klikněte na **převzetí služeb při selhání** > **naplánované**.
2. Na stránce **potvrdit plánované převzetí služeb při selhání** vyberte zdrojové a cílové umístění. Poznamenejte si směr převzetí služeb při selhání. Pokud se převzetí služeb při selhání od primární aplikace vypracovalo jako očekávané a všechny virtuální počítače jsou v sekundárním umístění, je to jenom pro informace.
3. Pokud nakonfigurujete obnovení z Azure, vyberte nastavení v **synchronizaci dat**:
    - **Synchronizovat data před převzetím služeb při selhání (synchronizovat jenom rozdílové změny)** – Tato možnost minimalizuje výpadky virtuálních počítačů při synchronizaci bez jejich vypnutí. Provede následující kroky:
        - Fáze 1: Pořídí snímek virtuálního počítače v Azure a zkopíruje ho na místního hostitele Hyper-V. Počítač pokračuje v běhu v Azure.
        - Fáze 2: Vypne virtuální počítač v Azure, aby tam nedocházelo k žádným novým změnám. Poslední sada rozdílových změn se přenese na místní server a spustí se místní virtuální počítač.

    - **Synchronizovat data jenom během převzetí služeb při selhání (úplné stažení)** – Tato možnost je rychlejší.
        - Tato možnost je rychlejší, protože očekáváme, že se většina disku změnila a nechcete ztrácet čas při výpočtu kontrolního součtu. Provede stažení disku. Je to také užitečné, když se odstraní virtuální počítač Prem.
        - Tuto možnost doporučujeme použít, pokud jste Azure spustili po dobu delší než (měsíc nebo více) nebo když je virtuální počítač s Prem odstraněn. Tato možnost neprovede žádné výpočty kontrolního součtu.


4. Pokud je pro Cloud povolené šifrování dat, vyberte v **šifrovacím klíči** certifikát, který byl vydán, když jste povolili šifrování dat během instalace poskytovatele na serveru VMM.
5. Zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na kartě **Úlohy**.
6. Pokud jste vybrali možnost synchronizovat data před převzetím služeb při selhání, po dokončení počáteční synchronizace dat a když jste připraveni vypnout virtuální počítače v Azure, klikněte na **úlohy** > název úlohy > **Dokončit převzetí služeb při selhání**. Tím se vypne počítač Azure, přenese nejnovější změny do místního virtuálního počítače a virtuální počítač se spustí místně.
7. Nyní se můžete přihlásit k virtuálnímu počítači a ověřit, jestli je k dispozici podle očekávání.
8. Virtuální počítač je ve stavu čekání na potvrzení. Kliknutím na **Potvrdit** potvrďte převzetí služeb při selhání.
9. Chcete-li dokončit navrácení služeb po obnovení, kliknutím na možnost **Zpětná replikace** spusťte ochranu virtuálního počítače v primární lokalitě.


Při navrácení služeb po obnovení do původní primární lokality postupujte podle těchto pokynů. Tento postup popisuje, jak spustit plánované převzetí služeb při selhání pro plán obnovení. Alternativně můžete spustit převzetí služeb při selhání pro jeden virtuální počítač na kartě **Virtual Machines** .


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Navrácení služeb po obnovení do alternativního umístění v prostředí Hyper-V
Pokud jste nasadili ochranu mezi [lokalitou Hyper-V a Azure](site-recovery-hyper-v-site-to-azure.md) , musíte mít možnost navrátit služby po obnovení z Azure do alternativního místního umístění. To je užitečné v případě, že potřebujete nastavit nový místní hardware. Tady je postup, jak to udělat.

1. Pokud nastavujete nový hardware, nainstalujte na server Windows Server 2012 R2 a roli Hyper-V.
2. Vytvořte přepínač virtuální sítě se stejným názvem, který jste měli na původním serveru.
3. Vyberte **chráněné položky** ->  \<**Skupina** -> ochrany\<ProtectionGroupName >-> VirtualMachineName > chcete provést navrácení služeb po obnovení a vybrat **plánované převzetí služeb při selhání**.
4. V **potvrzení plánovaného převzetí služeb při selhání** vyberte **vytvořit místní virtuální počítač, pokud**neexistuje.
5. V části název hostitele * * vyberte nový hostitelský server Hyper-V, na který chcete virtuální počítač umístit.
6. V synchronizaci dat doporučujeme vybrat možnost synchronizace dat před převzetím služeb při selhání. Tím se minimalizují výpadky virtuálních počítačů při synchronizaci bez jejich vypnutí. Provádí následující akce:

    - Fáze 1: Pořídí snímek virtuálního počítače v Azure a zkopíruje ho na místního hostitele Hyper-V. Počítač pokračuje v běhu v Azure.
    - Fáze 2: Vypne virtuální počítač v Azure, aby tam nedocházelo k žádným novým změnám. Poslední sada změn se přenese na místní server a spustí se místní virtuální počítač.
    
7. Kliknutím na značku zaškrtnutí spustíte převzetí služeb při selhání (navrácení služeb po obnovení).
8. Po dokončení počáteční synchronizace a připravení vypnout virtuální počítač v Azure klikněte na **úlohy** > \<plánovaného převzetí služeb při selhání > > **Dokončit převzetí služeb při selhání**. Tím se vypne počítač Azure, přenese nejnovější změny do místního virtuálního počítače a spustí se.
9. Můžete se přihlásit k místnímu virtuálnímu počítači a ověřit, zda vše funguje podle očekávání. Pak kliknutím na **Potvrdit** dokončete převzetí služeb při selhání. Potvrzení odstraní virtuální počítač Azure a jeho disky a připraví virtuální počítač tak, aby se znovu chránil.
10. Kliknutím na **Zpětná replikace** zahajte ochranu místního virtuálního počítače.

    > [!NOTE]
    > Pokud zrušíte úlohu navrácení služeb po obnovení, která je v kroku synchronizace dat, místní virtuální počítač bude v poškozeném stavu. Synchronizace dat proto kopíruje nejnovější data z disků Azure VM na datové disky Prem a dokud se synchronizace nedokončí, data disku nemusejí být v konzistentním stavu. Pokud se virtuální počítač Prem spustí po zrušení synchronizace dat, nemusí se spustit. Znovu aktivujte převzetí služeb při selhání, aby se dokončila synchronizace dat.


## <a name="why-is-there-no-button-called-failback"></a>Proč není k dispozici žádné tlačítko s názvem navrácení služeb po obnovení?
Na portálu neexistuje žádné explicitní gesto s názvem navrácení služeb po obnovení. Navrácení služeb po obnovení je krok, ve kterém se vrátíte zpět k primární lokalitě. Po navrácení služeb po obnovení dojde k převzetí služeb při selhání virtuálních počítačů z obnovení zpět na primární.

Když zahájíte převzetí služeb při selhání, zobrazí se v okně informace o směru přesunu virtuálních počítačů, pokud je směr z Azure na místní, jedná se o navrácení služeb po obnovení.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Proč je pro navrácení služeb po obnovení jenom plánované gesto převzetí služeb při selhání?
Azure je prostředí s vysokou dostupností a vaše virtuální počítače jsou vždycky dostupné. Navrácení služeb po obnovení je plánovaná aktivita, u které se rozhodnete provést malé výpadky, aby bylo možné úlohy znovu spouštět místně. To neočekává žádnou ztrátu dat. Proto je k dispozici pouze plánované gesto převzetí služeb při selhání, které vypne virtuální počítače v Azure, stáhne nejnovější změny a zajistí, že nedojde ke ztrátě dat.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Potřebuji procesový Server v Azure pro navrácení služeb po obnovení do Hyper-v?
Ne. procesový Server se vyžaduje jenom v případě, že chráníte virtuální počítače VMware. Při ochraně nebo navrácení služeb po obnovení virtuálních počítačů s technologií Hyper-v není nutné nasadit žádné další součásti.


## <a name="time-taken-to-failback"></a>Doba potřebná k navrácení služeb po obnovení
Doba potřebná k dokončení synchronizace dat a spuštění virtuálního počítače závisí na různých faktorech. Abychom vám pomohli vzít v úvahu přehled, co se stane při synchronizaci dat.

Synchronizace dat pořizuje snímek disků virtuálního počítače a spustí kontrolu bloků podle bloku a vypočte kontrolní součet. Tento vypočtený kontrolní součet se pošle do místního počítače a porovná se s místním kontrolním součtem stejného bloku. V případě, že se kontrolní součty shodují, není datový blok převeden. Pokud se neshoduje, datový blok se přenese do místního prostředí. Tato doba přenosu závisí na dostupné šířce pásma. Rychlost kontrolního součtu je několik GB za minutu. 

Pokud chcete zrychlit stahování dat, můžete nakonfigurovat svého agenta MARS, aby používal více vláken pro paralelizovat stahování. V [tomto dokumentu](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) najdete informace o tom, jak změnit vlákna stahování v agentovi.


## <a name="next-steps"></a>Další kroky

Po **potvrzení**můžete zahájit *reverzní replikaci*. Tím se zahájí ochrana virtuálního počítače z místního prostředí zpátky do Azure. Tím se replikují jenom změny, protože virtuální počítač je v Azure vypnutý, a proto odesílá jenom rozdílové změny.
