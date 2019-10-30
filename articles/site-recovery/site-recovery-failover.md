---
title: Převzetí služeb při selhání při zotavení po havárii pomocí Azure Site Recovery
description: Přečtěte si o selhání virtuálních počítačů a fyzických serverů během zotavení po havárii pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2019
ms.author: raynew
ms.openlocfilehash: 1585c5dbdecf11bbc6ef3dad63bf4f982c70f73e
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053776"
---
# <a name="fail-over-vms-and-physical-servers"></a>Převzetí služeb při selhání virtuálních počítačů a fyzických serverů 

Tento článek popisuje, jak převzetí služeb při selhání virtuálních počítačů a fyzických serverů, které jsou chráněné pomocí Site Recovery.

## <a name="prerequisites"></a>Předpoklady
1. Než provedete převzetí služeb při selhání, proveďte [Test převzetí služeb](site-recovery-test-failover-to-azure.md) při selhání, abyste zajistili, že vše funguje podle očekávání.
1. Než provedete převzetí služeb při selhání, [Připravte síť](site-recovery-network-design.md) na cílové umístění.  

Následující tabulku použijte k získání informací o možnostech převzetí služeb při selhání, které poskytuje Azure Site Recovery. Tyto možnosti jsou uvedeny také pro různé scénáře převzetí služeb při selhání.

| Scénář | Požadavek na obnovení aplikace | Pracovní postup pro Hyper-V | Pracovní postup pro VMware
|---|--|--|--|
|Plánované převzetí služeb při selhání kvůli nadcházejícímu výpadku datového centra| Nulová ztráta dat pro aplikaci při provádění plánované aktivity| V případě technologie Hyper-V replikuje ASR data při kopírování, který určuje uživatel. Plánované převzetí služeb při selhání se používá k přepsání četnosti a k replikaci konečných změn před zahájením převzetí služeb při selhání. <br/> <br/> 1. Naplánujte časový interval pro správu a údržbu podle procesu správy změn vašeho podniku. <br/><br/> 2. upozorněte uživatele na nadcházející výpadky. <br/><br/> 3. Převeďte aplikaci s uživatelským rozhraním do režimu offline.<br/><br/>4. Inicializujte plánované převzetí služeb při selhání pomocí portálu ASR. Místní virtuální počítač se automaticky vypíná.<br/><br/>Efektivní ztráta dat aplikace = 0 <br/><br/>Deník bodů obnovení je také k dispozici v okně uchovávání pro uživatele, který chce použít starší bod obnovení. (24 hodin uchování pro Hyper-V). Pokud se replikace zastavila mimo časový rámec okna pro uchovávání informací, zákazníci můžou dál převzít služby při selhání s využitím nejnovějších dostupných bodů obnovení. | V případě VMware replikuje ASR data průběžně pomocí CDP. Převzetí služeb při selhání uživateli poskytuje možnost převzetí služeb při selhání pro nejnovější data (včetně vypnutí post aplikace).<br/><br/> 1. Naplánování časového období údržby podle procesu správy změn <br/><br/>2. upozorněte uživatele na nadcházející výpadky <br/><br/>3. Převeďte aplikaci s uživatelským rozhraním do režimu offline.<br/><br/>4. Inicializujte plánované převzetí služeb při selhání pomocí portálu ASR až do posledního bodu po dokončení aplikace v režimu offline. Na portálu použijte možnost plánované převzetí služeb při selhání a vyberte nejnovější bod pro převzetí služeb při selhání. Místní virtuální počítač se automaticky vypíná.<br/><br/>Efektivní ztráta dat aplikace = 0 <br/><br/>K dispozici je deník bodů obnovení v okně uchovávání pro zákazníky, kteří chtějí používat starší bod obnovení. (72 hodin uchovávání pro VMware). Pokud se replikace zastavila mimo časový rámec okna pro uchovávání informací, zákazníci můžou dál převzít služby při selhání s využitím nejnovějších dostupných bodů obnovení.
|Převzetí služeb při selhání kvůli neplánovanému výpadku datového centra (přirozené nebo IT havárie) | Minimální ztráta dat pro aplikaci | 1. zahájení plánu BCP v organizaci <br/><br/>2. Inicializujte neplánované převzetí služeb při selhání pomocí portálu ASR na nejnovější nebo bod z okna pro uchovávání informací (deník).| 1. Inicializujte plán BCP organizace. <br/><br/>2. Inicializujte neplánované převzetí služeb při selhání pomocí portálu ASR na nejnovější nebo bod z okna pro uchovávání informací (deník).


## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání
Tento postup popisuje, jak spustit převzetí služeb při selhání pro [plán obnovení](site-recovery-create-recovery-plans.md). Případně můžete spustit převzetí služeb při selhání pro jeden virtuální počítač nebo fyzický server ze stránky **replikované položky** , jak je popsáno [zde](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure).


![Převzetí služeb při selhání](./media/site-recovery-failover/Failover.png)

