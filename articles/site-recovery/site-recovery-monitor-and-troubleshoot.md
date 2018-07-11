---
title: Monitorování a řešení potíží s Azure Site Recovery | Dokumentace Microsoftu
description: Monitorování a řešení problémů replikace Azure Site Recovery a operací pomocí portálu
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 84b5bf3be09083a69216802fc7f557de1a7f0ee6
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917529"
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Monitorování a řešení potíží s Azure Site Recovery

V tomto článku se dozvíte, jak používat Azure Site Recovery v sestavených funkce monitorování pro monitorování a řešení potíží. Naučte se:
> [!div class="checklist"]
> - Pomocí řídicího panelu Azure Site Recovery (stránka Přehled úložiště)
> - Monitorování a řešení problémů s replikací
> - Monitorování Azure Site Recovery úloh/operace
> - Přihlášení k odběru e-mailových oznámení

## <a name="using-the-azure-site-recovery-dashboard"></a>Na řídicím panelu Azure Site Recovery

Řídicí panel Azure Site Recovery na trezor stránka s přehledem slučuje všechny informace o monitorování pro úložiště na jednom místě. Začněte na řídicím panelu trezoru a ponořte se hlouběji, abyste získali více podrobností tak, že přejdete do části řídicího panelu. Hlavní části řídicího panelu Azure Site Recovery jsou následující:

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Přepínání mezi Azure Backup a Azure Site Recovery řídicích panelů

Přepínač v horní části stránky přehled umožňuje přepínat mezi stránky řídicího panelu pro Site Recovery a Backup. Tohoto výběru, jednou, se uloží a při příštím otevření stránky přehled trezoru na výchozí. Vyberte možnosti Site Recovery, abyste zobrazili řídicí panel Site Recovery. 

Různé části na stránce řídicího panelu Azure Site Recovery automaticky aktualizovat každých 10 minut, aby řídicí panel zobrazuje nejnovější dostupné informace.

