---
title: Spustit během po havárii virtuálních počítačů Hyper-v navrácení služeb po obnovení z Azure do místního | Dokumentace Microsoftu
description: Zjistěte, jak převzít služby při zálohování virtuálních počítačů Hyper-V do místní lokality během zotavení po havárii do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: b841dee766399f1e3c7325d2ab67e342dfa8657a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211855"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Spuštění navrácení služeb po obnovení pro virtuální počítače Hyper-V

Tento článek popisuje, jak převzít služby při back Hyper-V virtuální počítače chráněné službou Site Recovery.

## <a name="prerequisites"></a>Požadavky
1. Ujistěte se, že jste četli podrobnosti o [různé typy navrácení služeb po obnovení](concepts-types-of-failback.md) a odpovídající upozornění.
1. Ujistěte se, že server VMM v primární lokalitě nebo hostitelský server Hyper-V je připojený k Azure.
2. Měli jste provedli **potvrzení** na virtuálním počítači.

## <a name="perform-failback"></a>Provést navrácení služeb po obnovení
Po převzetí služeb při selhání z primárního do sekundárního umístění replikované virtuální počítače nejsou chráněny službou Site Recovery a sekundární umístění je teď funguje jako aktivní. Selhání zálohování virtuálních počítačů v plánu obnovení, spusťte plánované převzetí služeb při selhání ze sekundární lokality na primární, následujícím způsobem. 
1. Vyberte **plány obnovení** > *recoveryplan_name*. Klikněte na tlačítko **převzetí služeb při selhání** > **plánované převzetí služeb při selhání**.
2. Na **potvrďte plánované převzetí služeb při selhání** zvolte zdrojovým a cílovým umístěním. Všimněte si směr převzetí služeb při selhání. Pokud převzetí služeb při selhání z primárního pracoval jako očekávají a jsou všechny virtuální počítače v sekundární lokalitě, jedná se pouze pro informaci.
3. Pokud přebíráte služby obnovení z Azure vyberte nastavení v **synchronizace dat**:
    - **Synchronizovat data před převzetí služeb při selhání (pouze synchronizovat rozdílové změny)**– tuto možnost minimalizuje prostoje pro virtuální počítače, jak synchronizuje bez tyto služby vypnout. Provádí následující kroky:
        - Fáze 1: Pořídí snímek virtuálního počítače v Azure a zkopíruje ho do místního hostitele Hyper-V. Počítač se bude spouštět dál v Azure.
        - Fáze 2: Vypne virtuální počítač v Azure tak, aby žádné nové změny dojde k dispozici. Obsahuje závěrečnou sadu rozdílové změny se přenáší na místním serveru a spuštění virtuálního počítače místní databáze.

    - **Synchronizovat data pouze převzetí služeb při selhání (úplné stažení)**– tato možnost je rychlejší.
        - Tato možnost je rychlejší, protože Očekáváme, že většina disku se změnil, a nechceme a ztrácet čas v výpočet kontrolního součtu. Provede stahování disku. Je také užitečné při místní virtuální počítač se odstranil.
        - Doporučujeme tuto možnost použijte, pokud běží Azure nějakou dobu (za měsíc nebo déle) nebo místní virtuální počítač se odstranil. Tato možnost nebude provádět výpočty kontrolního součtu.


4. Pokud je povolené šifrování dat pro cloud, v **šifrovací klíč** vyberte certifikát, který byl vydán když povolíte šifrování dat během instalace zprostředkovatele na serveru VMM.
5. Zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na kartě **Úlohy**.
6. Pokud jste vybrali možnost Synchronizovat data před převzetí služeb při selhání, po dokončení počáteční synchronizace a jste připraveni vypnout virtuální počítače v Azure, klikněte na tlačítko **úlohy** > název úlohy >  **Dokončete převzetí služeb při selhání**. To vypne počítač Azure, přenese na místním virtuálním počítači nejnovější změny a spustí virtuální počítač na místní.
7. Můžete teď přihlásit se do virtuálního počítače, abyste ověřili, že je k dispozici podle očekávání.
8. Virtuální počítač je ve stavu čekání potvrzení. Klikněte na tlačítko **potvrzení** potvrzení převzetí služeb při selhání.
9. K dokončení navrácení služeb po obnovení, klikněte na tlačítko **reverzní replikaci** ke spuštění ochrany virtuálního počítače v primární lokalitě.


Postupujte podle následujících procedur selhání zpátky do původní primární lokality. Tento postup popisuje, jak spustit plánované převzetí služeb při selhání pro plán obnovení. Alternativně můžete spustit převzetí služeb při selhání pro jeden virtuální počítač na **virtuálních počítačů** kartu.


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Navrácení služeb po obnovení do alternativního umístění v prostředí Hyper-V
Pokud jste nasadili ochrany mezi oblastí [z lokality Hyper-V a Azure](site-recovery-hyper-v-site-to-azure.md) budete muset možnost navrácení služeb po obnovení z Azure do alternativní místní umístění. To je užitečné, pokud je potřeba nastavit novou místní hardware. Tady je postup jeho.

