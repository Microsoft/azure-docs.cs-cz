---
title: Architektura zotavení po havárii hyper-V v Azure Site Recovery
description: Tento článek obsahuje přehled součástí a architektury používané při nasazování zotavení po havárii pro místní virtuální počítače Hyper-V (bez VMM) do Azure se službou Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 022d6edad1e907173dfde3481e60d2523be087a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082669"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Architektura zotavení po havárii Hyper-V do Azure


Tento článek popisuje architekturu a procesy používané při replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů Hyper-V (VM) mezi místními hostiteli Hyper-V a Azure pomocí služby [Azure Site Recovery.](site-recovery-overview.md)

Hostitele Hyper-V lze volitelně spravovat v privátních cloudech Správce virtuálních strojů (VMM) system center.



## <a name="architectural-components---hyper-v-without-vmm"></a>Architektonické komponenty - Hyper-V bez VMM

Následující tabulka a grafika poskytují zobrazení na vysoké úrovni komponent používaných pro replikaci Hyper-V do Azure, když hostitelé Hyper-V nejsou spravováni vm.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a síť Azure. | Replikovaná data z místních úloh virtuálních aplikací se ukládají v účtu úložiště. Virtuální počítače Azure se vytvářejí s daty replikované úlohy, když dojde k převzetí služeb při selhání z místního webu.<br/><br/> Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Hyper-V** | Během nasazení site recovery shromažďujete hostitele a clustery Technologie Hyper-V do lokalit Hyper-V. Nainstalujete zprostředkovatele obnovení webu Azure a agenta služby recovery services na každém samostatném hostiteli Hyper-V nebo na každém uzlu clusteru Hyper-V. | Zprostředkovatel orchestruje replikaci pomocí služby Site Recovery přes internet. Agent Recovery Services se stará o replikaci dat.<br/><br/> Komunikace z poskytovatele i agenta je zabezpečená a šifrovaná. Šifrují se rovněž replikovaná data v úložišti Azure.
**Virtuální počítače Hyper-V** | Jeden nebo více virtuálních počítačů spuštěných na Hyper-V. | Nic nemusí být explicitně nainstalován na virtuálních počítačích.


**Architektura Hyper-V na Azure (bez VMM)**

