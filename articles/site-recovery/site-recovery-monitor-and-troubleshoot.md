---
title: Azure Site Recovery monitorování | Microsoft Docs
description: Monitorování a řešení potíží s Azure Site Recoverymi problémy a operacemi replikace pomocí portálu
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: d441284b265ab11dd5ece42ec3737e455d662435
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545799"
---
# <a name="monitor-site-recovery"></a>Monitorování Site Recovery

V tomto článku se dozvíte, jak monitorovat [Site Recovery](site-recovery-overview.md)Azure pomocí Site Recovery sestaveného monitorování.  Můžete monitorovat:

- Stav počítačů replikovaných pomocí Site Recovery
- Zkontrolujte stav převzetí služeb při selhání u počítačů.
- Problémy a chyby ovlivňující konfiguraci a replikaci
- Komponenty infrastruktury, jako jsou například místní servery.


## <a name="before-you-start"></a>Než začnete

Než začnete, můžete si projít [běžné otázky týkající se monitorování](monitoring-common-questions.md) .

## <a name="monitor-in-the-dashboard"></a>Monitorování na řídicím panelu

1. V trezoru klikněte na **Přehled** . Řídicí panel Recovery Services slučuje všechny informace o monitorování pro trezor v jednom umístění. Existují stránky pro Site Recovery i Azure Backup službu a můžete mezi nimi přepínat.

    ![Řídicí panel Site Recovery](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Z řídicího panelu přejděte k podrobnostem do různých oblastí. 

    ![Snímek obrazovky zobrazující oblasti na řídicím panelu, kde můžete procházet k podrobnostem.](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. V části **replikované položky** klikněte na **Zobrazit vše** , aby se zobrazily všechny servery v trezoru.
4. Kliknutím na podrobnosti o stavu v jednotlivých částech přejdete k podrobnostem.
5. V **zobrazení infrastruktura** můžete seřadit informace o monitorování podle typu počítačů, které chcete replikovat.

## <a name="monitor-replicated-items"></a>Monitorovat replikované položky

V části **replikované položky** Monitorujte stav všech počítačů v trezoru, které mají povolenou replikaci.

**Státech** | **Podrobnosti**
--- | ---
V pořádku | Replikace probíhá normálně. Nezjistily se žádné chybové příznaky ani upozornění.
Upozornění | Zjistil se jeden nebo víc příznaků upozornění, které by mohly mít vliv na replikaci.
Kritické | Zjistil se minimálně jeden příznak kritické chyby replikace.<br/><br/> Tyto chybové příznaky jsou obvykle indikátory, které se zablokují replikací, nebo nepostupují stejně rychle jako rychlost změny dat.
Nelze použít | Servery, které se v současné době neočekávají pro replikaci. To může zahrnovat počítače, u kterých došlo k převzetí služeb při selhání.

## <a name="monitor-test-failovers"></a>Monitorování testovacího převzetí služeb při selhání

V případě **úspěchu testu převzetí služeb při selhání** Sledujte stav převzetí služeb při selhání u počítačů v trezoru.

- Doporučujeme spustit testovací převzetí služeb při selhání na replikovaných počítačích nejméně jednou za šest měsíců. Je to způsob, jak ověřit, že převzetí služeb při selhání funguje podle očekávání, aniž by došlo k přerušení produkčního prostředí. 
- Testovací převzetí služeb při selhání se považuje za úspěšné až po úspěšném dokončení a vyčištění po převzetí služeb při selhání.

**Státech** | **Podrobnosti**
--- | ---
Doporučuje se test | Počítače, u kterých došlo k testovacímu převzetí služeb při selhání, protože byla povolena ochrana.
Úspěšně provedeno | Počítače s nebo více úspěšnými testovacími převzetím služeb při selhání.
Nelze použít | Počítače, které aktuálně nejsou vhodné pro testovací převzetí služeb při selhání. Například počítače, u kterých došlo k převzetí služeb při selhání, mají počáteční replikaci, převzetí služeb při selhání nebo převzetí služeb při selhání.

## <a name="monitor-configuration-issues"></a>Sledování problémů s konfigurací

V případě **problémů s konfigurací** monitorujte všechny problémy, které by mohly mít vliv na úspěšnou převzetí služeb při selhání.

- Problémy s konfigurací (s výjimkou dostupnosti aktualizace softwaru) se zjišťují pomocí pravidelné operace ověřování, která se ve výchozím nastavení spouští každých 12 hodin. Můžete vynutit, aby operace ověřování běžela okamžitě kliknutím na ikonu aktualizace vedle záhlaví oddílu **problémy s konfigurací** .
- Kliknutím na odkazy zobrazíte další podrobnosti. U problémů, které mají vliv na konkrétní počítače, klikněte ve sloupci **cílové konfigurace** na možnost **vyžaduje pozornost** . Podrobnosti zahrnují doporučení pro nápravu.

**Státech** | **Podrobnosti**
--- | ---
Chybějící konfigurace | Chybí nezbytné nastavení, například síť pro obnovení nebo skupina prostředků.
Chybějící prostředky | Zadaný prostředek se nepovedlo najít nebo není v předplatném dostupný. Prostředek se například odstranil nebo migrovali. Monitorované prostředky zahrnovaly cílovou skupinu prostředků, cílovou virtuální síť, podsíť, protokol/cílový účet úložiště, cílovou skupinu dostupnosti, cílovou IP adresu.
Kvóta předplatného |  Dostupný zůstatek kvóty prostředků předplatného se porovnává s zůstatkem, který je potřeba pro převzetí služeb při selhání u všech počítačů v trezoru.<br/><br/> Pokud není dostatek prostředků, nahlásí se nedostatečný zůstatek kvóty.<br/><br/> Kvóty monitorují počet jader virtuálních počítačů, počet jader virtuálních počítačů a počet síťových rozhraní (NIC).
Aktualizace softwaru | Dostupnost nových aktualizací softwaru a informace o vypršení platnosti verzí softwaru.


## <a name="monitor-errors"></a>Sledování chyb

V **souhrnu chyb** monitorujte aktuálně aktivní příznaky chyb, které by mohly mít vliv na replikaci serverů v trezoru, a monitoruje počet ovlivněných počítačů.

- Zobrazí se chyby, které mají vliv na místní součásti infrastruktury, na začátek oddílu. Například nepříjem prezenčního signálu od poskytovatele Azure Site Recovery na místním konfiguračním serveru nebo hostiteli Hyper-V.
- V dalším kroku se zobrazí chybové příznaky při replikaci, které by ovlivnily replikované servery.
- Položky v tabulce jsou seřazené podle klesajícího pořadí závažnosti chyby a pak podle zkrácení pořadí podle počtu ovlivněných počítačů.
- Počet ovlivněných serverů je užitečný způsob, jak pochopit, zda jeden ze základních problémů může ovlivnit více počítačů. Například síťový porucha může potenciálně ovlivnit všechny počítače, které se replikují do Azure. 
- Na jednom serveru může dojít k několika chybám replikace. V takovém případě každý chybový příznak počítá tento server v seznamu ovlivněných serverů. Po vyřešení problému se vylepšit parametry replikace a z počítače se vymaže chyba.

## <a name="monitor-the-infrastructure"></a>Monitorujte infrastrukturu.

V **zobrazení infrastruktura** Sledujte komponenty infrastruktury zapojené do replikace a stav připojení mezi servery a službami Azure.

- Zelená čára indikuje, že je připojení v pořádku.
- Červená čára s chybou překrytí označuje existenci jednoho nebo více chybových symptomů, které mají vliv na připojení.
-  Pokud chcete zobrazit chybu a počet ovlivněných entit, najeďte ukazatelem myši na ikonu chyby. Klikněte na ikonu filtrovaného seznamu ovlivněných entit.

    ![Zobrazení infrastruktury Site Recovery (trezor)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Tipy pro monitorování infrastruktury

- Ujistěte se, že místní součásti infrastruktury (konfigurační server, procesové servery, servery VMM, hostitelé Hyper-V, počítače VMware) používají nejnovější verze poskytovatele Site Recovery a/nebo agentů.
- Pokud chcete používat všechny funkce v zobrazení infrastruktury, měli byste pro tyto součásti používat [kumulativní aktualizaci 22](https://support.microsoft.com/help/4072852) .
- Chcete-li použít zobrazení infrastruktury, vyberte příslušný scénář replikace ve vašem prostředí. Další podrobnosti najdete v zobrazení podrobností. V následující tabulce jsou uvedeny scénáře, které jsou zastoupeny.

    **Scénář** | **Státech**  | **Zobrazit dostupné?**
    --- |--- | ---
    **Replikace mezi místními lokalitami** | Všechny státy | Ne 
    **Replikace virtuálních počítačů Azure mezi oblastmi Azure**  | Replikace je povolená/počáteční replikace probíhá. | Ano
    **Replikace virtuálních počítačů Azure mezi oblastmi Azure** | Převzetí služeb při selhání/navrácení služeb po obnovení | Ne   
    **Replikace VMware do Azure** | Replikace je povolená/počáteční replikace probíhá. | Ano     
    **Replikace VMware do Azure** | Převzetí služeb při selhání/navrácení služeb po obnovení | Ne      
    **Replikace Hyper-V do Azure** | Převzetí služeb při selhání/navrácení služeb po obnovení | Ne

- Pokud chcete zobrazit zobrazení infrastruktury pro jeden počítač pro replikaci, v nabídce trezoru klikněte na **replikované položky** a vyberte server.  




## <a name="monitor-recovery-plans"></a>Monitorování plánů obnovení

V části **plány obnovení** sledujte počet plánů, vytvářejte nové plány a upravujte stávající.  

## <a name="monitor-jobs"></a>Monitorování úloh

V části **úlohy** Sledujte stav operací Site Recovery.

- Většina operací v Azure Site Recovery se provádí asynchronně, přičemž probíhá vytváření úlohy sledování a používá se ke sledování průběhu operace. 
- Objekt úlohy obsahuje všechny informace, které potřebujete ke sledování stavu a průběh operace. 

Sledujte úlohy následujícím způsobem:

1. V části **úlohy** >ch řídicích panelů uvidíte souhrn dokončených úloh, probíhajících operací nebo čekání na vstup za posledních 24 hodin. Můžete kliknout na libovolný stav a získat další informace o relevantních úlohách.
2. Kliknutím na **Zobrazit vše** zobrazíte všechny úlohy za posledních 24 hodin.

    > [!NOTE]
    > K informacím o úloze můžete získat přístup také v nabídce trezoru > **úlohy Site Recovery** . 

2. V seznamu **úloh Site Recovery** se zobrazí seznam úloh. V horní nabídce můžete získat podrobnosti o chybě pro konkrétní úlohy, filtrovat seznam úloh na základě určitých kritérií a exportovat podrobnosti vybrané úlohy do Excelu.
3. Kliknutím můžete přejít na úlohu. 

## <a name="monitor-virtual-machines"></a>Monitorování virtuálních počítačů

V části **replikované položky** Získejte seznam replikovaných počítačů. 
    ![Zobrazení seznamu replikovaných položek Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Můžete zobrazit a filtrovat informace. V nabídce Akce v horní části můžete provádět akce pro konkrétní počítač, včetně spuštění testovacího převzetí služeb při selhání nebo zobrazení specifických chyb.
3. Kliknutím na **sloupce** zobrazíte další sloupce, například chcete-li zobrazit RPO, problémy s konfigurací cíle a chyby replikace.
4. Kliknutím na **Filtr** zobrazíte informace na základě konkrétních parametrů, jako je třeba stav replikace nebo konkrétní zásada replikace.
5. Klikněte pravým tlačítkem na počítač a inicializujte operace, jako je třeba testovací převzetí služeb při selhání, nebo zobrazte konkrétní podrobnosti o chybách, které jsou k němu přidružené.
6. Kliknutím na počítač můžete přejít k podrobnostem. Podrobnosti zahrnují:
   - **Informace o replikaci** : aktuální stav a stav počítače.
   - **RPO** (cíl bodu obnovení): aktuální RPO pro virtuální počítač a čas, kdy byl naposledy vypočítán cíl RPO.
   - **Body obnovení** : nejnovější dostupné body obnovení pro daný počítač.
   - **Připravenost na převzetí služeb při selhání** : označuje, jestli se pro daný počítač spustilo testovací převzetí služeb při selhání, verze agenta běžící v počítači (pro počítače se službou mobility) a všechny problémy s konfigurací.
   - **Chyby** : seznam příznaků chyb replikace aktuálně zjištěných v počítači a možné příčiny nebo akce.
   - **Události** : chronologický seznam nedávných událostí, které mají vliv na daný počítač. Podrobnosti o chybě zobrazují aktuálně pozorovatelící chybové příznaky, zatímco události jsou historické záznamy o problémech, které mají vliv na daný počítač.
   - **Zobrazení infrastruktury** : zobrazuje stav infrastruktury pro scénář při replikaci počítačů do Azure.

     ![Podrobnosti/přehled replikované položky Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Přihlášení k odběru e-mailových oznámení

Můžete se přihlásit k odběru e-mailových oznámení pro tyto kritické události:
 
- Kritický stav pro replikovaný počítač.
- Mezi místními součástmi infrastruktury a Site Recovery službou nejsou žádné připojení. Připojení mezi Site Recovery a místními servery zaregistrovanými v trezoru se detekuje pomocí mechanismu prezenčního signálu.
- Selhání převzetí služeb při selhání.

Přihlaste se k odběru následujícím způsobem:

V části Trezor > **monitoring** klikněte na **Site Recovery události** .
1. Klikněte na **E-mailová oznámení** .
1. V **e-mailových oznámeních** zapněte oznámení a určete, komu se má odeslat. Můžete odeslat oznámení všem správcům předplatného a volitelně konkrétní e-mailové adresy.

    ![E-mailová oznámení](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Další kroky

[Přečtěte si o](monitor-log-analytics.md) Site Recovery monitorování pomocí Azure monitor.
