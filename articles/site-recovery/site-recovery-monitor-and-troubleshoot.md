---
title: Monitorování Azure Site Recovery | Dokumentace Microsoftu
description: Monitorování a řešení problémů replikace Azure Site Recovery a operací pomocí portálu
author: bsiva
manager: abhemra
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 07/19/2018
ms.author: bsiva
ms.openlocfilehash: dc089e29889b12a5a6d3fcb17328cfc13fe8d0c9
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211804"
---
# <a name="monitor-and-troubleshoot-site-recovery"></a>Monitorování a řešení Site Recovery

V tomto článku se dozvíte, jak používat Azure Site Recovery v sestavených funkce monitorování pro monitorování a řešení potíží. 

## <a name="use-the-dashboard"></a>Použití řídicího panelu

1. V trezoru, klikněte na tlačítko **přehled** otevřete řídicí panel Site Recovery. Existují stránky řídicího panelu pro Site Recovery a Backup, a můžete mezi nimi přepínat.

    ![Řídicí panel Site Recovery](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2.  Řídicí panel slučuje všechny informace o monitorování pro úložiště na jednom místě. Z řídicího panelu můžete procházet hierarchii do různých oblastí. 

    ![Řídicí panel Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. Na **replikované položky**, klikněte na tlačítko **zobrazení všech** zobrazíte všechny servery v trezoru.
4. K podrobnostem klikněte na podrobnosti o stavu v každé části. V **zobrazení infrastruktury**, informacím o monitorování můžete seřadit podle typu počítače, které replikujete.

## <a name="monitor-replicated-items"></a>Sledovat replikované položky

V části replikované položky zobrazuje stav všech počítačů, které mají replikace povolená v trezoru.

**Stav** | **Podrobnosti**
--- | ---
V pořádku | Replikace probíhá normálně. Byly zjištěny žádné chyby nebo upozornění příznaky.
Upozornění | Zjišťují se jeden nebo více upozornění příznaky, které může mít vliv na replikaci.
Kritická | Jeden nebo více příznaků chyba důležité replikace byly zjištěny.<br/><br/> Příznaky tyto chyby jsou obvykle indikátory, že je replikace zablokuje nebo nepostupuje tak rychle, jak frekvence změny dat.
Neuvedeno | Servery, které nejsou aktuálně očekává se replikovat. To může obsahovat počítače, které mají selhání.

## <a name="monitor-test-failovers"></a>Monitor testu převzetí služeb při selhání

Zobrazí se stav převzetí služeb při selhání testu pro počítače v trezoru.

- Doporučujeme spustit testovací převzetí služeb na replikované počítače alespoň jednou za šest měsíců. Je způsob, jak zkontrolovat, že převzetí služeb při selhání funguje podle očekávání pravidla bez narušení běžného vaše produkční prostředí. 
- Testovací převzetí služeb se považuje za úspěšná, až po převzetí služeb při selhání a post-převzetí služeb při selhání vyčištění byly úspěšně dokončeny.

**Stav** | **Podrobnosti**
--- | ---
Doporučuje se test | Počítače, u kterých nedošlo testovací převzetí služeb při selhání, protože byla povolena ochrana.
Proběhlo úspěšně | Počítače s nebo více úspěšné testovací převzetí služeb při selhání.
Neuvedeno | Počítače, které nejsou momentálně nárok testovací převzetí služeb při selhání. Počítače, které při selhání, třeba mít počáteční replikaci a testovací převzetí služeb při selhání/převzetí probíhá.

## <a name="monitor-configuration-issues"></a>Problémy s konfigurací monitorování

**Problémy s konfigurací** část obsahuje seznam problémů, které může mít vliv na schopnost úspěšně převzetí služeb při selhání.

- Problémy s konfigurací (s výjimkou dostupnost aktualizace softwaru), se zjišťují operací pravidelné ověřování, který ve výchozím nastavení spustí každých 12 hodin. Můžete vynutit operaci program pro ověření spustit okamžitě kliknutím na ikonu aktualizace vedle možnosti **problémy s konfigurací** nadpis oddílu.
- Kliknutím na odkazy zobrazíte další podrobnosti. Problémy s vliv na konkrétní počítače, klikněte na tlačítko **vyžaduje pozornost** v **cílové konfigurace** sloupce. Podrobnosti zahrnují doporučeními k nápravě.

**Stav** | **Podrobnosti**
--- | ---
Chybí konfigurace | Chybí nezbytná nastavení, jako je například síť pro obnovení nebo skupinu prostředků.
Chybějící prostředky | Zadaný prostředek nebyl nalezen nebo není k dispozici v rámci předplatného. Například prostředek se odstranil nebo migroval. Monitorovaných prostředků zahrnuté cílovou skupinu prostředků, cílové virtuální sítě nebo podsítě, účtu úložiště protokolu nebo cíle, cílová skupina dostupnosti, cílová IP adresa.
Kvóta předplatného |  Zůstatek kvóty prostředků k dispozici předplatné se porovná zůstatek potřebné k převzetí služeb při selhání všech počítačů v trezoru.<br/><br/> Pokud nejsou k dispozici dostatek prostředků, se použije v hlášení zůstatku Nedostatečná kvóta.<br/><br/> Kvóty monitorování pro počet jader virtuálního počítače, počet virtuálních počítačů řady jader, počet síťových rozhraní (NIC) karty.
Aktualizace softwaru | Dostupnost nové aktualizace softwaru a informace o u nichž vyprší platnost verze softwaru.


## <a name="monitoring-errors"></a>Sledování chyb 
**Souhrn chyb** část ukazuje příznaky aktuálně aktivní chyby, které může mít vliv na replikaci serverů do trezoru a počet ovlivněných počítačů.

- Na začátku části jsou uvedeny chyby, vliv na místní infrastrukturu komponenty. Například bez příjem prezenčního signálu od zprostředkovatele Azure Site Recovery na místní konfigurační server, VMM server nebo hostitele Hyper-V.
- Dále replikace chyba příznaky ovlivňuje replikované servery jsou uvedeny.
- Položky tabulky jsou seřazeny v sestupném pořadí podle závažnosti chyby a poté v sestupném pořadí počet ovlivněných počítačů.
- Počet ovlivněných serveru je užitečný způsob, jak pochopit, jestli jeden základní problém může mít vliv na několik počítačů. Například poruchu sítě může případně ovlivnit všechny počítače, které se replikují do Azure. 
- Na jednom serveru, může dojít k více chybám replikace. V tomto případě každý příznakem chyba počítá tento server v seznamu svých ovlivněné serverů. Jakmile je problém vyřešen, zlepšit parametry replikace a je chyba odstraněna z počítače.

## <a name="monitor-the-infrastructure"></a>Monitorování infrastruktury.

**Zobrazení infrastruktury** zobrazuje součásti infrastruktury používané v replikaci a stavu připojení mezi servery a služby Azure.

- Zelená čára označuje, že připojení je v pořádku.
- Červená čára s ikonou verlaid chyba značící existenci jeden nebo více chyb Příznaky tohoto neovlivní připojení.
-  Umístěte ukazatel myši nad ikonu chybě a zobrazí se chyba a počet ovlivněných entit. Klikněte na ikonu pro filtrovaný seznam ovlivněných entity.

    ![Zobrazení infrastruktury Site Recovery (trezor)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

## <a name="tips-for-monitoring-the-infrastructure"></a>Tipy pro monitorování infrastruktury

- Ujistěte se, že místní komponenty infrastruktury (konfigurační server, procesových serverů, servery VMM, hostitele Hyper-V, počítačů VMware) běží nejnovější verze zprostředkovatele služby Site Recovery a/nebo agenty.
- V zobrazení infrastruktury používat všechny funkce, by měl běžet [kumulativní 22](https://support.microsoft.com/help/4072852) pro tyto součásti.
- Pokud chcete použít zobrazení infrastruktury, vyberte scénář odpovídající replikace ve vašem prostředí. V zobrazení pro další podrobnosti můžete procházet hierarchii. Následující tabulka uvádí, jaké scénáře představují.

    **Scénář** | **Stav**  | **Zobrazit dostupné?**
    --- |--- | ---
    **Replikace mezi místními lokalitami** | Všechny stavy | Ne 
    **Replikace virtuálních počítačů Azure mezi oblastmi Azure**  | Probíhá replikace povolena/počáteční replikace | Ano
    **Replikace virtuálních počítačů Azure mezi oblastmi Azure** | Převzetí služeb při selhání / navrácení služeb po obnovení | Ne   
    **Replikace VMware do Azure** | Probíhá replikace povolena/počáteční replikace | Ano     
    **Replikace VMware do Azure** | Nepovedlo se nezdařilo nebo přes zadní | Ne      
    **Replikace Hyper-V do Azure** | Nepovedlo se nezdařilo nebo přes zadní | Ne

- Zobrazení infrastruktury pro jeden replikujícím počítači, v nabídce trezoru klikněte na tlačítko **replikované položky**a vyberte server.  

### <a name="common-questions"></a>Časté dotazy


**Proč se zobrazí počet virtuálních počítačů v zobrazení infrastruktury trezoru se liší od celkového počtu v replikované položky?**

Zobrazení infrastruktury úložiště je určeno scénáře replikace. Pouze počítače v aktuálně vybraných replikace scénáři jsou součástí počet zobrazení. Počítáme jenom virtuální počítače, které jsou nakonfigurované pro replikaci do Azure. Došlo k převzetí služeb počítače nebo počítače replikovat zpět do místní lokality se nepočítají v zobrazení.

**Proč je počet replikovaných položek zobrazených v zásuvce Essentials liší od celkový počet replikovaných položek na řídicím panelu?**

Pouze počítače, pro které počáteční replikace dokončí, které jsou zahrnuty v počet uvedené v zásuvce Essentials. Na replikované položky. obsahuje všechny počítače v trezoru, včetně těch, které počáteční replikace probíhá.


## <a name="monitor-recovery-plans"></a>Plány obnovení monitorování

V **plány obnovení části** můžete zkontrolovat počet plánů, vytvořit nové plány a upravovat stávající.  

## <a name="monitor-jobs"></a>Monitorování úloh

**Úlohy** části odráží stav operace Site Recovery.

- Většinu operací ve službě Azure Site Recovery se provedl asynchronně, úlohou sledování se vytváří a používá ke sledování průběhu operace. 
- Objekt úlohy má všechny informace, které potřebujete ke sledování stavu a průběhu zpracování operace. 

Monitorování úloh následujícím způsobem:

1. Na řídicím panelu > **úlohy** oddílu, můžete prohlédnout souhrnné informace o úlohy, které byly dokončeny, jsou v průběhu nebo čekání na vstup, za posledních 24 hodin. Kliknutím na jakýkoli stav zobrazíte další informace o příslušné úlohy.
2. Klikněte na tlačítko **zobrazit všechny** zobrazíte všechny úlohy za posledních 24 hodin.

    > [!NOTE]
    > Informace o úloze můžete dostat taky z nabídky trezoru > **úlohy Site Recovery**. 

2. V **úlohy Site Recovery** zobrazí se seznam, seznam úloh. V horní nabídce, které můžete získat podrobnosti o chybě pro konkrétní úlohy, můžete filtrovat seznam úloh na základě určitých kritérií a exportovat vybrané podrobnosti úlohy do Excelu.
3. Podrobnosti o úloze můžete zobrazit kliknutím. 

## <a name="monitor-virtual-machines"></a>Monitorování virtuálních počítačů

Přidání řídicího panelu můžete sledovat počítače na stránce virtuální počítače. 

1. V trezoru, klikněte na tlačítko **replikované položky** zobrazíte seznam replikované počítače.  Alternativně můžete získat na filtrovaný seznam chráněných položek kliknutím na některý z oboru klávesové zkratky na stránce řídicího panelu.

    ![Site Recovery replikuje zobrazení seznamu položek](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Na **replikované položky** stránky, můžete zobrazit a filtrovat informace. V nabídce Akce v horní části můžete provádět akce pro konkrétní počítač, včetně systémem testovací převzetí služeb při selhání nebo zobrazení konkrétní chyby.
3.  Klikněte na tlačítko **sloupce** zobrazíte další vrácenými, třeba když chcete zobrazit cíl bodu obnovení, cílení na problémy s konfigurací a chyby replikace.
4. Klikněte na tlačítko **filtr** Chcete-li zobrazit informace v závislosti na konkrétní parametry, jako je například stav replikace nebo zásady konkrétní replikace.
5. Klikněte pravým tlačítkem na počítač k zahájení operace, jako je test převzetí služeb při selhání, nebo pokud chcete zobrazit podrobnosti o chybě s ním spojená.
6. Klikněte na počítač přejít na další podrobnosti pro něj. Podrobnosti zahrnují:
      - **Informace o replikaci**: aktuální stav a stav počítače.
      - **Cíl bodu obnovení** (bodu obnovení rpo): aktuální cíl bodu obnovení pro virtuální počítač a čas, kdy se naposledy spočítá cíle bodu obnovení.
      - **Body obnovení**: nejnovější dostupné body obnovení pro počítač.
      - **Připravenost na převzetí služeb při selhání**: Určuje, zda testovací převzetí služeb spuštěné v počítači agenta verze, která běží na počítači (pro počítače se službou Mobility) a případných problémů s konfigurací.
      - **Chyby**: seznam příznaky Chyba replikace aktuálně pozorovaný na počítači a je to možné příčiny/akce.
      - **Události**: chronologický seznam posledních událostí vliv na tento počítač. Podrobnosti o chybě zobrazuje aktuálně pozorovatelných chyba projevuje, zatímco události je Historický záznam problémů, které mají vliv počítače.
      - **Zobrazení infrastruktury**: Zobrazí stav infrastruktury pro scénář, když počítače se replikují do Azure.

    ![Site Recovery replikované položky. Podrobnosti o/overview](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)


### <a name="common-questions"></a>Časté dotazy

**Čím se liší od nejnovější dostupný bod obnovení cíle bodu obnovení**


- Site Recovery používá asynchronní vícefázový proces Pokud chcete počítače replikovat do Azure.
- V kroku předposlední replikace nedávné změny v počítači, spolu s metadat, zkopírovány do účtu úložiště mezipaměti nebo protokolu.
- Tyto změny, společně s značku k identifikaci bod zotavit se zapisují do účtu úložiště v cílové oblasti.
-  Site Recovery nyní mohou generovat obnovitelné bod pro virtuální počítač.
- V tomto okamžiku cíle bodu obnovení byla splněna změny doposud nahráli do účtu úložiště. Jinými slovy, se počítač cíle bodu obnovení v tomto okamžiku je rovna množství času uplynulo od časové razítko odpovídající obnovitelné bodu.
- Nyní Site Recovery vybere odesílaná data z účtu úložiště a platí pro repliky disky vytvořené pro počítač.
- Site Recovery pak vytvoří bod obnovení a zpřístupňuje tohoto bodu obnovení při převzetí služeb při selhání. Proto nejnovější dostupný bod obnovení Určuje časové razítko odpovídající nejnovějším dostupným bodem obnovení, které již byly zpracovány a použity na repliku disky.

> [!NOTE]
> Nesprávné systémového času na replikaci zdrojového počítače nebo na místní servery infrastruktury se zkosení vypočtená hodnota cíle bodu obnovení. Pro správné cíl bodu obnovení vytváření sestav, zajistěte, aby u všech serverů a počítačů přesný systémových hodin. 

## <a name="subscribe-to-email-notifications"></a>Přihlášení k odběru e-mailových oznámení

Můžete se přihlásit k odběru e-mailová oznámení pro tyto důležité události:
 
- Kritický stav pro replikované počítače.
- Žádné připojení mezi místní infrastrukturou komponenty a služby Site Recovery. Připojení mezi Site Recovery a místní servery v trezoru zaregistrované zjistí použití mechanismu prezenčního signálu.
- Selhání převzetí služeb při selhání.

Přihlášení odběru následujícím způsobem:

V trezoru > **monitorování a sestavy** klikněte na tlačítko **událostí ASR**.
2. Klikněte na tlačítko **e-mailová oznámení**.
3. V **e-mailová oznámení**, zapněte oznámení a zadejte, kdo k odeslání. Můžete odeslat na všichni správci předplatného pošle oznámení a volitelně konkrétní e-mailové adresy.

    ![E-mailová oznámení](./media/site-recovery-monitor-and-troubleshoot/email.png)
