---
title: Převzetí služeb při selhání v Site Recovery | Dokumentace Microsoftu
description: Azure Site Recovery koordinuje replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů a fyzických serverů. Další informace o převzetí služeb při selhání do Azure nebo sekundárního datacentra.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 09/11/2018
ms.author: ponatara
ms.openlocfilehash: c9a2f258ca952ca36000e1ca0630fbde31ba7ba0
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391313"
---
# <a name="failover-in-site-recovery"></a>Převzetí služeb při selhání v Site Recovery
Tento článek popisuje, jak k převzetí služeb při selhání virtuálních počítačů a fyzických serverů chrání Site Recovery.

## <a name="prerequisites"></a>Požadavky
1. Než se pustíte převzetí služeb při selhání, proveďte [testovací převzetí služeb při selhání](site-recovery-test-failover-to-azure.md) zajistit, že vše funguje podle očekávání.
1. [Připravte sítě](site-recovery-network-design.md) na cílové umístění, než se pustíte převzetí služeb při selhání.  

Použijte následující tabulku vědět o možnosti převzetí služeb při selhání Azure Site Recovery. Tyto možnosti jsou také uvedeny pro převzetí služeb při selhání různé scénáře.

| Scénář | Požadavek na obnovení aplikace | Pracovní postup pro Hyper-V | Pracovní postup pro VMware
|---|--|--|--|
|Plánované převzetí služeb při selhání kvůli výpadku nadcházející datacenter| Nulová ztráta dat pro aplikace, kdy se provádí plánované aktivity| Pro Hyper-V Azure Site Recovery replikuje data s frekvencí kopírování, která je zadaná uživatelem. Plánované převzetí služeb při selhání se používá k přepsání četnost a replikace poslední změny předtím, než je zahájeno převzetí služeb při selhání. <br/> <br/> 1.    Naplánujte časové období údržby podle procesu správy změn vaši firmu. <br/><br/> 2. upozorněte uživatele na nadcházející výpadek. <br/><br/> 3. Přepněte do režimu offline aplikaci zaměřené na uživatele.<br/><br/>4. zahajte plánované převzetí služeb při selhání pomocí portálu Azure Site Recovery. Místní virtuální počítač je automaticky k jeho vypnutí.<br/><br/>Účinné používání ztrátě dat = 0 <br/><br/>Deník bodů obnovení je také součástí interval uchovávání dat pro uživatele, který chce použít staršího bodu obnovení. (24 hodin uchovávání dat pro technologii Hyper-V).| Azure Site Recovery pro replikaci z VMware, replikuje data neustále pomocí distribučního místa seznamu CRL. Převzetí služeb při selhání umožňuje uživateli možnost převzetí služeb při selhání na nejnovější data (včetně příspěvek aplikace vypnuto)<br/><br/> 1. Naplánovat časové období údržby podle procesu správy změn <br/><br/>2. upozornit uživatele na nadcházející výpadek <br/><br/>3.    Přepněte do režimu offline aplikaci zaměřené na uživatele. <br/><br/>4.  Zahajte plánované převzetí služeb při selhání pomocí portálu Azure Site Recovery k nejnovějšímu bodu po aplikace je offline. Pomocí možnosti "Neplánované převzetí služeb při selhání" na portál a vyberte nejnovější bod pro převzetí služeb při selhání. Místní virtuální počítač je automaticky k jeho vypnutí.<br/><br/>Účinné používání ztrátě dat = 0 <br/><br/>Deník bodů obnovení v intervalu se poskytuje pro zákazníky, kteří chce použít staršího bodu obnovení. (72 hodin dobu uchování pro replikaci z VMware).
|Převzetí služeb při selhání kvůli výpadku neplánované datacenter (přirozený nebo IT po havárii) | Minimální úniku informací u aplikace | 1. zahájit plán BCP organizace. <br/><br/>2. Zahájení neplánovaného převzetí služeb při selhání pomocí portálu Azure Site Recovery na nejnovější verzi nebo bodu z interval uchovávání dat (deník).| 1. Spustit plán BCP organizace. <br/><br/>2.  Zahájení neplánovaného převzetí služeb při selhání pomocí portálu Azure Site Recovery na nejnovější verzi nebo bodu z interval uchovávání dat (deník).


## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání
Tento postup popisuje, jak spustit převzetí služeb při selhání pro [plánu obnovení](site-recovery-create-recovery-plans.md). Alternativně můžete spustit převzetí služeb při selhání pro jeden virtuální počítač nebo fyzický server z **replikované položky** stránce, jak je popsáno [tady](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure).


![Převzetí služeb při selhání](./media/site-recovery-failover/Failover.png)

