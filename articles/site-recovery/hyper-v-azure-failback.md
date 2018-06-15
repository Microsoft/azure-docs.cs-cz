---
title: Spustit na místní web pro virtuální počítače Hyper-v navrácení služeb po obnovení | Microsoft Docs
description: Azure Site Recovery koordinuje replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů a fyzických serverů. Další informace o navrácení služeb po obnovení z Azure do místního datového centra.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: rajanaki
ms.openlocfilehash: d344174ffa290b55386918ae19e2f792bb801a8a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
ms.locfileid: "29467118"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Spustit navrácení služeb po obnovení pro virtuální počítače Hyper-V

Tento článek popisuje, jak selhání zpět technologie Hyper-V virtuální počítače chráněné službou Site Recovery.

## <a name="prerequisites"></a>Požadavky
1. Ujistěte se, že jste četli podrobnosti o [různé typy navrácení služeb po obnovení](concepts-types-of-failback.md) a odpovídající upozornění.
1. Ujistěte se, že na serveru VMM primární lokality nebo hostitelský server Hyper-V je připojená k Azure.
2. Měli jste provedli **potvrdit** na virtuálním počítači.

## <a name="perform-failback"></a>Proveďte navrácení služeb po obnovení
Po převzetí služeb při selhání z primárního na sekundární umístění replikované virtuální počítače nejsou chráněny službou Site Recovery a sekundární umístění teď funguje jako aktivní umístění. K selhání zálohování virtuálních počítačů v plánu obnovení, spusťte plánované převzetí služeb při selhání ze sekundární lokality na primární následujícím způsobem. 
1. Vyberte **plány obnovení** > *recoveryplan_name*. Klikněte na tlačítko **převzetí služeb při selhání** > **plánované převzetí služeb při selhání**.
2. Na **potvrďte plánované převzetí služeb při selhání** vyberte zdrojové a cílové umístění. Všimněte si, že směr převzetí služeb při selhání. Pokud převzetí služeb při selhání z primárního fungovala jako očekávat a všechny virtuální počítače jsou v sekundárním umístění, které toto je pouze pro informaci.
3. Pokud po obnovení zpět z Azure vyberte nastavení v **synchronizace dat**:
    - **Synchronizace dat před převzetí služeb při selhání (pouze synchronizovat rozdílové změny)**– tato možnost minimalizuje prostoje pro virtuální počítače jako synchronizuje bez jejich vypínání. Provede následující kroky:
        - Fáze 1: Vytváří snímek virtuálního počítače v Azure a zkopíruje jej na hostitele Hyper-V na místě. Bude počítač dál běží v Azure.
        - Fáze 2: Vypne virtuální počítač v Azure tak, aby žádné nové změny dojít k dispozici. Závěrečné sady rozdílového změny přenesou na místním serveru a místní virtuální počítač je spuštění.

    - **Synchronizace dat během pouze převzetí služeb při selhání (úplná ke stažení)**– tato možnost je rychlejší.
        - Tato možnost je rychlejší, protože Očekáváme, že došlo ke změně většinu disku a nechceme při výpočtu kontrolního součtu. Provede stahování disku. Je také užitečné při místní virtuální počítač byl odstraněn.
        - Doporučujeme tuto možnost použijte, pokud jste byla spuštěna Azure nějakou dobu (v měsíci nebo více) nebo místní virtuální počítač je odstraněný. Tato možnost nebude provádět výpočty kontrolního součtu.


4. Pokud je povolené šifrování dat pro cloud, v **šifrovací klíč** vyberte certifikát, který byl vydán, pokud povolíte šifrování dat během instalace zprostředkovatele na serveru VMM.
5. Zahájit převzetí služeb při selhání. Můžete sledovat průběh převzetí služeb při selhání **úlohy** kartě.
6. Pokud jste vybrali možnost pro synchronizaci dat před převzetí služeb při selhání, po dokončení synchronizace počátečního data a jste připraveni vypnout virtuální počítače v Azure, klikněte na tlačítko **úlohy** název úlohy plánované převzetí služeb při selhání **dokončení převzetí služeb při selhání**. To vypne počítač Azure, přenáší nejnovější změny na místním virtuálním počítači a spuštění virtuálního počítače na místní.
7. Můžete teď se přihlásit k ověření na virtuální počítač je k dispozici podle očekávání.
8. Virtuální počítač je ve stavu čekání na potvrzení. Klikněte na tlačítko **potvrzení** potvrzení převzetí služeb při selhání.
9. Nyní, aby bylo možné dokončit navrácení služeb po obnovení, klikněte na tlačítko **zpětnou replikaci** ke spuštění ochrany virtuálního počítače v primární lokalitě.


Podle následujících pokynů k selhání zpět na původní primární lokality. Tento postup popisuje, jak spustit plánované převzetí služeb při selhání pro plán obnovení. Případně můžete spustit převzetí služeb při selhání pro jeden virtuální počítač na **virtuální počítače** kartě.


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Navrácení služeb po obnovení do alternativního umístění v prostředí Hyper-V
Pokud jste nasadili ochrany mezi [web Hyper-V a Azure](site-recovery-hyper-v-site-to-azure.md) budete muset možnost navrácení služeb po obnovení z Azure do alternativní místní umístění. To je užitečné, pokud je potřeba nastavit novou místní hardware. Zde je postup ho.

