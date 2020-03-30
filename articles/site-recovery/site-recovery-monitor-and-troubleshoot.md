---
title: Monitorování obnovení webu Azure | Dokumenty společnosti Microsoft
description: Monitorování a řešení problémů s replikací obnovení webu Azure pomocí portálu
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: aa9d776df50306ab1705426c923413b5a5d545a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68717355"
---
# <a name="monitor-site-recovery"></a>Monitorování Site Recovery

V tomto článku se dozvíte, jak sledovat [Azure Site Recovery](site-recovery-overview.md)pomocí site recovery integrované monitorování.  Můžete sledovat:

- Stav a stav počítačů replikovaných službou Site Recovery
- Otestujte stav převzetí služeb při selhání počítačů.
- Problémy a chyby ovlivňující konfiguraci a replikaci.
- Součásti infrastruktury, jako jsou místní servery.


## <a name="before-you-start"></a>Než začnete

Možná budete chtít zkontrolovat [běžné otázky monitorování,](monitoring-common-questions.md) než začnete.

## <a name="monitor-in-the-dashboard"></a>Monitor na řídicím panelu

1. V úschovně klikněte na **Přehled**. Řídicí panel Služby obnovení konsoliduje všechny informace o monitorování trezoru na jednom místě. Existují stránky pro web recovery a službu Azure Backup a můžete mezi nimi přepínat.

    ![Řídicí panel obnovení webu](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Z řídicího panelu přejděte k podrobnostem do různých oblastí. 

    ![Řídicí panel obnovení webu](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. V **části Replikované položky**klepnutím na **tlačítko Zobrazit vše** zobrazíte všechny servery v úschovně.
4. Kliknutím na podrobnosti o stavu v jednotlivých oddílech přejděte k podrobnostem.
5. V **zobrazení infrastruktury**seřaďte informace o monitorování podle typu počítačů, které replikujete.

## <a name="monitor-replicated-items"></a>Sledování replikovaných položek

V **části Replikované položky**sledujte stav všech počítačů v úschovně, u kterých je povolena replikace.

**Stav** | **Podrobnosti**
--- | ---
V pořádku | Replikace probíhá normálně. Nebyly zjištěny žádné chyby nebo varovné příznaky.
Upozornění | Jsou zjištěny jeden nebo více varovných příznaků, které mohou ovlivnit replikaci.
Kritická | Byly zjištěny jeden nebo více kritických příznaků chybreplikace.<br/><br/> Tyto příznaky chyby jsou obvykle ukazatele, které replikace přilepená nebo nepostupuje tak rychle, jak rychlost změny dat.
Neuvedeno | Servery, u kterých se aktuálně neočekává replikace. To může zahrnovat počítače, které byly převzetí míse.

## <a name="monitor-test-failovers"></a>Monitorování převzetí služeb při selhání testu

V **úspěšném testu převzetí služeb při selhání**sledujte stav převzetí služeb při selhání pro počítače v trezoru.

- Doporučujeme spustit zkušební převzetí služeb při selhání na replikovaných počítačích alespoň jednou za šest měsíců. Je to způsob, jak zkontrolovat, že převzetí služeb při selhání funguje podle očekávání, aniž by došlo k narušení produkčního prostředí. 
- Zkušební převzetí služeb při selhání je považováno za úspěšné až po úspěšném dokončení vyčištění převzetí montovny a po převzetí služeb při selhání.

**Stav** | **Podrobnosti**
--- | ---
Test doporučen | Počítače, které neměly zkušební převzetí služeb při selhání od doby, kdy byla povolena ochrana.
Úspěšně provedeno | Počítače s nebo úspěšnější zkušební převzetí služeb při selhání.
Neuvedeno | Počítače, které nejsou aktuálně způsobilé pro převzetí služeb při selhání testu. Například počítače, které jsou převzetí služeb při selhání, mají počáteční replikace/test převzetí služeb při selhání/převzetí služeb při selhání probíhá.

## <a name="monitor-configuration-issues"></a>Sledování problémů s konfigurací

V **problémech s konfigurací**sledujte všechny problémy, které by mohly mít vliv na možnost úspěšného převzetí služeb při selhání.

- Problémy s konfigurací (s výjimkou dostupnosti aktualizace softwaru) jsou zjištěny periodickou operací validátoru, která je ve výchozím nastavení spuštěna každých 12 hodin. Operaci validátoru můžete vynutit okamžité spuštění klepnutím na ikonu aktualizace vedle záhlaví oddílu **Problémy s konfigurací.**
- Kliknutím na odkazy zobrazíte další podrobnosti. V případě problémů, které mají vliv na konkrétní počítače, klikněte na **tlačítko vyžaduje pozornost** ve **sloupci Cílové konfigurace.** Podrobnosti zahrnují doporučení pro nápravu.

**Stav** | **Podrobnosti**
--- | ---
Chybějící konfigurace | Chybí potřebné nastavení, například síť pro obnovení nebo skupina prostředků.
Chybějící zdroje | Zadaný prostředek nebyl nalezen nebo není k dispozici v předplatném. Například prostředek byl odstraněn nebo migrován. Mezi monitorované prostředky patří cílová skupina prostředků, cílová virtuální síť/podsíť, účet úložiště protokolu/cíle, cílová sada dostupnosti, cílová IP adresa.
Kvóta předplatného |  Zůstatek kvóty prostředků předplatného k dispozici je porovnán s zůstatek potřebný k převzetí služeb při selhání všech počítačů v trezoru.<br/><br/> Pokud není dostatek prostředků, je hlášen a nedostatečnou kvótu.<br/><br/> Kvóty jsou monitorování počtu jádra virtuálního zařízení, počet jádra rodiny virtuálních zařízení, počet karet síťového rozhraní (NIC).
Aktualizace softwaru | Dostupnost nových aktualizací softwaru a informace o vypršení platnosti verzí softwaru.


## <a name="monitor-errors"></a>Sledování chyb

V **souhrnu chyb**monitorování aktuálně aktivních chybových příznaků, které mohou ovlivnit replikaci serverů v úložišti, a sledujte počet ovlivněných počítačů.

- Chyby ovlivňující součásti místní infrastruktury jsou zobrazeny na začátku oddílu. Například nepřijetí prezenčního signálu od zprostředkovatele obnovení webu Azure na místním konfiguračním serveru nebo hostiteli Hyper-V.
- Dále jsou zobrazeny příznaky chyby replikace ovlivňující replikované servery.
- Položky tabulky jsou seřazeny podle klesajícího pořadí závažnosti chyby a potom podle snížení pořadí počtu ovlivněných strojů.
- Ovlivněný počet serverů je užitečný způsob, jak pochopit, zda jeden základní problém může mít vliv na více počítačů. Například chyba sítě může potenciálně ovlivnit všechny počítače, které se replikují do Azure. 
- Na jednom serveru může dojít k chybám více replikací. V tomto případě každý příznak chyby počítá tento server v seznamu jeho ovlivněných serverů. Po odstranění problému se parametry replikace zlepší a chyba se z počítače vymaže.

## <a name="monitor-the-infrastructure"></a>Sledujte infrastrukturu.

V **zobrazení infrastruktury**sledujte součásti infrastruktury, které se účastní replikace, a stav připojení mezi servery a službami Azure.

- Zelená čára označuje, že připojení je v pořádku.
- Červená čára s ikonou překryté chyby označuje existenci jednoho nebo více příznaků chyby, které ovlivňují připojení.
-  Najeďte myší myší na ikonu chyby, abyste zobrazili chybu a počet ovlivněných entit. Klikněte na ikonu pro filtrovaný seznam ovlivněných entit.

    ![Zobrazení infrastruktury obnovení lokality (trezor)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Tipy pro sledování infrastruktury

- Ujistěte se, že součásti místní infrastruktury (konfigurační server, procesní servery, servery VMM, hostitelé Technologie Hyper-V, počítače VMware) používají nejnovější verze zprostředkovatele obnovení webu nebo agentů.
- Chcete-li použít všechny funkce v zobrazení infrastruktury, měli byste pro tyto součásti spustit [kumulativní aktualizaci 22.](https://support.microsoft.com/help/4072852)
- Chcete-li použít zobrazení infrastruktury, vyberte příslušný scénář replikace ve vašem prostředí. Další podrobnosti můžete přejít k podrobnostem v zobrazení. V následující tabulce jsou uvedeny scénáře, které jsou reprezentovány.

    **Scénář** | **Stav**  | **Zobrazit k dispozici?**
    --- |--- | ---
    **Replikace mezi místními lokalitami** | Všechny státy | Ne 
    **Replikace virtuálních počítačů Azure mezi oblastmi Azure**  | Probíhá replikace nebo počáteční replikace | Ano
    **Replikace virtuálních počítačů Azure mezi oblastmi Azure** | Převzetí služeb při selhání/navrácení služeb při selhání | Ne   
    **Replikace VMware do Azure** | Probíhá replikace nebo počáteční replikace | Ano     
    **Replikace VMware do Azure** | Převzetí selhání nebo vrácení zpět | Ne      
    **Replikace Hyper-V do Azure** | Převzetí selhání nebo vrácení zpět | Ne

- Chcete-li zobrazit zobrazení infrastruktury pro jeden replikující počítač, klepněte v nabídce úschovny na **položku Replikované položky**a vyberte server.  




## <a name="monitor-recovery-plans"></a>Sledování plánů obnovení

V **plánech obnovení**sledujte počet plánů, vytvořte nové plány a upravte stávající plány.  

## <a name="monitor-jobs"></a>Monitorování úloh

V **úlohách**sledujte stav operací obnovení webu.

- Většina operací v Azure Site Recovery se provádí asynchronně, s úlohou sledování, která se vytváří a používá ke sledování průběhu operace. 
- Objekt úlohy má všechny informace, které potřebujete ke sledování stavu a průběhu operace. 

Sledování úloh následujícím způsobem:

1. V části Řídicí panel > **úlohy** můžete zobrazit souhrn úloh, které byly dokončeny, probíhají nebo čekají na vstup za posledních 24 hodin. Kliknutím na libovolný stát získáte další informace o příslušných úlohách.
2. Kliknutím na **Zobrazit vše** zobrazíte všechny úlohy za posledních 24 hodin.

    > [!NOTE]
    > Můžete také získat přístup k informacím o úlovcích z nabídky úschovny > **úlohy obnovení webu**. 

2. V seznamu **Úlohy obnovení webu** se zobrazí seznam úloh. V horní nabídce můžete získat podrobnosti o chybě pro konkrétní úlohy, filtrovat seznam úloh na základě konkrétních kritérií a exportovat vybrané podrobnosti o projektu do aplikace Excel.
3. Kliknutím na něj můžete přejít k úloze. 

## <a name="monitor-virtual-machines"></a>Monitorování virtuálních počítačů

V **položkách Replikované**získáte seznam replikovaných počítačů. 
    ![Zobrazení seznamu replikovaných položek pro obnovení sítě](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Můžete zobrazit a filtrovat informace. V nabídce akce v horní části můžete provádět akce pro konkrétní počítač, včetně spuštění zkušebního převzetí služeb při selhání nebo zobrazení konkrétních chyb.
3. Kliknutím na **Sloupce** zobrazíte další sloupce, například zobrazíte rpo, problémy s konfigurací cíle a chyby replikace.
4. Klepnutím na **tlačítko Filtr** zobrazíte informace založené na konkrétních parametrech, jako je stav replikace nebo konkrétní zásady replikace.
5. Klepněte pravým tlačítkem myši na počítač a můžete zahájit operace, jako je například testování převzetí služeb při selhání, nebo zobrazit konkrétní podrobnosti o chybě, které jsou k němu přidruženy.
6. Klikněte na stroj a pro přejděte k dalším podrobnostem. Podrobnosti zahrnují:
   - **Informace o replikaci**: Aktuální stav a stav počítače.
   - **RPO** (cíl bodu obnovení): Aktuální rpo pro virtuální počítač a čas, kdy byl naposledy vypočítán RPO.
   - **Body obnovení**: Nejnovější dostupné body obnovení pro stroj.
   - **Připravenost převzetí služeb při selhání**: Označuje, zda bylo pro počítač spuštěno zkušební převzetí služeb při selhání, verze agenta spuštěná v počítači (pro počítače se službou Mobility) a všechny problémy s konfigurací.
   - **Chyby**: Seznam příznaků chyb replikace aktuálně pozorovaných v počítači a možných příčin/akcí.
   - **Události**: Chronologický seznam nedávných událostí ovlivňujících stroj. Podrobnosti o chybě zobrazuje aktuálně pozorovatelné příznaky chyby, zatímco události jsou historickým záznamem problémů, které ovlivnily počítač.
   - **Zobrazení infrastruktury**: Zobrazuje stav infrastruktury pro scénář, kdy se počítače replikují do Azure.

     ![Podrobnosti/přehled replikovaných položek obnovení lokality](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Přihlásit se k odběru e-mailových oznámení

Můžete se přihlásit k odběru e-mailových oznámení pro tyto kritické události:
 
- Kritický stav pro replikovaný počítač.
- Žádné připojení mezi součástmi místní infrastruktury a službou Site Recovery. Připojení mezi site recovery a místními servery registrovanými v trezoru je detekováno pomocí mechanismu prezenčního signálu.
- Selhání převzetí služeb při selhání.

Přihlásit se takto:

V **části** sledování > úschovny klikněte na Události **obnovení webu**.
1. Klikněte na **E-mailová oznámení**.
1. V **e-mailovém oznámení**zapněte oznámení a určete, komu se má odesílat. Můžete odeslat všem správcům předplatného, aby vám byla odeslána oznámení a volitelně konkrétní e-mailové adresy.

    ![E-mailová oznámení](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Další kroky

[Přečtěte si o](monitor-log-analytics.md) monitorování site recovery pomocí Azure Monitoru.
