---
title: Hyper-V do Azure replikace architektura v Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje přehled komponent a architektury používané při replikaci místních virtuálních počítačů Hyper-V (bez nástroje VMM) do Azure s využitím služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 49059415c5f96eeb4dd871e7d2c8ae4ab9b2c12e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078354"
---
# <a name="hyper-v-to-azure-replication-architecture"></a>Hyper-V do Azure replikace architektury


Tento článek popisuje, architektury a procesy používané při replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů Hyper-V (VM) mezi místní hostitelé Hyper-V a Azure, pomocí [Azure Site Recovery](site-recovery-overview.md) služby.

Hostitelé Hyper-V je možné Volitelně můžete spravovat v privátních cloudech System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Architektonické součásti - Hyper-V bez VMM

Následující tabulka a obrázek poskytuje souhrnný přehled součásti použité pro replikaci Hyper-V do Azure, je-li hostitele Hyper-V nejsou spravovány nástrojem VMM.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a síť Azure. | Replikovaná data z místních virtuálních počítačů úloh je uložený v účtu úložiště. Virtuální počítače Azure se vytvoří s dat replikovaných úloh, když dojde k převzetí služeb při selhání z místní lokality.<br/><br/> Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Hyper-V** | Během nasazování Site Recovery shromáždíte hostitelé a clustery Hyper-V do lokality Hyper-V. Nainstalujete zprostředkovatele Azure Site Recovery a služby Recovery Services agent na každém hostiteli Hyper-V samostatné, nebo na každém uzlu clusteru Hyper-V. | Zprostředkovatel orchestruje replikaci pomocí služby Site Recovery přes internet. Agent Recovery Services se stará o replikaci dat.<br/><br/> Komunikace z poskytovatele i agenta je zabezpečená a šifrovaná. Šifrují se rovněž replikovaná data v úložišti Azure.
**Virtuální počítače Hyper-V** | Nejméně jeden virtuální počítač běží na Hyper-V. | Nic musí být nainstalován na virtuálních počítačích.


**Hyper-V do Azure architektury (bez VMM)**

