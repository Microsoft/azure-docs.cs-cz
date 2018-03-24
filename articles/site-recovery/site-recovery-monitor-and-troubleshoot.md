---
title: Monitorování a řešení potíží s Azure Site Recovery | Microsoft Docs
description: Monitorování a řešení problémů s replikací Azure Site Recovery a operace pomocí portálu
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
ms.date: 02/22/2018
ms.author: bsiva
ms.openlocfilehash: b357a3231dac6dfa54cb02fe921baf771c0880f4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Monitorování a řešení potíží s Azure Site Recovery

V tomto článku se dozvíte, jak používat Azure Site Recovery v integrované funkce monitorování pro monitorování a řešení potíží s. Naučte se:
> [!div class="checklist"]
> - Pomocí řídicího panelu Azure Site Recovery (stránka Přehled trezoru)
> - Monitorování a řešení problémů s replikací
> - Monitorování Azure Site Recovery úlohy/operace
> - Přihlášení k odběru e-mailových oznámení

## <a name="using-the-azure-site-recovery-dashboard"></a>Pomocí řídicího panelu Azure Site Recovery

Řídicí panel Azure Site Recovery na stránce Přehled úložiště slučuje všechny informace o monitorování pro úložišti na jednom místě. Spusťte v řídicím panelu trezoru a ponořit hlouběji k získání dalších podrobností přechodem prostřednictvím části řídicího panelu. Hlavní části řídicího panelu Azure Site Recovery jsou následující:

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Přepínání mezi Azure Backup a Azure Site Recovery řídicí panely

Přepnutí přepínače v horní části stránky přehled umožňuje přepínat mezi stránky řídicího panelu pro obnovení lokality a zálohování. Tento výběr, jednou provedené, je zapamatovaných a převezme k otevření stránky přehled trezoru. Vyberte možnost obnovení lokality najdete v řídicím panelu Site Recovery. 

Různé části stránky řídicí panel Azure Site Recovery automaticky aktualizovat každých 10 minut tak, aby řídicí panel se vztahuje k nejnovější dostupné informace.