1. Vyberte **plány obnovení** > *recoveryplan_name*. Klikněte na **převzetí služeb při selhání**
2. Na obrazovce **převzetí služeb při selhání** vyberte **bod obnovení** pro převzetí služeb při selhání. Můžete použít jednu z následujících možností:
   1. **Nejnovější**: Tato možnost spustí úlohu tím, že nejprve zpracovává všechna data, která byla odeslána do služby Site Recovery Service. Zpracování dat vytvoří bod obnovení pro každý virtuální počítač. Tento bod obnovení používá virtuální počítač během převzetí služeb při selhání. Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO), protože virtuální počítač vytvořený po převzetí služeb při selhání obsahuje všechna data, která byla při aktivaci převzetí služeb při selhání replikována do Site Recovery služby.
   1. **Poslední zpracování**: Tato možnost převezme všechny virtuální počítače z plánu obnovení do nejnovějšího bodu obnovení, který již byl zpracován službou Site Recovery služby. Při provádění testovacího převzetí služeb při selhání virtuálního počítače se zobrazí také časové razítko posledního zpracovaného bodu obnovení. Pokud provádíte převzetí služeb při selhání plánu obnovení, můžete získat tyto informace na samostatném virtuálním počítači a na dlaždici zobrazit **nejnovější body obnovení** . Vzhledem k tomu, že nestráví žádná doba zpracování nezpracovaných dat, tato možnost poskytuje možnost převzetí služeb při selhání s nízkou RTO (doba obnovení).
   1. **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost převezme všechny virtuální počítače z plánu obnovení do nejnovějšího bodu obnovení konzistentního vzhledem k aplikacím, který již byl zpracován službou Site Recovery. Při provádění testovacího převzetí služeb při selhání virtuálního počítače se zobrazí také časové razítko posledního bodu obnovení konzistentního vzhledem k aplikacím. Pokud provádíte převzetí služeb při selhání plánu obnovení, můžete získat tyto informace na samostatném virtuálním počítači a na dlaždici zobrazit **nejnovější body obnovení** .
   1. **Nejnovější zpracovaný vícenásobný virtuální počítač**: Tato možnost je dostupná jenom pro plány obnovení, které mají minimálně jeden virtuální počítač s konzistencí pro víc virtuálních počítačů. Virtuální počítače, které jsou součástí replikační skupiny, přecházejí na nejnovější běžný bod obnovení s více virtuálními počítači. Ostatní virtuální počítače převzetí služeb při selhání do svého nejnovějšího zpracovaného bodu obnovení.  
   1. **Nejnovější konzistentní vzhledem k aplikacím pro více virtuálních počítačů**: Tato možnost je dostupná jenom pro plány obnovení, které mají minimálně jeden virtuální počítač s konzistencí pro víc virtuálních počítačů. Virtuální počítače, které jsou součástí replikační skupiny, přecházejí na nejnovější běžný bod obnovení konzistentního vzhledem k aplikacím pro více virtuálních počítačů. Ostatní virtuální počítače převzetí služeb při selhání nejnovějším bodem obnovení konzistentním vzhledem k aplikacím.
   1. **Vlastní**: Pokud testujete převzetí služeb při selhání virtuálního počítače, můžete tuto možnost použít k převzetí služeb při selhání určitého bodu obnovení.

      > [!NOTE]
      > Možnost výběru bodu obnovení je dostupná jenom při selhání služby Azure.
      >
      >


1. Pokud u některých virtuálních počítačů v plánu obnovení došlo při převzetí služeb při selhání v předchozím běhu a virtuální počítače jsou aktivní na zdrojovém i cílovém umístění, můžete použít možnost **změnit směr** a určit směr, ve kterém by se mělo převzetí služeb při selhání probíhat.
1. Pokud pro Cloud přecházíte přes Azure a pro Cloud je povolené šifrování dat (platí jenom v případě, že jste nastavili ochranu virtuálních počítačů Hyper-v ze serveru VMM), vyberte v **šifrovacím klíči** certifikát, který byl vydán, když jste povolili šifrování dat během instalace na serveru VMM.
1. Před spuštěním **převzetí služeb při selhání vyberte možnost vypnout počítač** , pokud se chcete Site Recovery pokusit před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání pokračuje i v případě, že dojde k selhání vypnutí.  

    > [!NOTE]
    > Pokud jsou virtuální počítače Hyper-v chráněné, možnost vypnutí se taky pokusí synchronizovat místní data, která ještě nebyla před aktivací převzetí služeb při selhání odeslána službě.
    >
    >

1. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**. I když během neplánovaného převzetí služeb při selhání dojde k chybám, plán obnovení se spustí až do dokončení.
1. Po převzetí služeb při selhání ověřte virtuální počítač tím, že se k němu přihlásíte. Pokud chcete přepnout na jiný bod obnovení virtuálního počítače, můžete použít možnost **změnit bod obnovení** .
1. Jakmile budete spokojeni s virtuálním počítačem, u kterého došlo k převzetí služeb při selhání, můžete převzetí služeb při selhání **Potvrdit**. Příkaz **Commit odstraní všechny body obnovení dostupné pomocí služby** a možnost **změnit bod obnovení** již není k dispozici.