1. Vyberte **plány obnovení** > *recoveryplan_name*. Klikněte na tlačítko **převzetí služeb při selhání**
2. Na **převzetí služeb při selhání** obrazovky, vyberte **bod obnovení** převzetí služeb při selhání. Můžete použít jednu z následujících možností:
    1.  **Nejnovější**: Tato možnost spustí úlohu pomocí první zpracování všech dat poslaných do služby Site Recovery. Zpracování dat vytvoří bod obnovení pro každý virtuální počítač. Tohoto bodu obnovení se používá pro virtuální počítač během převzetí služeb při selhání. Tato možnost poskytuje nejnižší cíl (bodu obnovení rpo) jako virtuální počítač po převzetí služeb při selhání bude mít veškerá data, které jsou replikované do služby Site Recovery při aktivaci převzetí služeb při selhání.
    1.  **Nejnovější zpracovaný**: Tato možnost převezme služby při selhání všech virtuálních počítačů v plánu obnovení do nejnovějšího bodu obnovení, která již byla zpracována službou Site Recovery. Při provádění testovacího převzetí služeb při selhání virtuálního počítače, je zobrazen také časové razítko posledního bodu obnovení zpracované. Pokud provádíte převzetí služeb při selhání použít plán obnovení, můžete přejít na jednotlivých virtuálních počítačů a podívejte se na **nejnovější body obnovení** dlaždice k získání těchto informací. Protože se neztrácí žádný čas ke zpracování nezpracovaných dat, tato možnost poskytuje nízkou možnost převzetí služeb při selhání obnovení čas (RTO).
    1.  **Nejnovější konzistentní vzhledem k**: Tato možnost převezme služby při selhání všech virtuálních počítačů v plánu obnovení do nejnovějšího bodu obnovení konzistentní vzhledem k aplikaci, která již byla zpracována službou Site Recovery. Při provádění testovacího převzetí služeb při selhání virtuálního počítače, je zobrazen také časové razítko bodu nejnovější konzistentní vzhledem k obnovení. Pokud provádíte převzetí služeb při selhání použít plán obnovení, můžete přejít na jednotlivých virtuálních počítačů a podívejte se na **nejnovější body obnovení** dlaždice k získání těchto informací.
    1.  **Nejnovější více virtuálních počítačů zpracovat**: Tato možnost dostupná jenom pro plány obnovy, které mají nejméně jeden virtuální počítač s konzistencí pro víc virtuálních počítačů na. Virtuální počítače, které jsou součástí replikační skupiny převzetí služeb při nejnovější společné pro více virtuálních počítačů obnovení konzistentní vzhledem k bodu. Ostatní virtuální počítače převzetí služeb při selhání do bodu jejich nejnovější zpracované obnovení.  
    1.  **Nejnovější více virtuálních počítačů konzistentní**: Tato možnost dostupná jenom pro plány obnovy, které mají nejméně jeden virtuální počítač s ON konzistence více virtuálních počítačů. Virtuální počítače, které jsou součástí replikační skupiny převzetí služeb při nejnovější běžné konzistentní obnovení více virtuálních počítačů bodu. Ostatní virtuální počítače převzetí služeb při selhání jejich nejnovější bod obnovení konzistentní s aplikací.
    1.  **Vlastní**: Pokud provádíte test převzetí služeb při selhání virtuálního počítače, pak můžete použít tuto možnost, převzetí služeb při selhání konkrétnímu bodu obnovení.

    > [!NOTE]
    > Zvolte bod obnovení možnost je k dispozici, pouze pokud jste se převzetí služeb při selhání do Azure.
    >
    >


1. Pokud některé virtuální počítače v plánu obnovení byly převzetí služeb při selhání v předchozím spuštění a teď virtuální počítače jsou aktivní na zdrojovém i cílovém umístění, můžete použít **změnit směr** možnost se rozhodnout, směr, ve kterém převzetí služeb při selhání by mělo nastat.
1. Pokud jste už převzetí služeb při selhání do Azure a je povolené šifrování dat pro cloud (platí pouze v případě, že jste nastavili ochranu virtuálních počítačů Hyper-v ze serveru VMM), v **šifrovací klíč** vyberte certifikát, který byl vydán při povolování šifrování dat během instalace na serveru VMM.
1. Vyberte **vypnutí počítači před převzetí služeb při selhání** Pokud chcete, aby Site Recovery chcete pokusit před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání pokračovat i v případě, že selže jeho vypnutí.  

    > [!NOTE]
    > Pokud jsou chráněné virtuální počítače Hyper-v, pokusí se možnost vypnutí také synchronizovat místní data, která nebyla ještě nebyly odeslány do služby před aktivací převzetí služeb.
    >
    >

1. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**. I v případě, že dojde k chybám při neplánovaném převzetí služeb při selhání, spustí plánu obnovení, dokud se nedokončí.
1. Po převzetí služeb při selhání ověřte při protokolování se změnami do ní virtuální počítač. Pokud chcete přepnout na jiný bod obnovení virtuálního počítače, pak můžete použít **změnit bod obnovení** možnost.
1. Jakmile budete spokojeni s virtuálním počítačem, u kterého došlo k převzetí služeb při selhání, můžete převzetí služeb při selhání **Potvrdit**. **Potvrzení odstraní všechny body obnovení k dispozici ve službě** a **změnit bod obnovení** možnost už není k dispozici.

