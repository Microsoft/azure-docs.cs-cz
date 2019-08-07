---
title: Výpočty pro vyhodnocení v Azure Migrate | Microsoft Docs
description: Poskytuje přehled výpočtů hodnocení ve službě Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 4511c42514a5399d41029b61297bd4c1b0b63d9a
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827547"
---
# <a name="assessment-calculations-in-azure-migrate"></a>Výpočty pro vyhodnocení v Azure Migrate

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh. Také sleduje vaše soukromé a veřejné cloudové instance do Azure. Centrum nabízí Azure Migrate nástroje pro posouzení a migraci a také nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Posuzování serveru je nástroj v Azure Migrate, který vyhodnocuje místní servery pro migraci do Azure. Tento článek poskytuje informace o tom, jak se vypočítávají vyhodnocení.

## <a name="whats-in-an-assessment"></a>Co je součástí posouzení?

**Vlastnost** | **Podrobnosti**
--- | ---
**Cílové umístění** | Určuje umístění Azure, do kterého chcete migrovat.<br/><br/>Posouzení serveru aktuálně podporuje tyto cílové oblasti: Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Střed Indie, Střed USA, Čína – východ, Čína – sever, Východní Asie, Východní USA, východní USA 2, Německo – střed, Německo – jihovýchod, Japonsko – východ, Japonsko – západ, Jižní Korea, Korea – jih, Severní Střed USA, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Velká Británie – západ, US Gov – Arizona, US Gov – Texas, US Gov – Virginie, Středozápadní USA, Západní Evropa, Západní Indie, Západní USA a západní USA 2.
**Typ úložiště** | Určuje typ disků, které chcete použít pro úložiště v Azure. <br/><br/> U místních velikostí můžete zadat typ cílového disku úložiště jako spravovaných, SSD úrovně Standard spravovaných na úrovni Premium nebo HDD úrovně Standard spravovaných. Pro určení velikosti na základě výkonu můžete zadat typ cílového disku úložiště jako automatické, spravované na úrovni Premium, HDD úrovně Standard spravovaný nebo SSD úrovně Standard. Pokud zadáte typ úložiště jako automatický, doporučení disku vychází z údajů o výkonu těchto disků: vstupně-výstupní operace za sekundu (IOPS) a propustnost. <br/><br/>Pokud jako typ úložiště zadáte Premium nebo Standard, vyhodnocování bude doporučit SKU disku v rámci vybraného typu úložiště. Pokud chcete dosáhnout smlouvy SLA pro virtuální počítače s jednou instancí 99,9%, můžete zadat typ úložiště jako disky spravované na úrovni Premium. Tím se zajistí, že se všechny disky v posouzení doporučují jako disky spravované na úrovni Premium. Poznámka: Azure Migrate podporuje pro posouzení migrace jenom spravované disky.
**Rezervované instance (RIs)** | Tato vlastnost vám pomůže určit [rezervované instance](https://azure.microsoft.com/pricing/reserved-vm-instances/) v Azure. Odhad nákladů v hodnocení pak vezme v úvahu slevy na rezervované instance. Služby RIs se momentálně podporují jenom pro nabídky s průběžnými platbami v Azure Migrate.
**Kritéria změny velikosti** | Nastaví kritéria, která se mají použít pro virtuální počítače s *pravou velikostí* pro Azure. Můžete se rozhodnout pro přizpůsobení velikosti virtuálních počítačů na *základě výkonu* nebo jejich velikosti *jako v* místním prostředí bez zvážení historie výkonu.
**Historie výkonu** | Nastaví dobu trvání, která se má vzít v úvahu při vyhodnocování dat výkonu počítačů. Tato vlastnost je platná pouze v případě, že kritéria změny velikosti jsou *založená na výkonu*.
**Percentilové využití** | Určuje hodnotu percentilu sady ukázek výkonu, která má být považována za správné určení velikosti. Tato vlastnost je platná pouze v případě, že je velikost na základě výkonu.
**Řada virtuálních počítačů** | Umožňuje zadat řadu virtuálních počítačů, které chcete zvážit pro správnou velikost. Pokud máte například produkční prostředí, které neplánujete migrovat na virtuální počítače řady A-Series v Azure, můžete vyloučit řady-Series ze seznamu nebo řady a v případě, že se pravá velikost provádí pouze ve vybraných řadách.
**Faktor komfortu** | Vyhodnocování Azure Migrate serveru během posuzování považuje vyrovnávací paměť (faktor komfortu). Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry.
**Nabídka** | Zobrazí [nabídku Azure](https://azure.microsoft.com/support/legal/offer-details/) , kterou jste si zaregistrovali. Azure Migrate odhadne náklady odpovídajícím způsobem.
**Měna** | Zobrazuje fakturační měnu vašeho účtu.
**Sleva (%)** | Obsahuje seznam všech slev specifických pro předplatné, které obdržíte nad nabídkou Azure. Výchozí nastavení je 0 %.
**Doba provozu virtuálního počítače** | Pokud vaše virtuální počítače nebudou běžet 24 hodin denně, 7 dní týden v Azure, můžete zadat dobu trvání (počet dní za měsíc a počet hodin za den), pro které budou spuštěné, a odhad nákladů se zpracuje odpovídajícím způsobem. Výchozí hodnota je 31 dní za měsíc a 24 hodin denně.
**Zvýhodněné hybridní využití Azure** | Určuje, jestli máte program Software Assurance a máte nárok na [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. Ve výchozím nastavení je Ano.

## <a name="how-are-assessments-calculated"></a>Jak se počítají posouzení?

Posouzení při vyhodnocování Azure Migrate serveru se počítá pomocí metadat shromážděných na místních serverech. Výpočet vyhodnocení se zpracovává ve třech fázích. U každého serveru začíná výpočet vyhodnocení s analýzou vhodnosti Azure, za nímž následuje Změna velikosti a nakonec měsíční odhad nákladů. Server se přesune do pozdější fáze pouze v případě, že předá předchozí. Pokud třeba server nepovede kontrolu vhodnosti Azure, je označený jako nevhodný pro Azure a u tohoto serveru se neprovádí Změna velikosti a nákladů.

## <a name="azure-suitability-analysis"></a>Analýza vhodnosti pro Azure

Ne všechny počítače jsou vhodné ke spuštění v Azure. Posouzení serveru vyhodnocuje každý místní počítač pro migraci do Azure. Každý vyhodnocený počítač se také přiřadí jedné z následujících kategorií vhodnosti:
- **Připraveno pro Azure**: Počítač se dá migrovat tak, jak je, do Azure bez jakýchkoli změn. Spustí se v Azure s plnou podporou Azure.
- **Podmíněně připravené pro Azure**: Počítač může začínat v Azure, ale nemusí mít plnou podporu Azure. Například počítač, na kterém běží starší verze Windows serveru, se v Azure nepodporuje. Než tyto počítače migrujete do Azure, musíte být opatrní a podle pokynů k nápravě navrhovaných v posouzení opravit problémy s připraveností.
- **Nepřipraveno pro Azure**: Počítač se v Azure nespustí. Pokud má například místní počítač disk s více než 64 terabajty (TB), není možné ho hostovat v Azure. Než počítač migrujete do Azure, musíte postupovat podle pokynů k nápravě navržených v posouzení, abyste opravili problém připravenosti. Pro počítače, které jsou označené jako nepřipravené pro Azure, se neprovádí správná velikost a odhad nákladů.
- **Připravenost**není známa: Azure Migrate nemohl určit připravenost počítače kvůli nedostatečným metadatům shromážděným z místního prostředí.

Posouzení serveru zkontroluje vlastnosti počítače a hostovaný operační systém a určí připravenost Azure na místním počítači.

### <a name="machine-properties"></a>Vlastnosti počítače

Posouzení serveru kontroluje následující vlastnosti místního virtuálního počítače, aby zjistil, jestli je možné ho spustit v Azure.

**Vlastnost** | **Podrobnosti** | **Stav připravenosti na Azure**
--- | --- | ---
**Typ spouštění** | Azure podporuje virtuální počítače s typem spouštění systému BIOS, nikoli UEFI. | Podmíněně připravený, pokud je typ spouštění UEFI.
**Jader** | Počet jader v počítačích musí být větší nebo roven maximálnímu počtu jader (128) podporovanému pro virtuální počítač Azure.<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využité jádra k porovnání. Pokud je v nastavení hodnocení určen faktor komfortu, je počet využitých jader vynásoben faktorem pohodlí.<br/><br/> Pokud není k dispozici žádná historie výkonu, Azure Migrate používá přidělená jádra bez použití faktoru pohodlí. | Připraveno, pokud je omezení menší nebo rovno.
**Paměť** | Velikost paměti počítače musí být stejná nebo menší než maximální paměť (3892 GB [GB] v Azure M Series Standard_M128m&nbsp;<sup>2</sup>), která je povolená pro virtuální počítač Azure. [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využití paměti za využitou paměť k porovnání. Pokud je určen faktor komfortu, vyhodnotí se využitá paměť podle faktoru pohodlí.<br/><br/> Pokud není k dispozici žádná historie, přidělená paměť se použije bez použití faktoru pohodlí.<br/><br/> | V rámci omezení je připravený.
**Disk úložiště** | Přidělená velikost disku musí být 32 TB nebo méně. I když Azure podporuje disky 64 TB s SSD úrovně Ultra disky, Azure Migrate: Posouzení serveru aktuálně kontroluje 32 TB jako omezení velikosti disku, protože ještě nepodporuje SSD úrovně Ultra. <br/><br/> Počet disků připojených k počítači musí být 65 nebo méně, včetně disku s operačním systémem. | V rámci omezení je připravený.
**Sítě** | Počítač musí mít k tomuto počítači připojenou 32 nebo méně síťových rozhraní (nic). | V rámci omezení je připravený.

### <a name="guest-operating-system"></a>Hostovaný operační systém
Posouzení serveru spolu s vlastnostmi virtuálního počítače podívá na hostovaný operační systém počítačů a určí, jestli se dá spustit na Azure.

> [!NOTE]
> U virtuálních počítačů VMware používá posouzení serveru operační systém, který je zadaný pro virtuální počítač v vCenter Server pro zpracování analýzy hostovaného operačního systému. Pro virtuální počítače se systémem Linux běžící na VMware aktuálně neidentifikuje přesnou verzi jádra hostovaného operačního systému.

Následující logiku používá posouzení serveru k identifikaci připravenosti na Azure podle operačního systému.

**Operační systém** | **Podrobnosti** | **Stav připravenosti na Azure**
--- | --- | ---
Windows Server 2016 & všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 R2 & všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 & všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2008 R2 se všemi službami SPs | Azure poskytuje plnou podporu.| Připraveno pro Azure
Windows Server 2008 (32 a 64-bit) | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2003, 2003 R2 | Tyto operační systémy prošly datem ukončení podpory a potřebují pro podporu v Azure [vlastní smlouvu o podpoře (CSA)](https://aka.ms/WSosstatement) . | Podmíněně připravené pro Azure. Před migrací do Azure zvažte možnost upgradovat operační systém.
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Tyto operační systémy prošly datem ukončení podpory. Počítač se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure. Před migrací do Azure doporučujeme upgradovat operační systém.
Klient Windows 7, 8 a 10 | Azure poskytuje podporu [jenom pro předplatné sady Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Připraveno pro Azure s podmínkou
Stolní počítač s Windows 10 pro | Azure podporuje [práva hostování s více klienty.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Připraveno pro Azure s podmínkou
Windows Vista, XP Professional | Tyto operační systémy prošly datem ukončení podpory. Počítač se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure. Před migrací do Azure doporučujeme upgradovat operační systém.
Linux | Azure tyto [operační systémy pro Linux](../virtual-machines/linux/endorsed-distros.md)schválí. Ostatní operační systémy Linux se můžou v Azure spustit, ale před migrací do Azure doporučujeme upgradovat operační systém na schválenou verzi. | Je připravený pro Azure, pokud je verze schválená.<br/><br/>Podmíněně připravený, pokud verze není schválená.
Jiné operační systémy<br/><br/> Například Oracle Solaris, Apple Mac OS atd., FreeBSD atd. | Azure tyto operační systémy neschvaluje. Počítač se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure. Před migrací do Azure doporučujeme nainstalovat podporovaný operační systém.  
Operační systém určený jako **jiný** v vCenter Server | Azure Migrate nemůže v tomto případě identifikovat operační systém. | Neznámá připravenost. Ujistěte se, že operační systém běžící v rámci virtuálního počítače je podporovaný v Azure.
32 – bitové operační systémy | Počítač se může v Azure spustit, ale Azure možná neposkytne plnou podporu. | Podmíněně připravené pro Azure. Před migrací do Azure zvažte možnost upgradovat operační systém počítače z 32 operačního systému na 64.

## <a name="sizing"></a>Velikost

Když je počítač označený jako připravený pro Azure, vyhodnocování serveru vytvoří doporučení pro změnu velikosti, což zahrnuje identifikaci příslušného virtuálního počítače Azure a jednotky disku pro místní virtuální počítač. Tato doporučení se liší v závislosti na zadaných vlastnostech posouzení.

- Pokud hodnocení používá určení velikosti *na základě výkonu*, Azure Migrate považuje historii výkonu počítače za účelem identifikace velikosti virtuálního počítače a typu disku v Azure. Tato metoda je užitečná hlavně v případě, že jste přestali využívat místní virtuální počítač, ale využití je nízké a vy chcete ušetřit náklady v Azure na správnou velikost. Tato metoda vám pomůže optimalizovat velikosti během migrace.
- Pokud nechcete brát v úvahu údaje o výkonu pro změny velikosti virtuálních počítačů a chcete pořizovat místní počítače jako do Azure, můžete nastavit kritéria pro změnu velikosti *jako místní*. Vyhodnocování serveru pak provede velikost virtuálních počítačů na základě místní konfigurace bez zvážení dat o využití. V takovém případě jsou aktivity velikosti disků založené na typu úložiště, který zadáte ve vlastnostech posouzení (HDD úrovně Standard, SSD úrovně Standard nebo prémiových discích).

### <a name="performance-based-sizing"></a>Změny velikosti na základě výkonu

Pro určení velikosti na základě výkonu začíná posouzení serveru disky připojenými k virtuálnímu počítači a potom síťové adaptéry. Pak namapuje SKU virtuálního počítače Azure na základě výpočetních požadavků místního virtuálního počítače. Zařízení Azure Migrate profiluje místní prostředí pro shromažďování údajů o výkonu procesoru, paměti, disků a síťového adaptéru.

**Postup shromažďování dat výkonu:**

1. Pro virtuální počítače VMware shromažďuje Azure Migrate zařízení ukázkový bod v reálném čase v intervalu 20 sekund. V případě virtuálních počítačů Hyper-V je ukázkový bod v reálném čase shromážděn v intervalu 30 sekund.
1. Zařízení zahrne vzorové body shromážděné každých 10 minut a pošle maximální hodnotu za posledních 10 minut do posouzení serveru.
1. Vyhodnocování serveru ukládá všechny ukázkové body 10 minut za poslední měsíc. V závislosti na vlastnostech posouzení zadaných pro *historii výkonu* a procento *využití*se pak určí vhodný datový bod, který se má použít pro správnou velikost. Například pokud je historie výkonu nastavena na 1 den a procento využití je 95. percentil, vyhodnocování serveru používá pro poslední den 10 minut, seřadí je ve vzestupném pořadí a vybere hodnotu percentilu 95. pro správnou velikost.
1. Tato hodnota je vynásobena faktorem komfortu, aby získala efektivní data o využití výkonu pro jednotlivé metriky (využití procesoru, využití paměti, IOPS disku (čtení a zápis), propustnost disku (čtení a zápis) a propustnost sítě (v/v). zařízení shromažďuje.

Po určení efektivní hodnoty využití se velikost úložiště, sítě a výpočetního prostředí zpracuje následujícím způsobem.

**Velikost úložiště**: Azure Migrate se pokusí mapovat všechny disky připojené k počítači na disk v Azure.

> [!NOTE]
> Vyhodnocování serveru Azure Migrate podporuje pro posouzení pouze spravované disky.

  - Posouzení serveru přidá IOPS čtení a zápisu disku, aby bylo možné získat celkový počet požadovaných IOPS. Podobně přidá hodnoty propustnosti čtení a zápisu k získání celkové propustnosti každého disku.
  - Pokud jste zadali typ úložiště jako automatický na základě platných hodnot IOPS a propustnosti, vyhodnocování serveru určí, jestli se má disk v Azure namapovat na standardní HDD, Standard SSD nebo disk Premium. Pokud je typ úložiště nastavený na HDD úrovně Standard/SSD/Premium, vyhodnocování serveru se pokusí najít SKU disku v rámci vybraného typu úložiště (disky HDD úrovně Standard/SSD/Premium).
  - Pokud server Assessment nenalezne disk s požadovanými vstupně-výstupními operacemi a propustností, označí počítač jako nevhodný pro Azure.
  - Pokud posouzení serveru najde sadu vhodných disků, vybere disky, které podporují umístění zadané v nastavení posouzení.
  - Pokud je k dispozici více opravňujících disků, vybírá Server Assessment disk s nejnižšími náklady.
  - Pokud nejsou k dispozici údaje o výkonu pro jakýkoli disk, k nalezení standardního disku SSD v Azure se použije konfigurační data disku (velikost disku).

**Velikost sítě**: Vyhodnocování serveru se pokouší najít virtuální počítač Azure, který může podporovat počet síťových adaptérů připojených k místnímu počítači a výkon vyžadovaný těmito síťovými adaptéry.
- Aby se dosáhlo efektivního výkonu sítě v místním VIRTUÁLNÍm počítači, vyhodnocování serveru agreguje data přenášená za sekundu (MB/s) na všech síťových adaptérech a uplatní faktor komfortu. Pomocí tohoto čísla vyhledá virtuální počítač Azure, který může podporovat požadovaný výkon sítě.
- Společně s výkonem sítě taky posouzení serveru bere v úvahu, jestli virtuální počítač Azure může podporovat požadovaný počet síťových adaptérů.
- Pokud nejsou k dispozici žádná data o výkonu sítě, posouzení serveru považuje pouze počet síťových adaptérů pro velikost virtuálního počítače.

**Výpočet velikosti**: Jakmile vypočítá požadavky na úložiště a síť, vyhodnotí se požadavky na procesor a paměť, aby se v Azure vyhledala vhodná velikost virtuálního počítače.
- Azure Migrate podíváme se na efektivní využité jádra a paměť a najděte vhodnou velikost virtuálního počítače v Azure.
- Pokud se nenajde žádná vhodná velikost, je počítač označený jako nevhodný pro Azure.
- Pokud se najde vhodná velikost, Azure Migrate použije výpočty úložiště a sítě. Pak použije nastavení umístění a cenové úrovně pro konečné doporučení velikosti virtuálního počítače.
- Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.

### <a name="as-on-premises-sizing"></a>Podle velikosti v místním prostředí

Pokud používáte *jako místní velikost*, posouzení serveru nebere v úvahu historii výkonu virtuálních počítačů a disků. Místo toho přiděluje SKU virtuálního počítače v Azure na základě přidělené velikosti v místním prostředí. Podobně jako u velikosti disků se posouzení serveru vyhledá v typu úložiště, který je určený ve vlastnostech posouzení (HDD úrovně Standard/SSD/Premium), a podle toho doporučí typ disku. Výchozí typ úložiště je prémiové disky.

## <a name="confidence-ratings"></a>Hodnocení spolehlivosti
Každé posouzení na základě výkonu v Azure Migrate je přidruženo k hodnocení spolehlivosti, které je v rozsahu od jednoho (nejnižší) po pět hvězdiček (nejvyšší).
- Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení.
- Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti není použitelné pro vyhodnocení *místních* hodnot.
- Pro určení velikosti na základě výkonu, požadavky na vyhodnocení serveru:
    - Data o využití procesoru a paměti virtuálního počítače.
    - IOPS disku a data propustnosti pro každý disk připojený k virtuálnímu počítači.
    - I/O sítě pro zpracování velikosti pro každý síťový adaptér připojený k virtuálnímu počítači na základě výkonu.

   Pokud některá z těchto čísel využití nejsou v vCenter Server k dispozici, doporučení velikosti nemusí být spolehlivé.

V závislosti na procentu dostupných datových bodů se hodnocení spolehlivosti pro posouzení provede následujícím způsobem.

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0-20% | 1 hvězda
   21-40% | 2 hvězdičky
   41-60% | 3 hvězdičky
   61-80% | 4 hvězdičky
   81-100% | 5 hvězdiček

### <a name="low-confidence-ratings"></a>Hodnocení nízké důvěry

Tady je několik důvodů, proč hodnocení může získat nízkou spolehlivost:

- Nevytvořili jste profil svého prostředí po dobu, po kterou vytváříte posouzení. Pokud například vytvoříte hodnocení s trváním výkonu nastaveným na jeden den, musíte po spuštění zjišťování pro všechny datové body, které se mají shromáždit, počkat aspoň jeden den.
- Některé virtuální počítače se vypnuly během období, pro které se hodnocení vypočítalo. Pokud jsou některé virtuální počítače po určitou dobu vypnuté, vyhodnocování serveru nemůže shromažďovat data o výkonu pro tuto dobu.
- Některé virtuální počítače byly vytvořeny během období, pro které bylo hodnocení vypočítáno. Pokud jste například vytvořili vyhodnocení pro historii výkonu za poslední měsíc, ale některé virtuální počítače byly vytvořeny pouze před týdnem, historie výkonu nových virtuálních počítačů nebude po celou dobu trvání k dispozici.

> [!NOTE]
> Pokud je hodnocení spolehlivosti nějakého posouzení menší než pět hvězdiček, doporučujeme, abyste počkali aspoň jeden den, než zařízení profiluje prostředí, a pak posouzení přepočítá. Pokud to neuděláte, velikost na základě výkonu nemusí být spolehlivá. V takovém případě doporučujeme, abyste přepnuli posouzení na místní nastavení velikosti.

## <a name="monthly-cost-estimation"></a>Odhad měsíčních nákladů

Až se dokončí Změna velikosti doporučení, Azure Migrate vypočítá náklady na výpočetní prostředky a úložiště pro po migraci.

- **Náklady na výpočetní**výkon: Při použití doporučené velikosti virtuálního počítače Azure používá Azure Migrate k výpočtu měsíčních nákladů na virtuální počítač rozhraní API pro fakturaci.
    - Výpočet vezme v úvahu operační systém, Software Assurance, rezervované instance, dobu provozu virtuálního počítače, umístění a nastavení měny.
    - Agreguje náklady napříč všemi počítači za účelem výpočtu celkových měsíčních výpočetních nákladů.
- **Náklady na úložiště**: Měsíční náklady na úložiště pro počítač se počítají pomocí agregace měsíčních nákladů na všechny disky připojené k počítači, a to takto:
    - Posouzení serveru vypočítá celkové měsíční náklady na úložiště tím, že agreguje náklady na úložiště pro všechny počítače.
    - V současné době Výpočet nebere v úvahu nabídky uvedené v nastavení hodnocení.

Náklady se zobrazují v měně určené v nastavení hodnocení.


## <a name="next-steps"></a>Další postup

Vytvořte posouzení pro [virtuální počítače VMware](tutorial-assess-vmware.md) nebo [virtuální počítače Hyper-V](tutorial-assess-hyper-v.md).
