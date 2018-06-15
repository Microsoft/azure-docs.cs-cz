---
title: Technologie Hyper-V do Azure replikace architektury v Azure Site Recovery | Microsoft Docs
description: Tento článek obsahuje přehled komponent a architektury používané při replikaci místních virtuálních počítačů Hyper-V (bez nástroje VMM) do Azure s využitím služby Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: 11ecc341977efb5c815581a77383d1c8cae51ed6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894326"
---
# <a name="hyper-v-to-azure-replication-architecture"></a>Technologie Hyper-V do architektury Azure replikace


Tento článek popisuje architektuře a procesech používá při replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů technologie Hyper-V (VM) mezi místními hostitelů Hyper-V a Azure, pomocí [Azure Site Recovery](site-recovery-overview.md) služby.

Hostitelé Hyper-V můžete volitelně spravované v privátních cloudech System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Komponenty architektury, technologie Hyper-V bez VMM

Následující obrázek a tabulka poskytují souhrnné zobrazení komponenty použité pro replikaci technologie Hyper-V do Azure, když hostitelů Hyper-V nejsou spravované přes VMM.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a sítě Azure. | Replikovaná data z místní úlohy virtuálních počítačů je uložený v účtu úložiště. Virtuální počítače Azure vytvořené mají dat replikovaných úloh, když dojde k převzetí služeb při selhání z vaší místní lokalitě.<br/><br/> Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Hyper-V** | Během nasazování Site Recovery shromažďovat hostitelů Hyper-V a clustery do lokalit Hyper-V. Nainstalujte zprostředkovatele Azure Site Recovery a obnovení služby agenta na každém hostiteli technologie Hyper-V samostatné nebo na každém uzlu clusteru technologie Hyper-V. | Zprostředkovatel orchestruje replikaci pomocí služby Site Recovery přes internet. Agent Recovery Services se stará o replikaci dat.<br/><br/> Komunikace z poskytovatele i agenta je zabezpečená a šifrovaná. Šifrují se rovněž replikovaná data v úložišti Azure.
**Virtuální počítače Hyper-V** | Jeden nebo více virtuálních počítačů spuštěných na Hyper-V. | Nic musí být nainstalován na virtuálních počítačích.


**Technologie Hyper-V do Azure architektury (bez VMM)**