## <a name="planned-failover"></a>Plánované převzetí služeb při selhání
Virtuální počítače/fyzické servery chráněné pomocí Site Recovery také podporují **plánované převzetí služeb při selhání**. Plánovaná převzetí služeb při selhání je nulová možnost převzetí služeb při selhání. Při aktivaci plánovaného převzetí služeb při selhání se nejdřív odpojí zdrojové virtuální počítače, synchronizují se nejnovější data a pak se aktivuje převzetí služeb při selhání.

> [!NOTE]
> Během převzetí služeb při selhání virtuálních počítačů Hyper-v z jedné místní lokality do jiné místní lokality se vraťte na primární místní lokalitu, kterou musíte nejdřív **vrátit** zpět do primární lokality, a potom aktivovat převzetí služeb při selhání. Pokud primární virtuální počítač není k dispozici, pak před zahájením **replikace** musíte obnovit virtuální počítač ze zálohy.   
 
 
## <a name="failover-job"></a>Úloha převzetí služeb při selhání

![Převzetí služeb při selhání](./media/site-recovery-failover/FailoverJob.png)

Při aktivaci převzetí služeb při selhání zahrnuje následující kroky:

1. Ověření předpokladů: Tento krok zajišťuje splnění všech podmínek vyžadovaných pro převzetí služeb při selhání.
1. Převzetí služeb při selhání: Tento krok zpracovává data a zpřístupňuje je, aby bylo možné vytvořit virtuální počítač Azure. Pokud jste zvolili **nejnovější** bod obnovení, tento krok vytvoří bod obnovení z dat, která byla odeslána do služby.
1. Spustit: Tento krok vytvoří virtuální počítač Azure pomocí dat zpracovaných v předchozím kroku.

> [!WARNING]
> **Nerušit probíhající převzetí služeb při selhání**: před spuštěním převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud **zrušíte** probíhající úlohu, převzetí služeb při selhání se zastaví, ale virtuální počítač se nezačne replikovat. Replikaci nelze spustit znovu.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Doba potřebná pro převzetí služeb při selhání do Azure

V některých případech převzetí služeb virtuálních počítačů při selhání vyžaduje další přechodný krok, který dokončení obvykle trvá přibližně 8 až 10 minut. V následujících případech bude doba potřebná k převzetí služeb při selhání vyšší než obvykle:

* Virtuální počítače VMware, které používají službu mobility verze starší než 9,8
* Fyzické servery
* Virtuální počítače VMware Linux
* Virtuální počítače Hyper-V chráněné jako fyzické servery
* Virtuální počítače VMware, ve kterých nejsou k dispozici následující ovladače jako spouštěcí ovladače
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ovladač
* Virtuální počítače VMware, které nemají povolenou službu DHCP bez ohledu na to, zda používají protokol DHCP nebo statické IP adresy

Ve všech ostatních případech se tento přechodný krok nevyžaduje a doba trvání převzetí služeb při selhání je nižší.

## <a name="using-scripts-in-failover"></a>Používání skriptů v převzetí služeb při selhání
Při převzetí služeb při selhání můžete chtít automatizovat určité akce. K tomu můžete použít skripty nebo [Runbooky Azure Automation](site-recovery-runbook-automation.md) v [plánech obnovení](site-recovery-create-recovery-plans.md) .

## <a name="post-failover-considerations"></a>Předpoklady po převzetí služeb při selhání
Po převzetí služeb při selhání můžete chtít vzít v úvahu následující doporučení:
### <a name="retaining-drive-letter-after-failover"></a>Zachovávání písmen jednotek po převzetí služeb při selhání
Azure Site Recovery zpracovává uchovávání písmen jednotek. [Přečtěte si další](vmware-azure-exclude-disk.md#example-1-exclude-the-sql-server-tempdb-disk) informace o tom, jak se to provede, když se rozhodnete vyloučit některé disky.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Pokud se chcete připojit k virtuálním počítačům Azure pomocí protokolů RDP/SSH po převzetí služeb při selhání, musíte dodržet požadavky shrnuté v této [tabulce](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Při řešení problémů s připojením po převzetí služeb při selhání použijte [zde](site-recovery-failover-to-azure-troubleshoot.md) popsaný postup.


## <a name="next-steps"></a>Další kroky

> [!WARNING]
> Po převzetí služeb při selhání virtuálních počítačů a zpřístupnění místního datového centra byste měli virtuální počítače VMware znovu [**chránit**](vmware-azure-reprotect.md) zpátky do místního datového centra.

Možnost [**plánovaného převzetí služeb při selhání**](hyper-v-azure-failback.md) použijte k **navrácení služeb po obnovení** virtuálních počítačů Hyper-v zpátky do místního prostředí z Azure.

Pokud jste převzali služby při selhání virtuálního počítače Hyper-v na jiné místní datové centrum spravované serverem VMM a je dostupné primární datové centrum, spusťte replikaci zpátky do primárního datového centra a pak použijte možnost **Zpětná replikace** .