![Architektura](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Architektonické komponenty - Hyper-V s VMM

Následující tabulka a grafika poskytují zobrazení na vysoké úrovni komponent používaných pro replikaci Hyper-V do Azure, když jsou hostitelé Hyper-V spravováni v cloudech VMM.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a síť Azure. | Replikovaná data z místních úloh virtuálních aplikací se ukládají v účtu úložiště. Virtuální počítače Azure se vytvářejí s replikovaná data, když dojde k převzetí služeb při selhání z místního webu.<br/><br/> Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Server VMM** | Server VMM obsahuje jeden nebo více cloudů s hostiteli Hyper-V. | Nainstalujete zprostředkovatele obnovení webu na server VMM, orchestrovat replikaci s site recovery a zaregistrovat server v trezoru služby obnovení.
**Hostitel Hyper-V** | Jeden nebo několik hostitelů/clusterů Hyper-V spravovaných nástrojem VMM. |  Agenta služby Recovery Services nainstalujete do každého hostitele nebo uzlu clusteru Hyper-V.
**Virtuální počítače Hyper-V** | Jeden nebo několik virtuálních počítačů spuštěných na hostitelském serveru Hyper-V. | Na virtuálních počítačích není výslovně potřeba nic instalovat.
**Síťové služby** | Logické sítě a sítě virtuálních počítačů nastavené na serveru VMM. Síť virtuálních montovny by měla být propojená s logickou sítí, která je přidružená ke cloudu. | Sítě virtuálních počítače jsou namapované na virtuální sítě Azure. Když se virtuální počítače Azure vytvoří po převzetí služeb při selhání, přidají se do sítě Azure, která je namapovaná na síť virtuálních počítačích.

**Architektura Hyper-V na Azure (s VMM)**

![Komponenty](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Proces replikace

![Replikace hyper-V na Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Proces replikace a obnovení**


### <a name="enable-protection"></a>Povolení ochrany

1. Po povolení ochrany pro virtuální počítače Hyper-V (na webu Azure Portal nebo místně) se spustí **Povolení ochrany**.
2. Úloha zkontroluje, zda počítač splňuje požadavky, a potom vyvolá metodu [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), která nastaví replikaci s nastavením, které jste nakonfigurovali.
3. Úloha spustí počáteční replikaci vyvoláním metody [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), která zahájí úplnou replikaci virtuálního počítače a odešle virtuální disky virtuálního počítače do Azure.
4. Úlohu můžete sledovat na kartě **Úlohy.**      ![](media/hyper-v-azure-architecture/image1.png) Seznam ![úloh Povolit přechod k podrobnostem ochrany](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Počáteční replikace dat

1. Při spuštění počáteční replikace je pořízen [snímek snímku virtuálního počítače Hyper-V.](https://technet.microsoft.com/library/dd560637.aspx)
2. Virtuální pevné disky na virtuálním počítači se replikují jeden po druhém, dokud se všechny nezkopírují do Azure. To může chvíli trvat, v závislosti na velikosti virtuálního počítače a šířce pásma sítě. [Přečtěte si, jak](https://support.microsoft.com/kb/3056159) zvýšit šířku pásma sítě.
3. Pokud dojde ke změnám disku během počáteční replikace, nástroj Sledování repliky replik y hyperv sleduje změny jako protokoly replikace Hyper-V (.hrl). Tyto soubory protokolu jsou umístěny ve stejné složce jako disky. Každý disk má přidružený soubor HRL, který je odeslán do sekundárního úložiště. Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se snímek virtuálního počítače odstraní.
5. Rozdílové změny na disku v protokolu se synchronizují a sloučí s nadřazeným diskem.


### <a name="finalize-protection-process"></a>Dokončení procesu ochrany

1. Po dokončení počáteční replikace se spustí **ochrana finalizace na úloze virtuálního počítače.** Konfiguruje nastavení sítě a další chod po replikaci, takže virtuální počítač je chráněný.
2. V této fázi můžete zkontrolovat nastavení virtuálního počítače a ujistěte se, že je připravený pro převzetí služeb při selhání. Můžete spustit zotavení po havárii vrták (test převzetí služeb při selhání) pro virtuální ho, zkontrolujte, zda je převzetí služeb při selhání podle očekávání. 


## <a name="delta-replication"></a>Rozdílová replikace

1. Po počáteční replikaci začíná rozdílová replikace v souladu se zásadami replikace.
2. Nástroj Sledování replik replik y Hyper-V sleduje změny na virtuálním pevném disku jako soubory HRL. Každý disk nakonfigurovaný pro replikaci má přidružený soubor .hrl.
3. Protokol je odeslán na účet úložiště zákazníka. Když je protokol při přenosu do Azure, změny na primárním disku jsou sledovány v jiném souboru protokolu ve stejné složce.
4. Během počáteční a delta replikace můžete sledovat virtuální počítač na webu Azure Portal.

### <a name="resynchronization-process"></a>Proces resynchronizace

1. Pokud rozdílová replikace selže a úplná replikace by byla náročná, pokud jde o šířku pásma nebo čas, pak se virtuální počítač označí pro resynchronizaci.
    - Pokud například soubory .hrl dosáhnou 50 % velikosti disku, pak se u virtuálního počítače stanoví nutnost resynchronizace.
    -  Ve výchozím nastavení je naplánováno automatické spuštění synchronizace mimo úřední hodiny.
1.  Resynchronizace odesílá pouze rozdílová data.
    - Minimalizuje množství dat odeslaných výpočetními kontrolními součty zdrojových a cílových virtuálních počítačů.
    - Používá algoritmus blokování pevných bloků, kde jsou zdrojové a cílové soubory rozděleny na pevné bloky.
    - Jsou generovány kontrolní součty pro každý blok. Ty jsou porovnány k určení, které bloky od zdroje je třeba použít na cíl.
2. Po dokončení resynchronizace by měla pokračovat normální rozdílová replikace.
3. Pokud nechcete čekat na výchozí resynchronizaci mimo hodiny, můžete znovu synchronizovat virtuální hospodařící ručně. Například pokud dojde k výpadku. Chcete-li to provést, vyberte na webu Azure Portal > **znovu synchronizovat**.

    ![Ruční resynchronizace](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Proces opakování

Pokud dojde k chybě replikace, je předdefinován opakovaný pokus. Opakování je klasifikováno tak, jak je popsáno v tabulce.

**Kategorie** | **Podrobnosti**
--- | ---
**Neopravitelné chyby** | Pokus se nebude opakovat. Stav virtuálního počítače bude **Kritický** a bude nutný zásah správce.<br/><br/> Příklady těchto chyb zahrnují poškozený řetězec virtuálního pevného disku, neplatný stav pro virtuální počítače repliky, chyby ověřování v síti, chyby autorizace a nenalezené chyby virtuálního počítače (pro samostatné servery Hyper-V.
**Opravitelné chyby** | Pokusy se budou opakovat v každém intervalu replikace a pomocí exponenciální regrese se bude od počátku prvního pokusu zvětšovat interval opakování o 1, 2, 4, 8 a 10 minut. Pokud chyba přetrvává, bude se pokus opakovat každých 30 minut. Mezi příklady patří chyby sítě, chyby nedostatku disku a podmínky nedostatku paměti.



## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

1. Můžete spustit plánované nebo neplánované převzetí služeb při selhání z místních virtuálních počítačů Hyper-V do Azure. Pokud spustíte plánovanou operaci, dojde k ukončení zdrojových virtuálních počítačů, aby se zcela předešlo možné ztrátě dat. Pokud primární lokalita není přístupná, spusťte neplánované převzetí služeb při selhání.
2. Můžete převzít službu při selhání jednoho počítače nebo vytvořit plány zotavení a orchestrovat převzetí služeb při selhání více počítačů.
3. Spouštět převzetí služeb při selhání. Po dokončení první fáze převzetí služeb při selhání, měli byste být schopni zobrazit vytvořené repliky virtuálních počítačů v Azure. Virtuálnímu počítači můžete přiřadit veřejnou IP adresu, když je to třeba.
4. Potom potvrdíte převzetí služeb při selhání a můžete začít přistupovat k úlohám z repliky virtuálního počítače Azure.

Až bude vaše místní infrastruktura znovu spuštěná, můžete ji znovu navrátit. K navrácení služeb po selhání dochází ve třech fázích:

1. Nastartovat plánované převzetí služeb při selhání z Azure na místní web:
    - **Minimalizovat prostoje**: Pokud použijete tuto možnost Site Recovery synchronizuje data před převzetím služeb při selhání. Kontroluje změněné datové bloky a stáhne je do místního webu, zatímco virtuální počítač Azure bude dál spuštěný a minimalizuje prostoje. Když ručně určíte, že převzetí služeb při selhání by měla být dokončena, virtuální počítač Azure se vypne, všechny konečné změny delta se zkopírují a převzetí služeb při selhání spustí.
    - **Úplné stažení**: S touto možností jsou data synchronizována během převzetí služeb při selhání. Tato možnost stáhne celý disk. Je to rychlejší, protože se nepočítají žádné kontrolní součty, ale dochází k dalším prostojům. Tuto možnost použijte, pokud už nějakou dobu spouštíte repliky virtuálních počítačů Azure nebo pokud byl odstraněn místní virtuální počítač.
    - **Vytvořit virtuální ho:** Můžete vybrat, chcete-li obnovit na stejný virtuální virtuální nebo alternativní virtuální hod. Můžete určit, že obnovení webu by měl vytvořit virtuální hod, pokud ještě neexistuje.

2. Po dokončení počáteční synchronizace vyberete k dokončení převzetí služeb při selhání. Po dokončení se můžete přihlásit k místnímu virtuálnímu počítači a zkontrolovat, že vše funguje podle očekávání. Na webu Azure Portal uvidíte, že virtuální počítače Azure byly zastavené.
3.  Potom potvrdíte převzetí služeb při selhání k dokončení a znovu zahájíte přístup k úlohě z místního virtuálního počítače.
4. Po selhání úloh se nezdařilo zpět, povolíte reverzní replikace, takže místní virtuální počítače replikovat do Azure znovu.



## <a name="next-steps"></a>Další kroky


Podle [tohoto kurzu](tutorial-prepare-azure.md) můžete začít s replikací Technologie Hyper-V do Azure.