![Funkce monitorování na stránce Přehled Azure Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Replikované položky

Replikované položky část řídicího panelu představuje přehled o stavu replikace chráněných serverů v trezoru. 

<table>
<tr>
    <td>V pořádku</td>
    <td>Replikace probíhá obvykle pro tyto servery a bez chyby nebo upozornění příznaky byly zjištěny.</td>
</tr>
<tr>
    <td>Upozornění </td>
    <td>Byl zjištěn jeden nebo více upozornění příznaky, které může mít vliv na replikaci nebo indikoval, že je replikace nepokračuje, obvykle pro tyto servery.</td>
</tr>
<tr>
    <td>Důležité</td>
    <td>Jeden nebo více příznaků chyba důležité replikace byly zjištěny pro tyto servery. Příznaky tyto chyby jsou obvykle indikátory, že je replikace se zasekla buď nebo neprobíhá tak rychle, jak data změnit rychlost pro tyto servery.</td>
</tr>
<tr>
    <td>Neuvedeno</td>
    <td>Servery, které nejsou aktuálně očekává se replikace, jako jsou třeba servery, které selhaly přes.</td>
</tr>
</table>

Pokud chcete zobrazit seznam chráněných serverů filtrovat podle stavu replikace, klikněte na popis stavu replikace vedle prstencovém. Zobrazení, které odkazovat v názvu oddílu je zástupce na stránce replikovaných položek pro trezor. Pomocí zobrazení, že všechny odkaz zobrazíte seznam všech serverů v trezoru.

### <a name="3-failover-test-success"></a>3. Úspěšné testovací převzetí služeb při selhání

Část úspěšné testovací převzetí služeb při selhání řídicího panelu nabízí rozdělení virtuálních počítačů v trezoru, na základě stavu testovacího převzetí služeb při selhání. 

<table>
<tr>
    <td>Doporučuje se test</td>
    <td>Virtuální počítače, u kterých nedošlo úspěšné testovací převzetí služeb při selhání od doby dosáhnou chráněném stavu.</td>
</tr>
<tr>
    <td>Proběhlo úspěšně</td>
    <td>Virtuální počítače, které jste využili nejméně úspěšné testovací převzetí služeb při selhání.</td>
</tr>
<tr>
    <td>Neuvedeno</td>
    <td>Virtuální počítače, které nejsou momentálně nárok testovací převzetí služeb při selhání. Mezi příklady patří: selhání serverů, serverů, pro které počáteční replikace je v průběhu, u kterých je převzetí služeb při selhání v průběhu servery, servery, u kterých již testovací převzetí služeb.</td>
</tr>
</table>

Klikněte na stav převzetí služeb při selhání testu vedle prstencovém, takže zobrazit seznam chráněných serverů na základě jejich stavu testovacího převzetí služeb při selhání.
 
> [!IMPORTANT]
> Jako osvědčený postup se doporučuje provést testovací převzetí služeb na chráněných serverech alespoň jednou za šest měsíců. Testovací převzetí služeb při selhání je mimo rušivé způsob, jak otestovat převzetí služeb při selhání serverů a aplikací izolovaného prostředí a pomůže vám vyhodnocení připravenosti vaší obchodní kontinuity podnikových procesů.

 Operaci testovací převzetí služeb při selhání na serveru nebo plán obnovení se považuje za úspěšná, až po operaci převzetí služeb při selhání testu a operace vyčištění testovacího převzetí služeb při selhání byly úspěšně dokončeny.

### <a name="4-configuration-issues"></a>4. Problémy s konfigurací

Konfigurační oddíl problémů zobrazí seznam problémů, které může mít vliv na schopnost úspěšně převzetí služeb při selhání virtuálních počítačů. Třídy problémy uvedené v této části jsou:
 - **Chybí konfigurace:** chráněných serverů chybí potřebné konfigurace, jako je například síť pro obnovení nebo skupinu prostředků pro obnovení.
 - **Chybějící prostředky:** nakonfigurovaný cíl nebo obnovení prostředků nebyla nalezena nebo není k dispozici v rámci předplatného. Například prostředek byl odstraněn nebo se migroval do jiné předplatné nebo skupinu prostředků. Následující cíl či obnovení konfigurace se monitorují dostupnosti: cílová skupina prostředků, cílové virtuální síť a podsíť, účtu úložiště protokolu nebo cíle, cílová skupina dostupnosti, cílová IP adresa.
 - **Kvóta předplatného:** zůstatku předplatného k dispozici prostředek kvóty se porovná zůstatek na účtu musí být schopen převzetí služeb při selhání všech virtuálních počítačů v trezoru. Pokud se nenajde dostatek dostupné zůstatek, se použije v hlášení zůstatek Nedostatečná kvóta. Kvóty pro následující prostředky Azure se monitorují: počet jader virtuálního počítače, počet virtuálních počítačů řady jader, počet síťových rozhraní (NIC) karty.
 - **Aktualizace softwaru:** dostupnost nové aktualizace softwaru, vypršení platnosti verze softwaru.

Problémy s konfigurací (jiné než dostupnost aktualizací softwaru), se zjišťují operací pravidelné ověřování, který ve výchozím nastavení spustí každých 12 hodin. Můžete vynutit operaci program pro ověření spustit okamžitě kliknutím na ikonu aktualizace vedle možnosti *problémy s konfigurací* nadpis oddílu.

Kliknutím na odkazy zobrazíte další podrobnosti o uvedených problémy a virtuální počítače, je vliv. Pro problémy vliv na konkrétní virtuální počítače, můžete získat další podrobnosti kliknutím **vyžaduje pozornost** odkaz ve sloupci cíl konfigurací pro virtuální počítač. Podrobnosti zahrnují doporučení na tom, jak můžete opravit zjištěné problémy.

### <a name="5-error-summary"></a>5. Souhrn chyb

Souhrnné části chyba zobrazuje příznaky replikace aktivní chyby, které může mít vliv na replikaci serverů v trezoru a počty ovlivněných entity z důvodu každé chyby.

Příznaky Chyba replikace pro servery ve stavu replikace kritického nebo výstražného stavu lze zobrazit v tabulce souhrnu chyb. 

- Vliv na místních komponent infrastruktury, například prezenční signál ze zprostředkovatele Azure Site Recovery na místní konfigurační Server, VMM server nebo hostitele Hyper-V bez přijetí chyby jsou uvedené na začátku souhrn chyb oddíl
- Příznaky Chyba replikace vliv na chráněných serverech je uveden jako další. Chyba souhrnnou tabulku položky jsou seřazeny v sestupném pořadí podle závažnosti chyby a poté v sestupném pořadí podle počtu ovlivněných servery.
 

> [!NOTE]
> 
>  Více příznaků Chyba replikace může být dodržen na jednom serveru. Pokud existuje více příznaků chyba na jednom serveru počítá každé chybě příznaku tento server v seznamu svých ovlivněné serverů. Po opravě příčiny problému, což vede k chybě příznaku zlepšit parametry replikace a chyba se vymaže z virtuálního počítače.
>
> > [!TIP]
> Počet ovlivněných serverů je užitečný způsob, jak pochopit, pokud jeden základní problém může mít vliv na víc serverů. Například poruchu sítě může případně ovlivnit všechny servery, které se replikují z místní lokality do Azure. Toto zobrazení rychle vyjadřuje, že oprava, že jeden problém opraví replikace pro více serverů.
>

### <a name="6-infrastructure-view"></a>6. Zobrazení infrastruktury

Zobrazení infrastruktury poskytuje scénář z pohledu vizuální znázornění infrastruktury součásti účastnící se replikace. Znázorňuje také vizuální stav připojení mezi různými servery a mezi servery a služby Azure, které je součástí replikace. 

Zelená čára označuje, že připojení je v pořádku, zatímco červená čára s ikonou překryté chyba značící existenci jeden nebo více příznaků chyba vliv na připojení mezi zahrnuté komponenty. Ukazatel myši přesunout ukazatel myši na ikonu chyba na řádku zobrazí chyba a počet ovlivněných entit. 

Kliknutím na ikonu chyby zobrazí filtrovaný seznam ovlivněných entity pro chyby.

![Zobrazení infrastruktury Site Recovery (trezor)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Ujistěte se, že místní komponenty infrastruktury (konfigurační Server, dalších procesových serverů, které se replikují virtuální počítače VMware, hostitelé Hyper-V, servery VMM) běží nejnovější verze softwaru Azure Site Recovery. Abyste mohli používat všechny funkce zobrazení infrastruktury, budete muset běžet [22 kumulativní aktualizace](https://support.microsoft.com/help/4072852) nebo novější pro službu Azure Site Recovery

Pokud chcete použít zobrazení infrastruktury, výběr scénáře replikace příslušné (virtuální počítače Azure, virtuální počítače/fyzické prostředky serveru VMware nebo Hyper-V) v závislosti na zdrojové prostředí. Zobrazení infrastruktury, které jsou uvedené na stránce Přehled trezoru je souhrnný náhled na trezoru. Můžete přejít k další nižší do jednotlivých komponent kliknutím na pole.

Zobrazení infrastruktury omezené na kontext jedné replikujícím počítači je k dispozici na stránce přehledu replikované položky. Přejděte na stránku přehled pro replikaci serveru, přejděte na replikované položky v nabídce trezoru a vyberte server, pokud chcete zobrazit podrobnosti pro.

### <a name="infrastructure-view---faq"></a>Zobrazení infrastruktury – nejčastější dotazy

**Otázka:** Proč se mi nezobrazují zobrazení infrastruktury pro můj virtuální počítač? </br>
**Odpověď:** Zobrazení funkcí infrastruktury dostupná jenom pro virtuální počítače, které se replikují do Azure. Tato funkce není aktuálně k dispozici pro virtuální počítače, které se replikují mezi místními lokalitami.

**Otázka:** Proč se zobrazí počet virtuálních počítačů v zobrazení infrastruktury trezoru se liší od celkového počtu v prstencovém replikované položky?</br>
**Odpověď:** Zobrazení infrastruktury úložiště je určeno scénáře replikace. Účast ve scénáři s aktuálně vybranou replikace jenom virtuální počítače jsou součástí počet virtuálních počítačů v zobrazení infrastruktury. Navíc ve vybraném scénáři pouze virtuální počítače, které jsou aktuálně nakonfigurované pro replikaci do Azure jsou zahrnuté v počet virtuálních počítačů v zobrazení infrastruktury (příklad Fo: převzetí služeb virtuálních počítačů při selhání, virtuální počítače replikovaly zpátky do místní lokality nejsou zahrnuty v zobrazení infrastruktury.)

**Otázka:** Proč je počet replikovaných položek zobrazených v zásuvce essentials na stránce s přehledem se liší od celkového počtu replikovaných položek zobrazených v prstencový graf na řídicí panel?</br>
**Odpověď:** Jenom těch virtuálních počítačů které počáteční replikace dokončí jsou zahrnuté v počtu uvedené v zásuvce essentials. Celkový počet replikované položky prstencový zahrnuje všechny virtuální počítače v trezoru, včetně serverů pro které počáteční replikace probíhá.

**Otázka:** Jaké scénáře replikace je k dispozici pro zobrazení infrastruktury? </br>
**Odpověď:**
>[!div class="mx-tdBreakAll"]
>|Scénáře replikace  | Stav virtuálního počítače  | Zobrazení infrastruktury k dispozici  |
>|---------|---------|---------|
>|Virtuální počítače, které se replikují mezi dvěma místní weby     | -        | Ne      |
>|Vše     | Převzetí služeb při selhání         |  Ne       |
>|Virtuální počítače, které se replikují mezi dvěma oblastmi Azure     | Počáteční replikace probíhá, nebo chráněné         | Ano         |
>|Virtuální počítače VMware, které se replikují do Azure     | Počáteční replikace probíhá, nebo chráněné        | Ano        |
>|Virtuální počítače VMware, které se replikují do Azure     | Došlo k převzetí služeb virtuálního počítače replikuje zpět do místní lokality VMware         | Ne        |
>|Virtuální počítače Hyper-V replikaci do Azure     | Počáteční replikace probíhá, nebo chráněné        | Ano       |
>|Virtuální počítače Hyper-V replikaci do Azure     | Převzetí služeb při selhání a navrácení služeb po obnovení probíhá        |  Ne       |


### <a name="7-recovery-plans"></a>7. Plány obnovení

V části plány obnovení zobrazí počet plánů obnovení v trezoru. Klikněte na číslo, které má seznam plánů obnovení, vytvořit nové plány obnovení nebo upravovat stávající. 

### <a name="8-jobs"></a>8. Úlohy

Azure Site Recovery úlohy sledování stavu operací Azure Site Recovery. Většinu operací ve službě Azure Site Recovery se provedl asynchronně, s úlohou sledování se používá ke sledování průběhu operace.  Další informace o monitorování stavu operace, najdete v článku [monitorování Azure Site Recovery/operace úloh](#monitor-azure-site-recovery-jobsoperations) oddílu.

Tato část úlohy řídicího panelu poskytuje následující informace:

<table>
<tr>
    <td>Selhalo</td>
    <td>Neúspěšné úlohy Azure Site Recovery za posledních 24 hodin</td>
</tr>
<tr>
    <td>Probíhá</td>
    <td>Azure Site Recovery úlohy, které jsou aktuálně probíhá</td>
</tr>
<tr>
    <td>Čekání na vstup</td>
    <td>Azure Site Recovery úlohy, které jsou momentálně pozastavený čekání na vstup od uživatele.</td>
</tr>
</table>

Zobrazit vše odkaz vedle záhlaví oddílu je zástupce, přejděte na stránku seznamu úloh.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Monitorování a řešení problémů s replikací

Kromě informací, které jsou k dispozici na stránce řídicího panelu trezoru můžete získat další podrobnosti a informace o odstraňování potíží v stránce se seznamem virtuálních počítačů a na stránce podrobností virtuálního počítače. Zobrazí se seznam chráněných virtuálních počítačů v trezoru tak, že vyberete **replikované položky** možnost v nabídce trezoru. Alternativně můžete získat na filtrovaný seznam chráněných položek seřadit klepnutím na s vymezeným oborem zkratek, které jsou k dispozici na stránce řídicího panelu trezoru.

![Site Recovery replikuje zobrazení seznamu položek](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

Možnost filter na stránce replikovaných položek seznamu umožňuje aplikovat různé filtry, např. stav replikace a zásady replikace. 

Výběr možnosti sloupce umožňuje určit další sloupce, které má být zobrazen jako cíle bodu obnovení, problémy s konfigurací cílové a chyby replikace. Můžete zahájit operace na virtuálním počítači nebo zobrazit chyby dopadu na virtuální počítač kliknutím pravým tlačítkem na konkrétní řádek seznam počítačů.

K podrobnostem, vyberte kliknutím na virtuální počítač. Tím se otevře na stránce podrobností virtuálního počítače. Stránka s přehledem v části Podrobnosti virtuálního počítače obsahuje řídicí panel, kde najdete další informace týkající se k počítači. 

Na stránce Přehled replikujícím počítači najdete:
- Cíl bodu obnovení (bodu obnovení rpo): aktuální cíl bodu obnovení pro virtuální počítač a čas, kdy se naposledy spočítá cíle bodu obnovení.
- Nejnovější dostupné body obnovení pro počítač
- Problémy s konfigurací Pokud libovolný, který může mít vliv na připravenost převzetí služeb při selhání počítače. Klikněte na odkaz zobrazíte další podrobnosti.
- Podrobnosti o chybě: seznam příznaky Chyba replikace v současné době pozorovaný na počítač spolu s možné příčiny a doporučené nápravy
- Události: Chronologický seznam posledních událostí vliv na tento počítač. Podrobnosti o chybě se zobrazí aktuálně pozorovatelných chyba projevuje na počítači, je události Historický záznam různé události, které může mít vliv na počítače, včetně chyba příznaky, které dřív byly všimnout pro počítač.
- Zobrazení infrastruktury pro počítače, které se replikují do Azure

![Site Recovery replikované položky. Podrobnosti o/overview](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

V nabídce Akce v horní části stránky poskytuje možnosti, jak provádět různé operace, jako testovací převzetí služeb při selhání na virtuálním počítači. Tlačítko Podrobnosti o chybě v nabídce Akce umožňuje zobrazit všechny aktivní chyby, včetně replikace chyby, problémy s konfigurací a upozornění osvědčených postupů na základě konfigurace pro virtuální počítač.

> [!TIP]
> Čím se liší od nejnovější dostupný bod obnovení cíle bodu obnovení nebo zotavení cílových hodnot plánovaného bodu?
> 
>Azure Site Recovery používá asynchronní o více krocích k replikaci virtuálních počítačů do Azure. V kroku předposlední replikace nedávnými změnami na virtuálním počítači společně s metadata jsou zkopírovány do účtu úložiště mezipaměti nebo protokolu. Až tyto změny společně s značky pro identifikaci obnovitelné bodu byl zapsán do účtu úložiště v cílové oblasti, Azure Site Recovery obsahuje informace potřebné ke generování obnovitelné bod pro virtuální počítač. V tomto okamžiku cíle bodu obnovení byla splněna změny doposud nahráli do účtu úložiště. Jinými slovy, cíl bodu obnovení pro virtuální počítač v tuto chvíli vyjádřena v jednotkách času, se rovná množství času uplynulo od časové razítko odpovídající obnovitelné bodu.
>
>Služba Azure Site Recovery, provoz na pozadí, vybere odesílaná data z účtu úložiště a použije je na disky repliky vytvořený pro virtuální počítač. Potom vytvoří bod obnovení a zpřístupňuje tohoto bodu obnovení při převzetí služeb při selhání. Nejnovější dostupný bod obnovení Určuje časové razítko odpovídající nejnovějším dostupným bodem obnovení, které již byly zpracovány a použity na repliku disky.
>> [!WARNING]
> Vypočtená hodnota cíle bodu obnovení se zkosení hodiny výrazně nerovnoměrnou distribucí nebo nesprávný systémového času na replikaci zdrojového počítače nebo na místních serverech infrastruktury. K zajištění přesné vytváření sestav cíle bodu obnovení hodnoty zkontrolujte, že systémové hodiny na serverech součástí replikace přesné. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>Monitorování Azure Site Recovery úloh/operace

Azure Site Recovery provádí operace, které zadáte asynchronně. Příklady můžete provádět operace povolení replikace, vytvoření plánu obnovení, testovací převzetí služeb při selhání, aktualizovat nastavení replikace atd. Všechny tyto operace má odpovídající úlohu, která je vytvořena sledovat a auditovat operace. Objekt úlohy obsahuje všechny potřebné informace požadované pro vám umožní sledovat stav a průběh operace. Na stránce úlohy můžete sledovat stav různých operací Site Recovery pro trezor. 

Chcete-li zobrazit seznam úlohy Site Recovery pro trezor, přejděte **monitorování a sestavy** část v nabídce trezoru a vyberte úlohy > úlohy Site Recovery. Vyberte úlohu ze seznamu úloh na stránce kliknutím zobrazíte další podrobnosti na zadanou úlohu. Pokud úloha nebyla úspěšně dokončena nebo obsahuje chyby, zobrazí se další informace na opravy chyb a jejich možná kliknutím na tlačítko Podrobnosti o chybě v horní části stránky s podrobnostmi úlohy (taky dostupný ze stránky seznamu úloh kliknutím pravým tlačítkem na neúspěšné úloha.) Můžete použít možnost filtru v nabídce Akce nahoře na stránce seznamu úloh pro filtrování seznamu závislosti na konkrétních kritériích a použijte tlačítko pro export Export podrobnosti vybrané úlohy do excelového. Zobrazení seznamu úloh se můžete dostat taky z místní k dispozici na stránce řídicího panelu Site Recovery. 

 Pro operace, které provedete na webu Azure Portal vytvořených úloh a jejich aktuální stav lze také sledovat z části oznámení (ikona zvonku v pravém horním rohu) na webu Azure portal.

## <a name="subscribe-to-email-notifications"></a>Přihlášení k odběru e-mailových oznámení

Funkci integrovaného e-mailové oznámení umožňuje přihlásit se k e-mailová oznámení pro kritické události odběru. Je-li připojila, e-mailová oznámení jsou zasílány pro následující události:
- Stav replikace replikujícím počítači snížení úrovně na kritický.
- Žádné připojení mezi místní infrastrukturou komponenty a služby Azure Site Recovery. Připojení k Site Recovery service z místní infrastruktury komponenty jako konfigurační Server (VMware) nebo System Center Virtual Machine Manager(Hyper-V) zaregistrují do trezoru se zjistí pomocí mechanismu prezenčního signálu.
- Selhání operací převzetí služeb při selhání, pokud existuje.

Chcete-li se přihlásit k odběru e-mailová oznámení pro Azure Site Recovery, přejděte **monitorování a sestavy** části nabídky trezoru a:
1. Vyberte výstrahy a události > Site Recovery události.
2. Vyberte z nabídky na stránku události, která je otevřena "E-mailová oznámení".
3. Pomocí Průvodce e-mailové oznámení můžete zapnout nebo vypnout e-mailová oznámení a vybrat příjemce oznámení. Můžete určit, že všichni správci předplatného pošle oznámení, nebo zadejte seznam e-mailové adresy odesílat oznámení. 