![Architektura](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Architektonické součásti - Hyper-V pomocí nástroje VMM

Následující tabulka a obrázek poskytuje souhrnný přehled komponenty používané pro replikaci Hyper-V do Azure, když jsou hostitelé Hyper-V spravované v cloudech VMM.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a síť Azure. | Replikovaná data z místních virtuálních počítačů úloh je uložený v účtu úložiště. Virtuální počítače Azure se vytvoří s replikovanými daty, když dojde k převzetí služeb při selhání z místní lokality.<br/><br/> Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Server VMM** | Server VMM obsahuje jeden nebo více cloudů s hostiteli Hyper-V. | Nainstalovat zprostředkovatele služby Site Recovery orchestrovat replikaci pomocí služby Site Recovery na server VMM a zaregistrujte server v trezoru služby Recovery Services.
**Hostitel Hyper-V** | Jeden nebo několik hostitelů/clusterů Hyper-V spravovaných nástrojem VMM. |  Nainstalujte agenta služby Recovery Services na každém uzlu hostitele nebo clusteru Hyper-V.
**Virtuální počítače Hyper-V** | Jeden nebo několik virtuálních počítačů spuštěných na hostitelském serveru Hyper-V. | Na virtuálních počítačích není výslovně potřeba nic instalovat.
**Sítě** | Logické sítě a sítě virtuálních počítačů nastavené na serveru VMM. Síť virtuálních počítačů musí být propojena na logickou síť, která je přidružena ke cloudu. | Sítě virtuálních počítačů se mapují k virtuálním sítím Azure. Virtuální počítače Azure vytvořené po převzetí služeb při selhání, se přidají k síti Azure, který se mapuje na síť virtuálních počítačů.

**Hyper-V do Azure architektury (s VMM)**

![Komponenty](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Proces replikace

![Hyper-V do Azure replikace](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replikace a proces obnovení**


### <a name="enable-protection"></a>Povolení ochrany

1. Po povolení ochrany pro virtuální počítače Hyper-V (na webu Azure Portal nebo místně) se spustí **Povolení ochrany**.
2. Úloha zkontroluje, zda počítač splňuje požadavky, a potom vyvolá metodu [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), která nastaví replikaci s nastavením, které jste nakonfigurovali.
3. Úloha spustí počáteční replikaci vyvoláním metody [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), která zahájí úplnou replikaci virtuálního počítače a odešle virtuální disky virtuálního počítače do Azure.
4. Úlohu můžete sledovat na kartě **Úlohy**.      ![Seznam úloh](media/hyper-v-azure-architecture/image1.png) ![Podrobnosti povolení ochrany](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Počáteční replikace dat.

1. Při aktivaci počáteční replikace se [snímek virtuálního počítače Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) pořízení snímku.
2. Virtuální pevné disky na virtuálním počítači se replikují jeden po druhém, dokud se všechny nezkopírují do Azure. To může chvíli trvat, v závislosti na velikosti virtuálního počítače a šířka pásma sítě. [Zjistěte, jak](https://support.microsoft.com/kb/3056159) zvětšíte šířku pásma sítě.
3. Pokud dojde ke změnám na disku, kdy probíhá počáteční replikace, Hyper-V Replica Replication Tracker zaznamenává změny zaznamená jako protokoly replikace technologie Hyper-V (.hrl). Tyto protokolové soubory jsou umístěny ve stejné složce jako disky. Každý disk má přidružený soubor .hrl, který se odešle do sekundárního úložiště. Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se snímek virtuálního počítače odstraní.
5. Rozdílové změny na disku v protokolu se synchronizují a sloučí s nadřazeným diskem.


### <a name="finalize-protection-process"></a>Dokončení procesu ochrany

1. Po dokončení počáteční replikace **dokončit ochranu na virtuálním počítači** úlohy spustí. Nakonfiguruje Síťová a další postreplikační nastavení tak, aby virtuální počítač je chráněný.
2. V této fázi můžete zkontrolovat nastavení virtuálního počítače, abyste měli jistotu, že je připravený pro převzetí služeb při selhání. Můžete spustit zotavení po havárii (testovací převzetí služeb) pro virtuální počítač, zkontrolujte, že rutina selže, než podle očekávání. 


## <a name="delta-replication"></a>Rozdílová replikace

1. Po počáteční replikaci se zahájí rozdílová replikace, v souladu se zásadami replikace.
2. Technologie Hyper-V Replica Replication Tracker zaznamenává změny na virtuální pevný disk souborech .hrl. Každý disk nakonfigurovaný pro replikaci má přidružený soubor .hrl.
3. V protokolu se odešle do účtu úložiště zákazníka. Když protokol je při přenosu do Azure, se sledují změny na primárním disku do jiného souboru protokolu, ve stejné složce.
4. Během počáteční a rozdílové replikace můžete sledovat virtuální počítač na webu Azure Portal.

### <a name="resynchronization-process"></a>Procesu synchronizace

1. Pokud rozdílová replikace selže a úplná replikace by byla náročná, pokud jde o šířku pásma nebo čas, pak se virtuální počítač označí pro resynchronizaci.
    - Pokud například soubory .hrl dosáhnou 50 % velikosti disku, pak se u virtuálního počítače stanoví nutnost resynchronizace.
    -  Ve výchozím nastavení je Opětovná synchronizace naplánovat automatické spouštění mimo pracovní dobu.
1.  Opětovná synchronizace – odesílá pouze rozdílová data.
    - Minimalizuje množství dat odesílaných v rámci výpočtů kontrolních součtů zdrojové a cílové virtuální počítače.
    - Využívá algoritmus vytváření bloků pevnou, kde jsou zdrojové a cílové soubory rozdělené do pevných bloků.
    - Jsou generovány kontrolních součtů pro každý blok. Tyto jsou porovnány pro určení, které bloky ze zdroje je potřeba použít na cíl.
2. Po dokončení resynchronizace by měla pokračovat normální rozdílová replikace.
3. Pokud nechcete čekat výchozí resynchronizace mimo pracovní dobu, můžete znovu synchronizovat virtuální počítač ručně. Například, pokud dojde k výpadku. Chcete-li to provést na webu Azure Portal, vyberte virtuální počítač > **opakovanou synchronizaci**.

    ![Ruční resynchronizace](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Opakujte proces

Pokud dojde k chybě replikace, je předdefinován opakovaný pokus. Opakování je klasifikován, jak je popsáno v tabulce.

**Kategorie** | **Podrobnosti**
--- | ---
**Neopravitelné chyby** | Pokus se nebude opakovat. Stav virtuálního počítače bude **Kritický** a bude nutný zásah správce.<br/><br/> Příklady těchto chyb porušený řetězec virtuálních pevných disků, neplatný stav replikovaného virtuálního počítače, chyby ověřování sítě, chyby autorizace a virtuální počítač nebyl nalezen chyby (pro samostatné servery Hyper-V.
**Opravitelné chyby** | Pokusy se budou opakovat v každém intervalu replikace a pomocí exponenciální regrese se bude od počátku prvního pokusu zvětšovat interval opakování o 1, 2, 4, 8 a 10 minut. Pokud chyba přetrvává, bude se pokus opakovat každých 30 minut. Příklady patří chyby sítě, chyby místu na disku a nedostatek paměti.



## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

1. Můžete spustit plánované nebo neplánované převzetí služeb při selhání z místních virtuálních počítačů Hyper-V do Azure. Pokud spustíte plánovanou operaci, dojde k ukončení zdrojových virtuálních počítačů, aby se zcela předešlo možné ztrátě dat. Spusťte neplánované převzetí služeb při selhání, pokud primární lokality není přístupná.
2. Můžete převzetí služeb při selhání jednoho počítače nebo vytvořit plány obnovení a orchestrovat převzetí služeb při selhání více počítačů.
3. Spuštění převzetí služeb při selhání. Po dokončení první fázi převzetí služeb při selhání byste měli vidět vytvořené replikované virtuální počítače v Azure. Virtuálnímu počítači můžete přiřadit veřejnou IP adresu, když je to třeba.
4. Po potvrzení procesu převzetí služeb při selhání, chcete-li začít používat úlohu na replikovaném virtuálním počítači Azure.

Po znovu spuštěn a je v místní infrastruktuře, můžete službu navrátit. Ve třech fázích, dojde k navrácení služeb po obnovení:

1. Pusťte se plánované převzetí služeb při selhání z Azure do místní lokality:
    - **Minimalizovat prostoje**: Pokud použijete tuto možnost Site Recovery synchronizuje data před převzetí služeb při selhání. Kontroluje bloky změněná data a stáhne do místní lokality při udržuje virtuálním počítači Azure spuštěný, minimalizace výpadků. Když ručně zadáte, že by se měl dokončit převzetí služeb při selhání, vypnutí virtuálního počítače Azure, se zkopírují všechny finální rozdílové změny a spustí převzetí služeb při selhání.
    - **Úplné stažení**: pomocí této možnosti se data synchronizují během převzetí služeb při selhání. Tato možnost umožňuje stáhnout celý disk. Je rychlejší, protože se počítají žádné kontrolní součty, ale není k dispozici další prostoje. Tuto možnost použijte, pokud běží repliky virtuálních počítačů Azure nějakou dobu, nebo pokud místní virtuální počítač byl odstraněn.
    - **Vytvoření virtuálního počítače**: můžete vybrat selhání zpět do stejného virtuálního počítače nebo do alternativní virtuálního počítače. Můžete určit, že by měl Site Recovery vytvořte virtuální počítač, pokud ještě neexistuje.

2. Po dokončení počáteční synchronizace, je vybrat k dokončení převzetí služeb. Po dokončení, přihlaste se na místní virtuální počítač, zkontrolujte, že všechno funguje podle očekávání. Na webu Azure Portal uvidíte, že virtuální počítače Azure byly zastaveny.
3.  Po potvrzení převzetí služeb při selhání skončí a začít používat úlohu z místní virtuální počítač znovu.
4. Po úloh se nepodařilo vrátit, povolíte zpětnou replikaci, tak, aby místní virtuální počítače replikovat do Azure znovu.



## <a name="next-steps"></a>Další postup


Postupujte podle [v tomto kurzu](tutorial-prepare-azure.md) začít s technologií Hyper-V do Azure replikace.


