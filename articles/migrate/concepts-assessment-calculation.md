---
title: Výpočty pro vyhodnocení v Azure Migrate | Microsoft Docs
description: Poskytuje přehled výpočtů hodnocení ve službě Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 87aa48c992b7887ce86c43cac29910dcc57b3e91
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234290"
---
# <a name="assessment-calculations"></a>Výpočty hodnocení

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a privátních a veřejných cloudových instancí do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Posuzování serveru je nástroj v Azure Migrate, který vyhodnocuje místní servery pro migraci do Azure. Tento článek poskytuje informace o tom, jak se vypočítávají vyhodnocení.

## <a name="whats-in-an-assessment"></a>Co je součástí posouzení?

**Vlastnost** | **Podrobnosti**
--- | ---
**Cílové umístění** | Umístění Azure, do kterého chcete migrovat.<br/> Posouzení serveru aktuálně podporuje tyto cílové oblasti: Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Střed Indie, Střed USA, Čína – východ, Čína – sever, Východní Asie, Východní USA, východní USA 2, Německo – střed, Německo – jihovýchod, Japonsko – východ, Japonsko – západ, Jižní Korea, Korea – jih, Severní Střed USA, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Velká Británie – západ, US Gov – Arizona, US Gov – Texas, US Gov – Virginie, Středozápadní USA, Západní Evropa, Západní Indie, Západní USA a západní USA 2.
**Typ úložiště** | Tato vlastnost slouží k určení typu disků, na které chcete přejít v Azure. <br/><br/> U místních velikostí můžete určit typ cílového úložiště buď jako disky spravované na úrovni Premium, SSD úrovně Standard disky spravované nebo HDD úrovně Standard spravované disky. Pro určení velikosti na základě výkonu můžete určit typ cílového disku buď jako automatické disky spravované na úrovni Premium, HDD úrovně Standard disky spravované systémem nebo pro SSD úrovně Standard spravované disky.<br/><br/> Když zadáte typ úložiště jako automatický, doporučení na disku se provede na základě údajů o výkonu disků (IOPS a propustnost). Pokud zadáte typ úložiště jako Premium/Standard, vyhodnocování bude doporučit SKU disku v rámci vybraného typu úložiště. Pokud chcete dosáhnout smlouvy SLA pro virtuální počítače s jednou instancí 99,9%, můžete zadat typ úložiště jako disky spravované na úrovni Premium. Tím se zajistí, že se všechny disky v posouzení doporučují jako disky spravované na úrovni Premium. Poznámka: Azure Migrate podporuje pro posouzení migrace jenom spravované disky.
**Rezervované instance (RI)** | Tato vlastnost vám pomůže určit, jestli máte [rezervované instance](https://azure.microsoft.com/pricing/reserved-vm-instances/) v Azure, odhad nákladů v posouzení se pak provede s využitím slev vyhrazené na rezervované instance. Rezervované instance se momentálně podporují jenom pro nabídky s průběžnými platbami v Azure Migrate.
**Kritérium určení velikosti** | Kritérium, které se má použít ke správné velikosti virtuálních počítačů pro Azure. Virtuální počítače můžete buď měnit podle *výkonu* , nebo měnit jejich velikost *jako v*místním prostředí, aniž byste museli zvážit historii výkonu.
**Historie výkonu** | Doba, kterou je třeba zvážit při vyhodnocování dat výkonu počítačů. Tato vlastnost je k dispozici pouze v případě, že kritérium velikosti je *založeno na výkonu*.
**Percentilové využití** | Hodnota percentilu sady vzorků výkonu, která se má zohlednit při určování správné velikosti. Tato vlastnost je k dispozici pouze v případě, že je nastavena velikost *na základě výkonu*.
**Řada virtuálních počítačů** |     Můžete zadat řadu virtuálních počítačů, pro kterou chcete zvážit nastavení správné velikosti. Pokud máte například produkční prostředí, které neplánujete migrovat na virtuální počítače řady A-Series v Azure, můžete vyloučit řady-Series ze seznamu nebo řady a správná velikost se provádí pouze ve vybraných řadách.
**Faktor komfortu** | Vyhodnocování Azure Migrate serveru během posuzování považuje vyrovnávací paměť (faktor komfortu). Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry.
**Nabídka** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/), kterou máte zaregistrovanou. Azure Migrate odhadne náklady odpovídajícím způsobem.
**Měna** | Fakturační měna.
**Sleva (%)** | Jakákoli sleva pro konkrétní předplatné, kterou získáte nad rámec nabídky Azure.<br/> Výchozí nastavení je 0 %.
**Doba provozu virtuálního počítače** | Pokud vaše virtuální počítače nefungují nepřetržitě v Azure, můžete zadat dobu trvání (počet dní za měsíc a počet hodin za den), pro které by byly spuštěné, a odhad nákladů se provede odpovídajícím způsobem.<br/> Výchozí hodnota je 31 dní za měsíc a 24 hodin denně.
**Zvýhodněné hybridní využití Azure** | Určete, jestli máte program Software Assurance a máte nárok na [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. Výchozí hodnota je Yes (Ano).

## <a name="how-are-assessments-calculated"></a>Jak se počítají posouzení?

Posouzení při vyhodnocování Azure Migrate serveru se počítá pomocí metadat shromážděných na místních serverech. Výpočet vyhodnocení se provádí ve třech fázích; pro každý server se kalkulace vyhodnocení začíná analýzou vhodnosti Azure, za kterou následuje Změna velikosti a nakonec Odhad měsíčních nákladů. Server se přesune pouze do pozdější fáze, pokud předá předchozí. Například pokud server neuspěje v případě kontroly vhodnosti Azure, je označený jako nevhodný pro Azure a změna velikosti a oceňování se neprovádí pro stejné.

## <a name="azure-suitability-analysis"></a>Analýza vhodnosti pro Azure

Ne všechny počítače jsou vhodné pro spuštění v Azure. Azure Migrate Assessment Server vyhodnocuje u každého místního počítače vhodnost migrace do Azure a rozděluje hodnocené počítače do jedné z následujících kategorií vhodnosti:
- **Připraveno pro Azure** – počítač se dá migrovat tak, jak je, do Azure bez jakýchkoli změn. Spustí se v Azure s plnou podporou Azure.
- **Podmíněně připravené pro Azure** – počítač se může spustit v Azure, ale nemusí mít plnou podporu Azure. Například počítač se starší verzí operačního systému Windows Server se v Azure nepodporuje. Před migrací těchto počítačů do Azure musíte být opatrní a při provádění migrace dodržujte pokyny k nápravě navržené v posouzení, abyste opravili problémy s připraveností.
- **Nepřipraveno pro Azure** – počítač se nespustí v Azure. Pokud má například místní počítač disk o velikosti větší než 64 TB, nemůže být hostovaný v Azure. Před migrací do Azure je třeba postupovat podle pokynů k nápravě navržených v posouzení, abyste opravili problém připravenosti. Pro počítače, které jsou označené jako nepřipravené pro Azure, se neprovádí správná velikost a odhad nákladů.
- **Neznámá připravenost** – Azure Migrate nemohl najít připravenost počítače kvůli nedostatečným metadatům shromážděným z místního prostředí.

Vyhodnocování Azure Migrate serveru zkontroluje vlastnosti počítače a hostovaný operační systém a určí připravenost Azure na místním počítači.

### <a name="machine-properties"></a>Vlastnosti počítače

Posouzení serveru kontroluje následující vlastnosti místního virtuálního počítače, aby zjistil, jestli je možné ho spustit v Azure.

**Vlastnost** | **Podrobnosti** | **Stav připravenosti na Azure**
--- | --- | ---
**Typ spouštění** | Azure podporuje virtuální počítače s typem spouštění jako BIOS, nikoli UEFI. | Podmíněně připravený, pokud je typ spouštění UEFI.
**Jader** | Počet jader v počítačích musí být větší nebo roven maximálnímu počtu jader (128 jader) podporovaných pro virtuální počítač Azure.<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využité jádra k porovnání. Pokud je v nastavení hodnocení určen faktor komfortu, je počet využitých jader vynásoben faktorem pohodlí.<br/><br/> Pokud není k dispozici žádná historie výkonu, Azure Migrate používá přidělená jádra bez použití faktoru pohodlí. | Připraveno, pokud je omezení menší nebo rovno.
**Paměť** | Velikost paměti počítače musí být stejná nebo menší než maximální paměť (3892 GB v Azure M Series Standard_M128m&nbsp;<sup>2</sup>), která je povolená pro virtuální počítač Azure. [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využití paměti za využitou paměť k porovnání. Pokud je určen faktor komfortu, vyhodnotí se využitá paměť podle faktoru pohodlí.<br/><br/> Pokud není k dispozici žádná historie, je přidělená paměť použita bez použití faktoru pohodlí.<br/><br/> | V rámci omezení je připravený.
**Disk úložiště** | Přidělená velikost disku musí být 32 TB nebo menší.<br/><br/> Počet disků připojených k počítači musí být 65 nebo méně, včetně disku s operačním systémem. | V rámci omezení je připravený.
**Sítě** | K počítači musí být připojen 32 nebo méně síťových adaptérů. | V rámci omezení je připravený.

### <a name="guest-operating-system"></a>Hostovaný operační systém
Spolu s vlastnostmi virtuálního počítače Azure Migrate posouzení serveru vypadá na hostovaném operačním systému počítačů, aby bylo možné zjistit, jestli se dá spustit na Azure.

> [!NOTE]
> Pro virtuální počítače VMware Azure Migrate posouzení serveru používá operační systém zadaný pro virtuální počítač v vCenter Server k provedení analýzy hostovaného operačního systému. Pro virtuální počítače se systémem Linux běžící na VMware aktuálně neidentifikuje přesnou verzi jádra hostovaného operačního systému.

Následující logiku používá Azure Migrate posouzení serveru k identifikaci připravenosti na Azure podle operačního systému.

**Operační systém** | **Podrobnosti** | **Stav připravenosti na Azure**
--- | --- | ---
Windows Server 2016 & všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 R2 & všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 & všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2008 R2 se všemi službami SPs | Azure poskytuje plnou podporu.| Připraveno pro Azure
Windows Server 2008 (32 a 64-bit) | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2003, 2003 R2 | Tyto operační systémy prošly datem podpory a potřebují pro podporu v Azure [vlastní smlouvu o podpoře (CSA)](https://aka.ms/WSosstatement) . | Podmíněně připravené pro Azure zvažte upgrade operačního systému před migrací do Azure.
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Tyto operační systémy prošly datem podpory, počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure doporučujeme před migrací do Azure upgradovat operační systém.
Klient Windows 7, 8 a 10 | Azure poskytuje podporu [jenom pro předplatné sady Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Připraveno pro Azure s podmínkou
Stolní počítač s Windows 10 pro | Azure podporuje [práva hostování s více klienty.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Připraveno pro Azure s podmínkou
Windows Vista, XP Professional | Tyto operační systémy prošly datem podpory, počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure doporučujeme před migrací do Azure upgradovat operační systém.
Linux | Azure tyto [operační systémy pro Linux](../virtual-machines/linux/endorsed-distros.md)schválí. Jiné operační systémy Linux se můžou spustit v Azure, ale před migrací do Azure se doporučuje upgradovat operační systém na schválenou verzi. | Je připravený pro Azure, pokud je verze schválená.<br/><br/>Podmíněně připravený, pokud verze není schválená.
Jiné operační systémy<br/><br/> Například Oracle Solaris, Apple Mac OS atd., FreeBSD atd. | Azure tyto operační systémy neschvaluje. Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure doporučujeme nainstalovat podporovaný operační systém před migrací do Azure.  
Operační systém určený jako **jiný** v vCenter Server | Azure Migrate nemůže v tomto případě identifikovat operační systém. | Neznámá připravenost. Ujistěte se, že operační systém běžící v rámci virtuálního počítače je podporovaný v Azure.
32 – bitové operační systémy | Počítač se může spustit v Azure, ale Azure možná neposkytne plnou podporu. | Předem připravené na Azure, zvažte upgrade operačního systému počítače z 32 na 64 operačního systému na, než se migruje do Azure.

## <a name="sizing"></a>Velikost

Po označení počítače jako připraveného pro Azure provede posouzení serveru změnu velikosti, která zahrnuje identifikaci příslušného virtuálního počítače Azure a SKU disku pro místní virtuální počítač. Doporučení velikosti se liší v závislosti na zadaných vlastnostech posouzení.

- Pokud hodnocení používá určení velikosti *na základě výkonu*, Azure Migrate považuje historii výkonu počítače za účelem identifikace velikosti virtuálního počítače a typu disku v Azure. Tato metoda je užitečná hlavně v případě, že jste přestali využívat místní virtuální počítač, ale jeho využití je nízké a vy chcete ušetřit náklady v Azure na správnou velikost. Tato metoda vám pomůže optimalizovat velikosti během migrace.
- Pokud nechcete brát v úvahu údaje o výkonu pro určení velikosti virtuálních počítačů a chcete pořídit místní počítače jako do Azure, můžete určit kritérium změny velikosti jako *u místního* prostředí a vyhodnocení serveru pak bude na základě místního nastavení velikosti virtuálních počítačů. konfigurace bez zvážení dat o využití. Velikost disku se v tomto případě provede na základě typu úložiště, který zadáte ve vlastnostech posouzení (HDD úrovně Standard disky, SSD úrovně Standard disky nebo disky úrovně Premium).

### <a name="performance-based-sizing"></a>Změny velikosti na základě výkonu

Pro určení velikosti na základě výkonu Azure Migrate posouzení serveru začíná disky připojenými k virtuálnímu počítači, za kterými následuje síťové adaptéry, a pak mapuje SKU virtuálního počítače Azure na základě výpočetních požadavků místního virtuálního počítače. Zařízení Azure Migrate profiluje místní prostředí pro shromažďování údajů o výkonu procesoru, paměti, disků a síťového adaptéru.

**Shromažďování údajů o výkonu**

- V případě virtuálních počítačů VMware shromažďuje Azure Migrate zařízení v každém 20 sekundách ukázkový bod v reálném čase. pro virtuální počítače Hyper-V se v reálném čase shromáždí ukázkový bod v intervalu 30 sekund.
- Zařízení pak zahrne ukázkové body shromážděné každých 10 minut a pošle maximální hodnotu za posledních 10 minut pro Azure Migrate posouzení serveru.
- Hodnocení serveru Azure Migrate ukládá všechny 10 minutových vzorků za poslední měsíc a v závislosti na vlastnostech posouzení zadaných pro *historii výkonu* a *využití percentilu*identifikuje vhodný datový bod. který má být použit pro správnou velikost. Pokud je například historie výkonu nastavená na jeden den a procento využití je 95. percentil, použije se pro poslední den vzorové body o velikosti 10 minut, seřadí je vzestupně a vybere hodnotu percentilu 95. pro správnou velikost.
- Výše uvedená hodnota je pak vynásobena faktorem pohodlí, aby získala efektivní data o využití výkonu pro každou metriku (využití procesoru, využití paměti, IOPS disku (čtení a zápis), propustnost disku (čtení a zápis), propustnost sítě (v/v)). zařízení shromažďuje.
- Jakmile je hodnota efektivního využití zjištěna, výpočet velikosti, úložiště a sítě se provádí takto:

**Velikost úložiště**: Azure Migrate se pokusí mapovat všechny disky připojené k počítači na disk v Azure.

> [!NOTE]
> Azure Migrate: Vyhodnocování serveru podporuje pro posouzení jenom spravované disky.

  - K získání celkové propustnosti každého disku se přidají vstupně-výstupní operace čtení a zápisu disku, které mají za sekundu získat celkový počet požadavků IOPS, podobně čtení a propustnosti zápisu.
  - Pokud jste zadali typ úložiště jako automatický, na základě efektivních hodnot IOPS a propustnosti Azure Migrate posouzení serveru pak určí, jestli se má disk namapovat na standardní pevný disk, Standard SSD nebo disk Premium v Azure. Pokud je typ úložiště nastavený na HDD úrovně Standard/SSD/Premium, pokusí se najít SKU disku v rámci vybraného typu úložiště (disky HDD úrovně Standard/SSD/Premium).
  - Pokud server Assessment nenalezne disk s požadovaným počtem IOPS & propustnosti, označí počítač jako nevhodný pro Azure.
  - Pokud najde sadu vhodných disků, vybere ty, které podporují umístění zadané v nastavení posouzení.
  - Pokud je k dispozici více opravňujících disků, vybere jednu z nich s nejnižšími náklady.
  - Pokud údaje o výkonu pro všechny disky nejsou v nedostupném, při hledání standardního disku SSD v Azure se použije konfigurační data disku (velikost disku).

**Velikost sítě**: Azure Migrate vyhodnocování serveru se pokouší najít virtuální počítač Azure, který může podporovat počet síťových adaptérů připojených k místnímu počítači a výkon vyžadovaný těmito síťovými adaptéry.
    - Aby se dosáhlo efektivního výkonu sítě v místním VIRTUÁLNÍm počítači, vyhodnocování serveru agreguje data přenášená za sekundu (MB/s) na všech síťových adaptérech a uplatní faktor komfortu. Toto číslo se používá k vyhledání virtuálního počítače Azure, který může podporovat požadovaný výkon sítě.
    - Společně s výkonem sítě taky bere v úvahu, jestli virtuální počítač Azure může podporovat požadovaný počet síťových adaptérů.
    - Pokud nejsou k dispozici žádná data o výkonu sítě, je pro velikost virtuálního počítače považováno pouze počet síťových adaptérů.

**Výpočet velikosti**: Až se vypočítají požadavky na úložiště a síť, Azure Migrate posouzení serveru posuzuje požadavky na procesor a paměť, aby se v Azure vyhledala vhodná velikost virtuálního počítače.
    - Azure Migrate podíváme se na efektivní využité jádra a paměť a najděte vhodnou velikost virtuálního počítače v Azure.
    - Pokud se nenajde žádná vhodná velikost, je počítač označený jako nevhodný pro Azure.
    - Pokud se najde vhodná velikost, Azure Migrate použije výpočty úložiště a sítě. Pak u konečného doporučení velikosti virtuálního počítače aplikuje nastavení umístění a cenové úrovně.
    - Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.

### <a name="as-on-premises-sizing"></a>Podle velikosti v místním prostředí

Pokud používáte jako *místní*velikost, vyhodnocování serveru nebere v úvahu historii výkonu virtuálních počítačů a disků a v Azure PŘIDĚLUJE SKU virtuálního počítače na základě přidělené velikosti v místním prostředí. Podobně jako u velikosti disku se vyhledává typ úložiště určený ve vlastnostech posouzení (HDD úrovně Standard/SSD/Premium) a podle toho tento typ disku doporučí. Výchozí typ úložiště je prémiové disky.

## <a name="confidence-ratings"></a>Hodnocení spolehlivosti
Každé posouzení na základě výkonu v Azure Migrate je přidruženo k hodnocení spolehlivosti, které rozsah od 1 (nejnižší) po pět začíná (nejvyšší).
- Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení.
- Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti se nedá použít jako vyhodnocení místních hodnot.
- Pro určení velikosti na základě výkonu Azure Migrate potřeby posouzení serveru:
    - Data o využití procesoru a paměti virtuálního počítače.
    - Pro každý disk připojený k virtuálnímu počítači navíc potřebuje IOPS a propustnost disku.
    - Podobně jako u každého síťového adaptéru připojeného k virtuálnímu počítači potřebuje hodnocení spolehlivosti síťové I výstupní operace, aby bylo možné určit velikost na základě výkonu.
    - Pokud některý z výše uvedených čísel využití není v vCenter Server k dispozici, doporučení velikosti nemusí být spolehlivé.

Tady je poskytnuté hodnocení spolehlivosti posouzení v závislosti na procentu dostupných datových bodů:

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 Star
   81 až 100 % | 5 hvězdiček

### <a name="low-confidence-ratings"></a>Hodnocení nízké důvěry

K dispozici je několik důvodů, proč hodnocení může získat nízkou spolehlivost:

- Nevytvořili jste profil svého prostředí po dobu, po kterou vytváříte posouzení. Pokud například vytvoříte hodnocení s dobou trvání trvání nastavenou na jeden den, budete muset po spuštění zjišťování pro všechny datové body, které se mají shromáždit, počkat aspoň jeden den.
- Některé virtuální počítače se vypnuly během období, pro které se posouzení počítá. Pokud byly některé virtuální počítače po určitou dobu vypnuté, Azure Migrate posouzení serveru nebude shromažďovat údaje o výkonu pro tuto dobu.
- Některé virtuální počítače byly vytvořeny v době mezi obdobím, pro které se posouzení počítá. Pokud například vytvoříte vyhodnocení pro historii výkonu za poslední měsíc, ale některé virtuální počítače byly v prostředí vytvořeny pouze před týdnem, historie výkonu nových virtuálních počítačů nebude platit pro celou dobu trvání.

> [!NOTE]
> Pokud je hodnocení spolehlivosti nějakého posouzení nižší než pět hvězdiček, doporučujeme počkat aspoň jeden den, než zařízení profiluje prostředí, a pak posouzení přepočítat. Pokud to neuděláte, velikost na základě výkonu nemusí být spolehlivá a doporučujeme, abyste převedli posouzení na použití jako při změně velikosti v místním prostředí.

## <a name="monthly-cost-estimation"></a>Odhad měsíčních nákladů

Až se dokončí Změna velikosti doporučení, Azure Migrate vypočítá náklady na výpočetní prostředky a úložiště pro po migraci.

- **Náklady na výpočetní**výkon: Při použití doporučené velikosti virtuálního počítače Azure používá Azure Migrate k výpočtu měsíčních nákladů na virtuální počítač rozhraní API pro fakturaci.
    - Výpočet vezme v úvahu operační systém, Software Assurance, rezervované instance, dobu provozu virtuálního počítače, umístění a nastavení měny.
    - Agreguje náklady napříč všemi počítači pro výpočet celkových měsíčních výpočetních nákladů.
- **Náklady na úložiště**: Měsíční náklady na úložiště pro počítač se vypočtou pomocí agregace měsíčních nákladů na všechny disky připojené k počítači.
    - Hodnocení serveru Azure Migrate vypočítá celkové měsíční náklady na úložiště tím, že se agreguje náklady na úložiště pro všechny počítače.
    - V současné době Výpočet nebere v úvahu nabídky zadané v nastavení hodnocení.

Náklady se zobrazují v měně určené v nastavení hodnocení.


## <a name="next-steps"></a>Další postup

Vytvořte posouzení pro [virtuální počítače VMware](tutorial-assess-vmware.md) nebo [virtuální počítače Hyper-V](tutorial-assess-hyper-v.md).