![Funkce monitorování na stránce Přehled Azure Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Replikované položky

Části replikované položky řídicí panel obsahuje přehled o stavu replikace chráněných serverů v trezoru. 

<table>
<tr>
    <td>V pořádku</td>
    <td>Replikace je obvykle pokročíte pro tyto servery a žádné chyby nebo upozornění příznaky byly zjištěny.</td>
</tr>
<tr>
    <td>Upozornění </td>
    <td>Jeden nebo více příznaky upozornění, které může mít vliv na replikace nebo znamenat, že replikace není pokročíte normálně zjistil pro tyto servery.</td>
</tr>
<tr>
    <td>Kritická</td>
    <td>Jeden nebo více příznaků chyba důležité replikace nebyly zjištěny pro tyto servery. Příznaky chyby jsou obvykle indikátory replikace je buď zablokuje nebo není pokročíte rychlého data změnit rychlost pro tyto servery.</td>
</tr>
<tr>
    <td>Neuvedeno</td>
    <td>Servery, které nejsou očekávané aktuálně k replikaci, jako jsou třeba servery, které byly při selhání.</td>
</tr>
</table>

Pokud chcete zobrazit seznam chráněných serverů filtrovaná podle stav replikace, klikněte na tlačítko popis stavu replikace vedle prstenec. Zobrazení, které všechny odkaz téměř názvu oddílu je zástupce replikované položky stránky trezoru. Pomocí zobrazení, že všechny na odkaz zobrazíte seznam všech serverů v trezoru.

### <a name="3-failover-test-success"></a>3. Úspěch testovací převzetí služeb při selhání

V části úspěch testovací převzetí služeb při selhání řídicího panelu uvede rozdělení virtuální počítače v úložišti na základě stavu testovací převzetí služeb při selhání. 

<table>
<tr>
    <td>Test doporučená</td>
    <td>Virtuální počítače, které předtím nebyl úspěšný testovací převzetí služeb od doby dosáhnou chráněném stavu.</td>
</tr>
<tr>
    <td>Úspěšně provedena</td>
    <td>Virtuální počítače, které má jeden nebo více úspěšné testovací převzetí služeb při selhání.</td>
</tr>
<tr>
    <td>Neuvedeno</td>
    <td>Virtuální počítače, které nejsou aktuálně vhodné pro testovací převzetí služeb. Příklady: v průběhu, servery, na které převzetí služeb při selhání se v průběhu, servery, pro které již testovací převzetí služeb při selhání serverů, je serverů, které počáteční replikace.</td>
</tr>
</table>

Klikněte na stav převzetí služeb při selhání testu vedle prstenec, takže najdete v seznamu chráněných serverů na základě jejich stavu testovací převzetí služeb při selhání.
 
> [!IMPORTANT]
> Jako osvědčený postup doporučujeme provést testovací převzetí služeb na chráněných serverech alespoň jednou za šest měsíců. Provedení testu převzetí služeb je jiný rušivý způsob, jak testovací převzetí služeb při selhání serverů a aplikací na izolovaném prostředí a pomůže vám vyhodnocení připravenosti kontinuity vaší firmy.

 Operaci testovací převzetí služeb při selhání na serveru nebo plán obnovení se považuje za úspěšnou, až poté, co byly úspěšně dokončeny operace testovacího převzetí služeb při selhání a operace čištění testovacího převzetí služeb při selhání.

### <a name="4-configuration-issues"></a>4. Problémy s konfigurací

V části problémy konfigurace zobrazuje seznam problémů, které může mít vliv na schopnost úspěšně převzetí služeb při selhání virtuálního počítače. Třídy problémy uvedené v této části jsou:
 - **Chybí konfigurace:** chráněných serverů chybí nezbytné konfigurace, třeba k síti pro obnovení nebo skupinu prostředků pro obnovení.
 - **Chybějící prostředky:** nakonfigurované prostředky cíl nebo obnovení nebyl nalezen nebo není k dispozici v rámci předplatného. Například prostředek byl odstraněn nebo byla migrována do jiné předplatné nebo skupinu prostředků. Následující konfigurace cílového nebo obnovení jsou monitorovány dostupnosti: cílová skupina prostředků, cílový virtuální síť a podsíť, protokolu nebo cílový účet úložiště, cílové skupiny dostupnosti, cílová IP adresa.
 - **Kvóty předplatného:** rovnováze kvótu prostředků dostupných předplatného se porovná s zůstatku, aby bylo možné převzetí služeb při selhání všechny virtuální počítače v trezoru. Pokud se nenajde dostatek dostupný zůstatek, je uvedená vyrovnávání nedostatečné kvótu. Kvóty pro následující prostředky Azure jsou monitorovány: počet jader virtuálního počítače, počet rodiny jader virtuálního počítače, počet síťových rozhraní karta (NIC).
 - **Aktualizace softwaru:** dostupnost nové aktualizace softwaru, vypršení platnosti verze softwaru.

Problémy s konfigurací (jiné než dostupnost aktualizací softwaru), se zjišťují pravidelné validátoru operací, který ve výchozím nastavení spouští každých 12 hodin. Můžete vynutit operaci validátor pro okamžité spuštění kliknutím na ikonu aktualizace vedle možnosti *problémy s konfigurací* nadpis oddílu.

Kliknutím na odkazy získat podrobnosti o uvedených problémy a virtuální počítače, je vliv. U konkrétních virtuálních počítačů, které mají vliv problémů, další informace získáte kliknutím **vyžaduje pozornost** odkaz ve sloupci Cíl konfigurace pro virtuální počítač. Podrobnosti zahrnují doporučení na tom, jak může napravit zjištěné problémy.

### <a name="5-error-summary"></a>5. Souhrn chyb

Souhrn části chyby zobrazuje chyba příznaky aktuálně aktivní replikace, které může mít vliv na replikaci serverů v trezoru, spolu s počtem ovlivněné entity z důvodu jednotlivé chyby.

Příznaky chyby replikace pro servery v kritickém nebo varovném replikace stavu najdete v tabulce souhrn chyb. 

- Chyby, které mají vliv na místní infrastruktury komponenty, například prezenční signál ze zprostředkovatele Azure Site Recovery systémem místní konfigurační Server, VMM server nebo hostitele Hyper-V bez přijetí jsou uvedené na začátku souhrn chyb část
- Příznaky chyby replikace, které mají vliv chráněných serverů je uveden jako další. Chyba tabulku souhrnu položky jsou seřazené v sestupném pořadí podle závažnosti chyby a pak v sestupném pořadí podle počtu ovlivněných servery.
 

> [!NOTE]
> 
>  Více příznaků chyby replikace může být dodržen na jednom serveru. Pokud máte více příznaků chyba na jednom serveru by každý chyba příznakem počet tento server v seznamu jeho dopad serverů. Po opravě základní problém, což vede k chybě příznakem zlepšit parametry replikace a je chyba odstraněna z virtuálního počítače.
>
> > [!TIP]
> Počet serverů ovlivněné je snadno pochopit, pokud jeden základní problém mohl mít dopad na několik serverů. Například k chybě sítě může potenciálně mít vliv na všechny servery pro replikaci z místního serveru do Azure. Toto zobrazení přenese rychle tak, že oprava, jeden základní problém opraví replikace pro více serverů.
>

### <a name="6-infrastructure-view"></a>6. Zobrazení infrastruktury

Zobrazení infrastruktury poskytuje vhodné vizuální reprezentace infrastruktury součásti účastnící replikace scénář. Znázorňuje také vizuálně stav připojení mezi různými servery a mezi servery a účastní se replikace služby Azure. 

Zelená řádek označuje, že připojení je v pořádku, zatímco red čáry s ikonu překryté chyby označuje existenci jedné nebo více příznaků chyby připojení mezi součástmi související se situací, které mají vliv. Ukazatele ukazatel myši nad ikonu chyba na řádku zobrazí chyba a počet ovlivněných entit. 

Kliknutím na ikonu chyby zobrazí filtrovaný seznam ovlivněných entity pro chybách.

![Zobrazení infrastruktury obnovení lokality (úložiště)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Zkontrolujte, zda jsou součásti místní infrastruktury (konfigurační Server, proces další servery, replikuje virtuální počítače VMware, hostitele Hyper-V, servery VMM) spuštěn nejnovější verzi softwaru Azure Site Recovery. Abyste mohli používat všechny funkce infrastruktury zobrazení, budete muset používat [kumulativní 22](https://support.microsoft.com/help/4072852) nebo novější pro Azure Site Recovery

Použití infrastruktury zobrazení, vyberte odpovídající replikace scénář (virtuální počítače Azure, VMware virtuálních počítačů nebo fyzický server nebo Hyper-V) v závislosti na vašem zdrojové prostředí. Zobrazení infrastruktury uvedené na stránce Přehled trezoru je agregovaným zobrazením trezoru. Můžete zobrazit další podrobnosti dolů další do jednotlivých součástí kliknutím na do polí.

Je k dispozici na stránce Přehled replikované položky zobrazení infrastruktury vymezeny do kontextu replikaci jeden počítač. Přejděte na stránku přehled pro replikaci server, přejděte na replikované položky v nabídce trezoru a vyberte server, chcete-li zobrazit podrobnosti.

### <a name="infrastructure-view---faq"></a>Zobrazení infrastruktury – nejčastější dotazy

**Otázka:** Proč není zobrazuje zobrazení infrastruktury pro virtuální počítač? </br>
**Odpověď:** Tato funkce zobrazení infrastruktury je jen pro virtuální počítače, které jsou replikaci do Azure k dispozici. Tato funkce není aktuálně k dispozici pro virtuální počítače, které jsou replikaci mezi místními servery.

**Otázka:** Proč se zobrazí počet virtuálních počítačů v různých z celkového počtu zobrazení infrastruktury úložiště v prstenec replikované položky?</br>
**Odpověď:** Zobrazení infrastruktury úložiště je vymezeny scénáře replikace. Účastí v aktuálně vybraném replikace scénáři jenom virtuální počítače jsou zahrnuty do počtu virtuálních počítačů v zobrazení infrastruktury. Pro vybraným scénářem, navíc jenom virtuální počítače, které jsou aktuálně nakonfigurované pro replikaci do Azure jsou zahrnuty do počtu virtuálních počítačů v zobrazení infrastruktury (třeba Fo: převzít služby při selhání virtuálního počítače, virtuální počítače replikující se zpět pro místní lokality nejsou zahrnuty v zobrazení infrastruktury.)

**Otázka:** Proč je počet replikované položky zobrazené v panel essentials na stránce Přehled liší od celkový počet zobrazených v grafu prstenec v řídicím panelu replikované položky?</br>
**Odpověď:** Jenom těch virtuálních počítačů, pro které počáteční replikace byla dokončena zahrnuty do počtu ukazuje panel essentials. Celkový počet replikované položky prstenec zahrnuje všechny virtuální počítače v trezoru, včetně serverů pro které počáteční replikaci právě probíhá.

**Otázka:** Jaké scénáře replikace je k dispozici pro zobrazení infrastruktury? </br>
**Odpověď:**
>[!div class="mx-tdBreakAll"]
>|Scénář replikace  | Stav virtuálního počítače  | Zobrazení infrastruktury k dispozici  |
>|---------|---------|---------|
>|Virtuální počítače replikující mezi dvěma místními lokalitami     | -        | Ne      |
>|Vše     | Převzetí služeb při selhání         |  Ne       |
>|Virtuální počítače replikující mezi dvěma oblastmi Azure     | Počáteční replikace v průběhu nebo chráněné         | Ano         |
>|Virtuální počítače VMware replikaci do Azure     | Počáteční replikace v průběhu nebo chráněné        | Ano        |
>|Virtuální počítače VMware replikaci do Azure     | Při selhání se replikovat zpět na stránku VMware místní virtuální počítače         | Ne        |
>|Technologie Hyper-V virtuální počítače replikující se do Azure     | Počáteční replikace v průběhu nebo chráněné        | Ano       |
>|Technologie Hyper-V virtuální počítače replikující se do Azure     | Převzetí služeb při selhání nebo navrácení služeb po obnovení v průběhu        |  Ne       |


### <a name="7-recovery-plans"></a>7. Plány obnovení

V části plány obnovení ukazuje počet plány obnovení v trezoru. Klikněte na číslo, které má zobrazit seznam plánů obnovení, vytvořit nové plány obnovení nebo upravit stávající. 

### <a name="8-jobs"></a>8. Úlohy

Azure Site Recovery úlohy sledování stavu operace Azure Site Recovery. Většinu operací v Azure Site Recovery se spustí asynchronně, s úlohou sledování slouží ke sledování průběh operace.  Naučte se monitorovat stav operace, najdete v článku [monitorování Azure lokality úlohy nebo operace obnovení](#monitor-azure-site-recovery-jobsoperations) části.

V této části úlohy řídicí panel poskytuje následující informace:

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
    <td>Azure Site Recovery úlohy, které jsou momentálně pozastaven čekání na vstup od uživatele.</td>
</tr>
</table>

Zobrazení všech odkaz vedle nadpis oddílu je zástupce, přejděte na stránku seznamu úloh.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Monitorování a řešení problémů s replikací

Kromě informací, které jsou k dispozici na stránce řídicího panelu trezoru můžete získat další podrobnosti a informace o odstraňování potíží v seznamu stránce virtuální počítače a stránce s podrobnostmi o virtuální počítač. Seznam chráněných virtuálních počítačů v trezoru můžete zobrazit výběrem **replikované položky** možnost v nabídce trezoru. Alternativně můžete získáte filtrovaný seznam chráněné položky klepnutím na libovolné vymezená zkratky, které jsou k dispozici na stránce řídicího panelu trezoru.

![Site Recovery replikované položky zobrazení seznamu](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

Možnost filter na stránce replikované položky seznamu umožňuje aplikovat různé filtry, jako je stav replikace a zásady replikace. 

Možnost selektoru sloupců umožňuje zadat další sloupce, která se má zobrazit jako plánovaný bod obnovení, problémy s konfigurací cíl a chyby replikace. Můžete zahájit operací na virtuální počítač nebo zobrazit chyby kliknutím pravým tlačítkem na konkrétní řádek seznam počítačů, které mají vliv virtuálního počítače.

K zobrazení dalších podrobností vyberte kliknutím na virtuální počítač. Otevře se stránka Podrobnosti virtuálního počítače. Přehled stránka v části Podrobnosti virtuálního počítače obsahuje řídicí panel, kde najdete další informace týkající se k počítači. 

Na stránce Přehled pro replikaci počítač zjistíte:
- Plánovaný bod obnovení (plánovaného bodu obnovení): Aktuální plánovaný bod obnovení pro virtuální počítač a čas, kdy se poslední spočítala plánovaný bod obnovení.
- Nejnovější dostupné body obnovení pro počítač
- Potíže s konfigurací Pokud žádné, může mít vliv na převzetí služeb při selhání připravenost počítače. Kliknutím na odkaz k získání dalších podrobností.
- Podrobnosti o chybě: seznam Příznaky chyby replikace v současné době zjištěnými na počítači spolu s možné příčiny a doporučené nápravy
- Události: Chronologickém seznam poslední události, které mají vliv na počítač. A podrobnosti o chybě se zobrazí příznaky aktuálně pozorovatelné chyba na počítači, je události v oblasti různé události, které může mít dopad na počítač, včetně chyba příznaky, které jste si nejspíše dříve bylo všimli pro počítač.
- Zobrazení infrastruktury pro počítače replikující se do Azure

![Site Recovery replikované položky podrobnosti/přehled](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

V nabídce Akce v horní části stránky poskytuje možnosti provádět různé operace, například testovací převzetí služeb při selhání na virtuálním počítači. Na tlačítko Podrobnosti o chybě v nabídce Akce umožňuje zobrazit všechny aktivní chyby včetně chyby replikace, problémy s konfigurací a upozornění osvědčených postupů na základě konfigurace pro virtuální počítač.

> [!TIP]
> Jak se liší od nejnovějším dostupným bodem obnovení plánovaného bodu RPO nebo obnovení?
> 
>Azure Site Recovery používá více asynchronní proces krok replikovat virtuální počítače Azure. V kroku předposlední replikace nedávné změny na virtuálním počítači společně s metadata se zkopírují do účtu úložiště protokol/mezipaměti. Jakmile se tyto změny společně s značky k identifikaci bod použitelná pro obnovení byla zapsána na účet úložiště cílová oblast, Azure Site Recovery obsahuje informace potřebné ke generování obnovitelné bodu pro virtuální počítač. Plánovaný bod obnovení je v tomto okamžiku splněn změny doposud nahrán do účtu úložiště. Jinými slovy, vyjádřené v jednotkami času, plánovaný bod obnovení pro virtuální počítač v tomto okamžiku se rovná množství času uplynulý z časové razítko odpovídající bodem použitelná pro obnovení.
>
>Služba Azure Site Recovery, operační na pozadí, vybere odesílaná data z účtu úložiště a použije je na disky repliky pro virtuální počítač vytvořit. Potom vytvoří bod obnovení a zpřístupní tento bod obnovení na převzetí služeb při selhání. Nejnovější bod obnovení Určuje časové razítko odpovídající nejnovější bod obnovení, která již byla zpracována a použity na repliku disky.
>> [!WARNING]
> Vypočtená hodnota plánovaného bodu obnovení bude zkreslit zkreslilo hodin nebo nesprávné systémového času na replikaci zdrojového počítače nebo na místní servery infrastruktury. K zajištění přesné vytváření sestav nástroje plánovaný bod obnovení hodnoty zajistěte, aby byl systémový čas v servery zapojené do replikace přesná. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>Monitorování Azure Site Recovery úlohy/operace

Azure Site Recovery provede operace, které zadáte asynchronně. Příklady můžete provádět operace povolení replikace, vytvoření plánu obnovení, testovací převzetí služeb při selhání, aktualizovat nastavení replikace atd. Každé takové operace má odpovídající úlohu, která se vytvoří pro sledování a audit operaci. Objekt úlohy má všechny potřebné informace požadované pro můžete sledovat stav a průběh operace. Na stránce úlohy můžete sledovat stav různých operací trezoru Site Recovery. 

Chcete-li zobrazit seznam úlohy Site Recovery trezoru, přejděte **monitorování a sestavy** části nabídky trezoru a vyberte úlohy > úlohy Site Recovery. Vyberte úlohu v seznamu úloh na stránce kliknutím na zobrazíte další podrobnosti k zadanou úlohu. Pokud úloha nebyla dokončena úspěšně nebo obsahuje chyby, kliknutím na tlačítko Podrobnosti o chybě v horní části stránky podrobnosti úlohy (také přístupné ze stránky seznam úloh kliknutím pravým tlačítkem na neúspěšná uvidíte Další informace na chyby a jejich možná nápravy úloha.) Můžete použít možnost filtru v nabídce Akce v horní části stránky seznamu úloh pro filtrování seznamu podle konkrétních kritérií a použít tlačítko pro export Export podrobnosti vybrané úlohy, které mají v aplikaci excel. Přístup k zobrazení seznamu úloh můžete také pomocí zástupce k dispozici na stránce řídicího panelu Site Recovery. 

 Pro operace, které můžete provádět z portálu Azure vytvoření úlohy a její aktuální stav lze také sledovat z části oznámení (ikonu zvonku vpravo nahoře) na portálu Azure.

## <a name="subscribe-to-email-notifications"></a>Přihlášení k odběru e-mailových oznámení

Funkce oznámení v vytvořená e-mailu umožňuje přihlásit se k odběru e-mailová oznámení pro kritické události. Pokud předplatné, se odesílají e-mailová oznámení pro následující události:
- Stav replikace replikaci počítače docházelo k omezení na kritický.
- Žádné připojení mezi místní infrastruktury komponenty a službou Azure Site Recovery. Připojení do služby Site Recovery služby z místní infrastruktury komponenty jako konfigurační Server (VMware) nebo System Center Virtual Machine Manager(Hyper-V) registrovaný k úložišti je zjištěna pomocí mechanismu prezenčního signálu.
- Selhání operací převzetí služeb při selhání, pokud existuje.

Přihlásit se k odběru e-mailová oznámení Azure Site Recovery, přejděte **monitorování a sestavy** části nabídky trezoru a:
1. Vyberte výstrahy a události > události obnovení lokality.
2. Vyberte z nabídky na stránku události, který se otevírá "E-mailová oznámení".
3. Chcete-li zapnout nebo vypnout e-mailová oznámení a vybrat příjemce pro oznámení, použijte Průvodce e-mailové oznámení. Můžete určit, že všichni správci předplatného odeslat oznámení, nebo zadejte seznam e-mailové adresy odesílat oznámení. 