![Architektura](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Komponenty architektury, technologie Hyper-V s nástrojem VMM

Následující obrázek a tabulka poskytují souhrnné zobrazení komponenty používané pro replikaci technologie Hyper-V do Azure, když jsou spravované hostitele Hyper-V v cloudech VMM.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a sítě Azure. | Replikovaná data z místní úlohy virtuálních počítačů je uložený v účtu úložiště. Virtuální počítače Azure vytvořené mají replikovaná data, když dojde k převzetí služeb při selhání z vaší místní lokalitě.<br/><br/> Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Server VMM** | Server VMM obsahuje jeden nebo více cloudů s hostiteli Hyper-V. | Instalace zprostředkovatele služby Site Recovery na serveru VMM pro orchestraci replikace pomocí Site Recovery a zaregistrujte server v trezoru služeb zotavení.
**Hostitel Hyper-V** | Jeden nebo několik hostitelů/clusterů Hyper-V spravovaných nástrojem VMM. |  Nainstalujte agenta služeb zotavení na každém uzlu hostitele nebo cluster technologie Hyper-V.
**Virtuální počítače Hyper-V** | Jeden nebo několik virtuálních počítačů spuštěných na hostitelském serveru Hyper-V. | Na virtuálních počítačích není výslovně potřeba nic instalovat.
**Sítě** | Logické sítě a sítě virtuálních počítačů nastavené na serveru VMM. Síť virtuálních počítačů musí být propojena na logickou síť, která je přidružena ke cloudu. | Sítě virtuálních počítačů jsou namapované na virtuálních sítí Azure. Když jsou virtuální počítače Azure vytvořené po převzetí služeb při selhání, jsou přidány k síti Azure, který je namapovaný na síť virtuálních počítačů.

**Technologie Hyper-V do Azure architektury (s nástrojem VMM)**

![Komponenty](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Proces replikace

![Technologie Hyper-V na Azure replikaci](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Proces replikace a obnovení**


### <a name="enable-protection"></a>Povolení ochrany

1. Po povolení ochrany pro virtuální počítače Hyper-V (na webu Azure Portal nebo místně) se spustí **Povolení ochrany**.
2. Úloha zkontroluje, zda počítač splňuje požadavky, a potom vyvolá metodu [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), která nastaví replikaci s nastavením, které jste nakonfigurovali.
3. Úloha spustí počáteční replikaci vyvoláním metody [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), která zahájí úplnou replikaci virtuálního počítače a odešle virtuální disky virtuálního počítače do Azure.
4. Úlohu můžete sledovat na kartě **Úlohy**.      ![Seznam úloh](media/hyper-v-azure-architecture/image1.png)![Podrobnosti povolení ochrany](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Počáteční replikaci dat

1. Když se aktivuje počáteční replikace [snímku virtuálního počítače technologie Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) pořízení snímku.
2. Virtuální pevné disky na virtuální počítač se replikují jeden po druhém, dokud se všechny nezkopírují do Azure. To může nějakou dobu trvat, v závislosti na velikosti virtuálního počítače a šířku pásma sítě. [Zjistěte, jak](https://support.microsoft.com/kb/3056159) zvýšit šířku pásma sítě.
3. Pokud dojde k změny na disku, když probíhá počáteční replikace, Hyper-V Replica Replication Tracker sleduje změny jako protokoly replikace technologie Hyper-V (.hrl). Tyto protokolové soubory jsou umístěny ve stejné složce jako disky. Každý disk má přidružený soubor .hrl, který je odešle do sekundárního úložiště. Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se snímek virtuálního počítače odstraní.
5. Rozdílové změny na disku v protokolu se synchronizují a sloučí s nadřazeným diskem.


### <a name="finalize-protection-process"></a>Dokončit proces ochrany

1. Po dokončení počáteční replikace **dokončit ochranu na virtuálním počítači** úlohy spustí. Konfiguruje sítě a další postreplikační nastavení tak, aby virtuální počítač je chráněný.
2. V této fázi můžete zkontrolovat nastavení virtuálního počítače a ujistěte se, že je připravený pro převzetí služeb při selhání. Můžete spustit postupu zotavení po havárii (převzetí služeb při selhání) pro virtuální počítač, zkontrolujte, že rutina selže přes podle očekávání. 


## <a name="delta-replication"></a>Rozdílová replikace

1. Po počáteční replikaci zahájí rozdílová replikace, v souladu se zásadami replikace.
2. Technologie Hyper-V Replica Replication Tracker sleduje změny virtuální pevný disk jako soubory .hrl. Každý disk nakonfigurovaný pro replikaci má přidružený soubor .hrl.
3. Protokol se odesílají do účtu úložiště zákazníka. Pokud protokol je při přenosu do Azure, se sledují změny na primární disku v souboru protokolu, ve stejné složce.
4. Během počáteční a rozdílové replikace můžete monitorovat virtuální počítač na portálu Azure.

### <a name="resynchronization-process"></a>Proces opakované synchronizace

1. Pokud rozdílová replikace selže a úplná replikace by byla náročná, pokud jde o šířku pásma nebo čas, pak se virtuální počítač označí pro resynchronizaci.
    - Pokud například soubory .hrl dosáhnou 50 % velikosti disku, pak se u virtuálního počítače stanoví nutnost resynchronizace.
    -  Ve výchozím nastavení je opětovnou synchronizaci naplánovat automatické spouštění mimo pracovní dobu.
1.  Opakovaná synchronizace odešle jenom rozdílová data.
    - Minimalizuje množství dat odeslaných výpočtů kontrolních součtů zdrojové a cílové virtuálních počítačů.
    - Použije algoritmus vytváření bloků-block, kde zdrojové a cílové soubory dělí do bloků, pevné.
    - Jsou generovány, kontrolní součty pro každého bloku. Tyto budou porovnány s určit, které bloky ze zdroje musí být použity k cíli.
2. Po dokončení resynchronizace by měla pokračovat normální rozdílová replikace.
3. Pokud nechcete čekat na opětovnou synchronizaci výchozí mimo dobu, můžete znovu synchronizovat virtuální počítač ručně. Například, pokud dojde k výpadku. Chcete-li to provést na portálu Azure, vyberte virtuální počítač > **Opětovná synchronizace**.

    ![Ruční resynchronizace](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Proces opakujte

Pokud dojde k chybě replikace, je předdefinován opakovaný pokus. Opakování je klasifikace podle popisu v tabulce.

**Kategorie** | **Podrobnosti**
--- | ---
**Neopravitelné chyby** | Pokus se nebude opakovat. Stav virtuálního počítače bude **Kritický** a bude nutný zásah správce.<br/><br/> Příklady tyto chyby jsou porušený řetězec virtuálního pevného disku, neplatném stavu pro repliku virtuálního počítače, sítě chybám při ověřování, autorizace chyby, a virtuálních počítačů Chyby nenalezení (pro samostatné servery Hyper-V.
**Opravitelné chyby** | Pokusy se budou opakovat v každém intervalu replikace a pomocí exponenciální regrese se bude od počátku prvního pokusu zvětšovat interval opakování o 1, 2, 4, 8 a 10 minut. Pokud chyba přetrvává, bude se pokus opakovat každých 30 minut. Tyto příklady chyby sítě, nízkou disku chyb a nedostatek paměti.



## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

1. Plánovaném nebo neplánovaném převzetí služeb při selhání můžete spustit z místní virtuální počítače Hyper-V do Azure. Pokud spustíte plánovanou operaci, dojde k ukončení zdrojových virtuálních počítačů, aby se zcela předešlo možné ztrátě dat. Spusťte neplánované převzetí služeb při selhání, pokud primární lokalita není přístupná.
2. Můžete převzít jeden počítač nebo vytvořit plány obnovení, orchestraci převzetí služeb při selhání více počítačů.
3. Při spuštění převzetí služeb při selhání. Po dokončení první fázi převzetí služeb při selhání byste měli vidět vytvoření repliky virtuálních počítačů v Azure. Virtuálnímu počítači můžete přiřadit veřejnou IP adresu, když je to třeba.
4. Potom potvrzení převzetí služeb při selhání, spusťte z repliky virtuálního počítače Azure přístup k zatížení.

Po znovu spuštěn a je vaše místní infrastruktura, může selhat zpět. Navrácení služeb po obnovení skládá ze tří fází:

1. Ji plánované převzetí služeb při selhání z Azure do místní lokality:
    - **Minimalizovat prostoje**: Pokud použijete tuto možnost obnovení lokality synchronizuje data před převzetí služeb při selhání. Zjistí bloky změněná data a soubory ke stažení je na místní lokalitu při virtuálního počítače Azure bude spuštěna, minimalizovat prostoje. Když ručně zadáte, že by se měla dokončit převzetí služeb při selhání, se vypne virtuální počítač Azure, zkopírují všechny poslední rozdílové změny a spustí převzetí služeb při selhání.
    - **Úplné stažení**: pomocí této možnosti data synchronizována během převzetí služeb při selhání. Tato možnost stáhne celý disk. Je rychlejší vzhledem k tomu, že jsou vypočítávány žádné kontrolní součty, ale dojde k výpadku Další. Tuto možnost použijte, pokud jste byla spuštěna repliky virtuálních počítačů Azure nějakou dobu, nebo místní virtuální počítač byl odstraněn.
    - **Vytvoření virtuálního počítače**: můžete vybrat selhání zpět do stejného virtuálního počítače nebo do alternativní virtuálních počítačů. Můžete určit, že Site Recovery by měl vytvořit virtuální počítač, pokud ještě neexistuje.

2. Po dokončení počáteční synchronizace vyberete dokončení převzetí služeb při selhání. Až se dokončí, můžete se přihlásit na místní počítač a zkontrolovat, zda že vše funguje podle očekávání. Na portálu Azure uvidíte, že byly zastaveny virtuálních počítačích Azure.
3.  Poté potvrďte převzetí služeb při selhání dokončit a spustit znovu přistoupit ke zatížení z virtuálního počítače na místě.
4. Po úlohy se nezdařilo zpět, povolíte zpětnou replikaci, tak, aby virtuální počítače na místní replikaci do Azure znovu.



## <a name="next-steps"></a>Další postup


Postupujte podle [v tomto kurzu](tutorial-prepare-azure.md) začít pracovat s technologií Hyper-V na Azure replikaci.