## <a name="planned-failover"></a>Plánované převzetí služeb při selhání
Virtuální počítače/fyzické servery, chránit pomocí Site Recovery také podporu **plánované převzetí služeb při selhání**. Plánované převzetí služeb při selhání je nulové ztráty převzetí služeb při selhání možnost dat. Při plánované převzetí služeb při selhání se aktivuje, nejprve zdrojové virtuální počítače jsou vypnutí, synchronizují nejnovější data a pak se aktivuje převzetí služeb při selhání.

> [!NOTE]
> Během převzetí služeb při selhání virtuálních počítačů Hyper-v z jedné místní lokality do jiné místní lokality, chcete-li se vrátit do primární místní lokality budete muset nejprve **zpětnou replikaci** virtuální počítač zpět do primární lokality a pak aktivační událost převzetí služeb při selhání. Pokud není k dispozici, pak před spuštěním na primárním virtuálním počítači **zpětnou replikaci** budete muset obnovit virtuální počítač ze zálohy.   
>
>
## <a name="failover-job"></a>Úlohy převzetí služeb při selhání

![Převzetí služeb při selhání](./media/site-recovery-failover/FailoverJob.png)

Když se aktivuje převzetí služeb při selhání, zahrnuje následující kroky:

1. Kontrola předpokladů: Tento krok zajistí, že jsou splněny všechny podmínky pro převzetí služeb při selhání
1. Převzetí služeb při selhání: Tento krok zpracuje data a zpřístupňuje je připravené tak, aby virtuální počítač Azure je možné vytvořit z ní. Pokud jste vybrali **nejnovější** bod obnovení, tento krok vytvoří bod obnovení z dat odesílaných do služby.
1. Začátek: Tento krok vytvoří virtuální počítač Azure pomocí dat zpracovaných v předchozím kroku.

> [!WARNING]
> **Nepřerušujte v průběhu převzetí služeb při selhání**: před spuštěním převzetí služeb při selhání, se zastaví replikace virtuálního počítače. Pokud jste **zrušit** v průběhu úlohy převzetí služeb při selhání zastaví, ale virtuální počítač nespustí replikaci. Replikaci nelze spustit znovu.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Doba trvání pro převzetí služeb při selhání do Azure

Převzetí služeb při selhání virtuálních počítačů v určitých případech vyžaduje velmi přechodný krok, který obvykle trvá přibližně 8 až 10 minut na dokončení. Čas potřebný k převzetí služeb při selhání bude vyšší než obvykle. v následujících případech:

* Virtuální počítače VMware pomocí služby mobility starší než 9.8 verze
* Fyzické servery
* Virtuální počítače VMware s Linuxem
* Virtuální počítače Hyper-V chráněné jako fyzické servery
* Virtuální počítače VMware, kde nejsou k dispozici jako následující ovladače spuštění ovladače
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* Virtuální počítače VMware, které nemají služba DHCP povoleno bez ohledu na to, zda jsou pomocí DHCP nebo statické IP adresy

Ve všech ostatních případech tento přechodný krok není povinný a doba trvání převzetí služeb při selhání je nižší.

## <a name="using-scripts-in-failover"></a>Pomocí skriptů v převzetí služeb při selhání
Můžete chtít automatizovat některé akce a převzetím služeb. Můžete použít skripty nebo [runbooky Azure automation](site-recovery-runbook-automation.md) v [plány obnovení](site-recovery-create-recovery-plans.md) to udělat.

## <a name="post-failover-considerations"></a>Důležité informace o převzetí služeb při selhání příspěvku
Účtování převzetí služeb při selhání může být vhodné vzít v úvahu tato doporučení:
### <a name="retaining-drive-letter-after-failover"></a>Zachování písmeno jednotky po převzetí služeb při selhání
Pokud chcete zachovat písmeno jednotky na virtuální počítače po převzetí služeb při selhání, můžete nastavit **zásada SAN** pro virtuální počítač na **OnlineAll**. [Přečtěte si další informace](https://support.microsoft.com/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Pokud chcete připojit k virtuálním počítačům Azure po převzetí služeb při selhání pomocí protokolu RDP nebo SSH, postupujte podle požadavků shrnutých v tabulce [tady](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Postupujte podle kroků popsaných [tady](site-recovery-failover-to-azure-troubleshoot.md) problémy řešit jakékoli připojení zveřejnit převzetí služeb při selhání.


## <a name="next-steps"></a>Další postup

> [!WARNING]
> Jakmile budete mít převzetí služeb při selhání virtuálních počítačů a je k dispozici v místním datovém centru, měli byste [ **znovunastavení ochrany** ](vmware-azure-reprotect.md) zpět virtuálních počítačů VMware v místním datovém centru.

Použití [ **plánované převzetí služeb při selhání** ](hyper-v-azure-failback.md) umožňuje **navrácení služeb po obnovení** virtuálních počítačů Hyper-v zpátky do místního z Azure.

Pokud jste prostřednictvím technologie Hyper-v virtuálního počítače do druhého v místním datovém centru spravován serverem VMM a je k dispozici primární datové centrum, pak použijte **zpětná replikace** možnosti spuštění replikace zpět na primární datové System Center.