1. Pokud instalujete nový hardware nainstalujte Windows Server 2012 R2 a roli Hyper-V na serveru.
2. Vytvořte virtuální síťový přepínač se stejným názvem, který měl na původním serveru.
3. Vyberte **chráněné položky** -> **skupiny ochrany**  ->  <ProtectionGroupName>  ->  <VirtualMachineName> chcete navrácení služeb po obnovení a vyberte **plánované převzetí služeb při selhání**.
4. V **potvrďte plánované převzetí služeb při selhání** vyberte **vytvořit místní virtuální počítač Pokud neexistuje**.
5. V názvu hostitele ** vyberte nový server hostitele technologie Hyper-V, na kterém chcete umístit virtuální počítač.
6. Synchronizace dat, doporučujeme vybrat možnost **synchronizovat data před převzetí**. To minimalizuje prostoje pro virtuální počítače jako synchronizuje bez jejich vypínání. Ji provede následující akce:

    - Fáze 1: Vytváří snímek virtuálního počítače v Azure a zkopíruje jej na hostitele Hyper-V na místě. Bude počítač dál běží v Azure.
    - Fáze 2: Vypne virtuální počítač v Azure tak, aby žádné nové změny dojít k dispozici. Poslední sadu změn jsou přeneseny na místním serveru a na místním virtuálním počítači spuštění.
    
7. Kliknutím na značku zaškrtnutí zahájíte převzetí služeb při selhání (navrácení služeb po obnovení).
8. Po dokončení počáteční synchronizace a jste připraveni vypnout virtuální počítač v Azure, klikněte na tlačítko **úlohy** > <planned failover job> > **dokončení převzetí služeb při selhání**. To vypne počítač Azure, přenáší nejnovější změny na místním virtuálním počítači a spustí ho.
9. Může se přihlásit k virtuálnímu počítači na místě a ověří, zda že vše funguje podle očekávání. Pak klikněte na tlačítko **potvrdit** ukončíte převzetí služeb při selhání. Potvrzení odstraní virtuální počítač Azure a jeho disky a připraví virtuálního počítače měly by být znovu.
10. Klikněte na tlačítko **zpětnou replikaci** ke spuštění ochrany na místním virtuálním počítači.

    > [!NOTE]
    > Pokud zrušíte úlohu navrácení služeb po obnovení, když je v kroku synchronizace dat, místní virtuální počítač bude v poškozeném stavu. Je to proto, že synchronizace dat zkopíruje nejnovější data z disků virtuálního počítače Azure do místní datové disky, a až po dokončení synchronizace, diskových dat nemusí být v konzistentním stavu. Pokud On místní virtuální počítač se spustí po synchronizaci dat se zruší, nemusí spustit. Znovu spustit převzetí služeb při selhání na dokončení synchronizace dat.


## <a name="why-is-there-no-button-called-failback"></a>Proč je k dispozici žádné tlačítko názvem navrácení služeb po obnovení?
Na portálu neexistuje žádné explicitní gesto názvem navrácení služeb po obnovení. Navrácení služeb po obnovení je krok, kde jste se vraťte k primární lokalitě. Podle definice navrácení služeb po obnovení je, když jste převzetí služeb při selhání virtuálního počítače z obnovení zpět na primární.

Když iniciujete převzetí služeb při selhání, v okně informuje o směr, ve kterém je virtuální počítače přesunout, pokud je směr je z Azure do místní, je navrácení služeb po obnovení.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Proč je k dispozici pouze gesto plánované převzetí služeb při selhání pro navrácení služeb po obnovení?
Azure je prostředí s vysokou dostupností a virtuální počítače jsou vždy k dispozici. Navrácení služeb po obnovení je plánované aktivity, kde se rozhodnete pro malé výpadek tak, aby úlohy můžete začít znovu spouštět místně. Toto předpokládá, že nedošlo ke ztrátě dat. Proto je k dispozici pouze gesto plánované převzetí služeb při selhání, který bude vypnout virtuálních počítačů v Azure, stáhněte nejnovější změny a ujistěte se, že nedošlo ke ztrátě dat.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Potřebuji procesní server v Azure k navrácení služeb po obnovení do Hyper-v?
Ne, je potřeba server procesu, pouze pokud chráníte virtuální počítače VMware. Žádné další součásti je potřeba nasadit při ochraně nebo navrácení služeb po obnovení virtuálních počítačů technologie Hyper-v.


## <a name="time-taken-to-failback"></a>Čas potřebný k navrácení služeb po obnovení
Čas potřebný k dokončení synchronizace dat a spuštění virtuálního počítače závisí na různých faktorech. Umožňuje lépe pochopit, doba vám vysvětlíme, co se stane, že během synchronizace dat.

Synchronizace dat pořídí snímek disky virtuálního počítače a spustí kontrola blok po bloku a vypočítá jeho kontrolního součtu. Toto počítané kontrolního součtu posílá místní k porovnání s místními kontrolního součtu stejné bloku. V případě, že kontrolní součty shodují, nebudou převedeny datového bloku. Pokud neodpovídá, se přenáší datového bloku místně. Tato doba přenosu závisí na dostupné šířky pásma. Rychlost kontrolního součtu je několik GB za minutu. 

Pro urychlení stahování dat, můžete nakonfigurovat agenta MARS používat další podprocesy učinit paralelní stahování. Odkazovat [dokumentu zde](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) na tom, jak změnit stažení vláken v agentovi.


## <a name="next-steps"></a>Další kroky

Po **potvrdit**, můžete spustit *zpětnou replikaci*. Tím se spustí ochranu virtuálního počítače z místního zpět do Azure. Replikují se tak pouze změn od virtuálního počítače byla vypnuta v Azure a proto odešle jenom rozdílové změny.