1. Pokud nastavujete nový hardware nainstalujte Windows Server 2012 R2 a roli Hyper-V na serveru.
2. Vytvoření přepínače virtuální sítě se stejným názvem, který měl na původním serveru.
3. Vyberte **chráněné položky** -> **skupiny ochrany**  ->  <ProtectionGroupName>  ->  <VirtualMachineName> chcete navrácení služeb po obnovení a vyberte **plánované Převzetí služeb při selhání**.
4. V **potvrďte plánované převzetí služeb při selhání** vyberte **vytvořit místní virtuální počítač neexistuje-li**.
5. V názvu hostitele ** vyberte nový server hostitele technologie Hyper-V, na kterém chcete umístit virtuální počítač.
6. Synchronizace dat doporučujeme, že vyberte možnost k synchronizaci dat před převzetí služeb při selhání. Tím se minimalizují prostoje pro virtuální počítače jako synchronizuje bez tyto služby vypnout. To provede následující akce:

    - Fáze 1: Pořídí snímek virtuálního počítače v Azure a zkopíruje ho do místního hostitele Hyper-V. Počítač se bude spouštět dál v Azure.
    - Fáze 2: Vypne virtuální počítač v Azure tak, aby žádné nové změny dojde k dispozici. Poslední sady změn se přenáší do místního serveru a spuštění virtuálního počítače místní databáze.
    
7. Kliknutím na značku zaškrtnutí zahájíte převzetí služeb při selhání (navrácení služeb po obnovení).
8. Po dokončení počáteční synchronizace a jste připraveni vypnout virtuální počítač v Azure, klikněte na tlačítko **úlohy** > <planned failover job> > **dokončení převzetí služeb při selhání**. To vypne počítač Azure, přenese na místním virtuálním počítači nejnovější změny a spustí ho.
9. Budete moct připojit na místním virtuálním počítači k ověření, že všechno funguje podle očekávání. Pak klikněte na tlačítko **potvrzení** dokončete převzetí služeb. Potvrzení odstraní virtuální počítač Azure a jeho disků a připraví chtít znovu ochráněny virtuální počítač.
10. Klikněte na tlačítko **reverzní replikaci** ke spuštění ochrany na místním virtuálním počítači.

    > [!NOTE]
    > Pokud zrušíte úlohu navrácení služeb po obnovení, i když je v kroku synchronizace dat, místní virtuální počítač bude v poškozeném stavu. Je to proto, že synchronizace dat zkopíruje nejnovější data z disků virtuálních počítačů Azure do místní datové disky, a dokud se synchronizace nedokončí, diskových dat nemusí být v konzistentním stavu. Pokud místní virtuální počítač, který naběhne po zrušení synchronizace dat, nemusí spustit. Znovu aktivovat neúspěšnou úplná synchronizace dat převzetí služeb při selhání.


## <a name="why-is-there-no-button-called-failback"></a>Proč není volána navrácení služeb po obnovení se neobjeví tlačítko?
Na portálu neexistuje žádné explicitní gesta volá navrácení služeb po obnovení. Navrácení služeb po obnovení je krok, kde můžete se vrátit do primární lokality. Podle definice je navrácení služeb po obnovení, když převezmete služby při selhání virtuálních počítačů z obnovení zpět na primární.

Při zahájení převzetí služeb při okno vás informuje o směr, ve kterém je přesunout, pokud směřuje z Azure do místního, jde navrácení služeb po obnovení virtuálních počítačů.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Proč je pouze gesto plánované převzetí služeb při selhání pro navrácení služeb po obnovení?
Azure je prostředí s vysokou dostupností a virtuální počítače byly vždycky dostupné. Navrácení služeb po obnovení je plánované aktivity, pokud se rozhodnete pro malý výpadek tak, aby úlohy můžete začít spouštět v místním znovu. To předpokládá, že bez ztráty. Proto je k dispozici pouze gesto plánované převzetí služeb při selhání, který se vypnout virtuální počítače v Azure, stáhněte si nejnovější změny a ujistěte se, že nedochází ke ztrátě dat.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Potřebuji procesový server v Azure pro navrácení služeb po obnovení do Hyper-v?
Ne, je procesový server vyžaduje pouze, když chráníte virtuální počítače VMware. Žádné další součásti jsou potřeba k nasazení při ochraně/navrácení služeb po obnovení virtuálních počítačů Hyper-v.


## <a name="time-taken-to-failback"></a>Čas potřebný k navrácení služeb po obnovení
Čas potřebný k dokončení synchronizace dat a spuštění virtuálního počítače závisí na různých faktorech. Poskytnout přehled o tom doba, jakou trvá, vám vysvětlíme, co se stane, že během synchronizace data.

Synchronizace dat pořídí snímek disky virtuálních počítačů a začne blok po bloku a vypočítá jeho kontrolní součet. Tento počítaný kontrolního součtu posílá do místního má být porovnán s místní kontrolní součet stejného bloku. V případě, že odpovídají kontrolní součty nebudou převedeny datového bloku. Pokud se neshoduje, přenese se do místního datového bloku. Tato doba přenosu závisí na dostupné šířce pásma. Rychlost kontrolního součtu je několik GB za minutu. 

Ke zrychlení stahování dat, můžete nakonfigurovat agenta MARS. použití více vláken paralelizovat stahování. Odkazovat [zdokumentovat](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) o tom, jak změnit stahování vlákna v agentovi.


## <a name="next-steps"></a>Další kroky

Po **potvrzení**, můžete zahájit *reverzní replikaci*. Tím se spustí ochranu virtuálního počítače z místního zpět do Azure. To bude replikovat jenom změny od virtuálního počítače byla vypnuta v Azure a proto odešle jenom rozdílové změny.
