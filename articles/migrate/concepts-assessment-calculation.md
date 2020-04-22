---
title: Hodnocení v azure migrate server hodnocení
description: Informace o hodnoceních v Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2f76ea5f195be2914cdcdb4de9e93af38504d66e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769927"
---
# <a name="assessments-in-azure-migrate-server-assessment"></a>Hodnocení v Azure Migrate: Hodnocení serveru

Tento článek obsahuje přehled hodnocení v nástroji [Azure Migrate: Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Nástroj může posoudit místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery pro migraci do Azure.

## <a name="whats-an-assessment"></a>Co je to hodnocení?

Hodnocení pomocí nástroje Hodnocení serveru měří připravenost a odhaduje účinek migrace místních serverů do Azure.

> [!NOTE]
> Ve službě Azure Government zkontrolujte podporovaná umístění [cílového](migrate-support-matrix.md#supported-geographies-azure-government) hodnocení. Všimněte si, že doporučení velikosti virtuálních počítače v hodnocení bude používat řady virtuálních počítače speciálně pro oblasti Government Cloud. [Další informace](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) o typech virtuálních počítačů.

## <a name="types-of-assessments"></a>Druhy hodnocení

Hodnocení, která vytvoříte pomocí vyhodnocení serveru, jsou snímek dat v čase. Vyhodnocení serveru poskytuje dva typy hodnocení.

**Typ posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Hodnocení, která doporučení vyhovují na základě shromážděných údajů o výkonnosti | Doporučení velikosti virtuálního počítače je založené na datech využití procesoru a paměti RAM.<br/><br/> Doporučení typu disku je založeno na vstupních a výstupních operacích za sekundu (IOPS) a propustnosti místních disků. Typy disků jsou disky Azure Standard HDD, Azure Standard SSD a Disky Azure Premium.
**As-je v místním prostředí** | Hodnocení, která nepoužívají údaje o výkonu k doporučením | Doporučení velikosti virtuálního počítače je založené na velikosti místního virtuálního počítače.<br/><br> Doporučený typ disku je založen na vybraném typu úložiště pro posouzení.

## <a name="how-do-i-run-an-assessment"></a>Jak spustím hodnocení?

Existuje několik způsobů, jak spustit hodnocení.

- Vyhodnoťte počítače pomocí metadat serveru shromážděných zjednodušenou službou Azure Migrate. Přístroj zjišťuje místní stroje. Potom odešle metadata počítače a data o výkonu do Migrace Azure.
- Vyhodnoťte počítače pomocí metadat serveru, která jsou importována ve formátu CSV (csv) oddělených čárkami.

## <a name="how-do-i-assess-with-the-appliance"></a>Jak hodnotím s přístrojem?

Pokud nasazujete zařízení Azure Migrate ke zjišťování místních serverů, postupujte takto:

1. Nastavte Azure a místní prostředí pro práci s vyhodnocením serveru.
1. Pro první hodnocení vytvořte projekt Azure a přidejte do něj nástroj pro vyhodnocení serveru.
1. Nasaďte zjednodušené zařízení Azure Migrate. Zařízení průběžně zjišťuje místní počítače a odesílá metadata a údaje o výkonu počítače do Migrace Azure. Nasazení zařízení jako virtuálnípočítač nebo fyzického počítače. Na počítače, které chcete posoudit, není nutné instalovat nic.

Po spuštění zjišťování stroje můžete shromáždit stroje, které chcete posoudit do skupiny, a spustit hodnocení pro skupinu.

Postupujte podle našich výukových programů pro [VMware](tutorial-prepare-vmware.md), [Hyper-V](tutorial-prepare-hyper-v.md)nebo [fyzické servery](tutorial-prepare-physical.md) vyzkoušet tyto kroky.

## <a name="how-do-i-assess-with-imported-data"></a>Jak hodnotím s importovanými daty?

Pokud vyhodnocujete servery pomocí souboru CSV, nepotřebujete zařízení. Místo toho proveďte následující kroky:

1. Nastavte Azure pro práci s vyhodnocením serveru.
1. Pro první hodnocení vytvořte projekt Azure a přidejte do něj nástroj pro vyhodnocení serveru.
1. Stáhněte si šablonu CSV a přidejte do ní data serveru.
1. Importujte šablonu do vyhodnocení serveru.
1. Objevte servery přidané s importem, shromážděte je do skupiny a spusťte hodnocení pro skupinu.

## <a name="what-data-does-the-appliance-collect"></a>Jaká data přístroj shromažďuje?

Pokud používáte zařízení Azure Migrate pro posouzení, přečtěte si informace o metadatech a údajích o výkonu shromážděných pro [společnosti VMware](migrate-appliance.md#collected-data---vmware) a [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Jak zařízení vypočítává údaje o výkonu?

Pokud zařízení používáte ke zjišťování, shromažďuje data o výkonu pro nastavení výpočetních prostředků pomocí následujících kroků:

1. Přístroj shromažďuje bod vzorku v reálném čase.

    - **Virtuální měna VMware**: Ukázkový bod se shromažďuje každých 20 sekund.
    - **Virtuální aplikace Hyper-V**: Bod vzorkování se shromažďuje každých 30 sekund.
    - **Fyzické servery**: Každých pět minut se shromažďuje ukázkový bod.

1. Zařízení kombinuje vzorkovací body a každých 10 minut vytváří jeden datový bod. Chcete-li vytvořit datový bod, zařízení vybere hodnoty špičky ze všech vzorků. Potom odešle datový bod do Azure.
1. Server Assessment ukládá všechny 10minutové datové body za poslední měsíc.
1. Při vytváření hodnocení, vyhodnocení serveru identifikuje příslušný datový bod použít pro rightsizing. Identifikace je založena na hodnotách percentilu pro *historii výkonu* a *využití percentilu*.

    - Pokud je například historie výkonu jeden týden a využití percentilu je 95 percentil, vyhodnocení serveru seřadí 10minutové ukázkové body za poslední týden. Třídí je ve vzestupném pořadí a vybere 95.
    - Hodnota 95. percentilu zajišťuje, že ignorujete všechny odlehlé hodnoty, které mohou být zahrnuty, pokud jste vybrali 99.
    - Pokud chcete vybrat využití ve špičce pro období a nechcete zmeškat žádné odlehlé hodnoty, vyberte 99 percentil pro využití percentilu.

1. Tato hodnota se vynásobí faktorem pohodlí, aby se získaly údaje o efektivním využití výkonu pro tyto metriky, které zařízení shromažďuje:

    - Využití procesoru
    - Využití paměti RAM
    - VOPS disku (čtení a zápis)
    - Propustnost disku (čtení a zápis)
    - Propustnost sítě (dovnitř a ven)

## <a name="how-are-assessments-calculated"></a>Jak se hodnocení počítají?

Vyhodnocení serveru používá metadata a údaje o výkonu místních počítačů k výpočtu hodnocení. Pokud nasadíte zařízení Azure Migrate, assessment používá data, která zařízení shromažďuje. Ale pokud spustíte hodnocení importované pomocí souboru CSV, zadáte metadata pro výpočet.

Výpočty se vyskytují v těchto třech fázích:

1. **Výpočet připravenosti Azure**: Vyhodnoťte, jestli jsou počítače vhodné pro migraci do Azure.
1. **Výpočet doporučení pro velikost**: Odhadujte výpočetní, úložnou a síťovou velikost.
1. **Výpočet měsíčních nákladů**: Vypočítejte odhadované měsíční náklady na výpočetní výkon a úložiště pro spouštění počítačů v Azure po migraci.

Výpočty jsou v předchozím pořadí. Strojový server se přesune do pozdější fáze pouze v případě, že projde předchozí. Například pokud server selže fáze připravenosti Azure, je označen jako nevhodné pro Azure. Pro tento server se neprovádí výpočet velikosti a nákladů.

## <a name="whats-in-an-assessment"></a>Co je součástí posouzení?

Co je zahrnuto v hodnocení v hodnocení serveru:

Vlastnost | Podrobnosti
--- | ---
**Cílové umístění** | Umístění, do kterého chcete migrovat. Server Assessment aktuálně podporuje tyto cílové oblasti Azure:<br/><br/> Austrálie – východ, Austrálie – jihovýchod, Brazílie jih, Kanada – střed, Kanada – východ, střední Indie, střední USA, Čína – východ, Čína – sever, východní Asie, východní USA, východní USA 2, Německo – střed, Německo – severovýchod, Japonsko – východ, Japonsko – západ, Korea – jih, severní střed USA, severní Evropa, jižní střed USA, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Velká Británie – západ, americká vláda Arizona, USA– Usa– Texas, usa – vláda , Západní střed USA, Západní Evropa, Západní Indie, Západní USA a Západní USA 2.
**Cílový disk úložiště (velikost stejně jako velikost)** | Typ disku, který se má použít pro úložiště v Azure. <br/><br/> Zadejte cílový disk úložiště jako spravovaný standardem, spravovanou standardním spojem SSD nebo standardní spravovanou hdd.
**Cílový disk úložiště (velikost založená na výkonu)** | Určuje typ cílového úložného disku jako automatického, spravovaného premium, spravovaného standardním pevným diskem nebo standardního spravovaného disku SSD.<br/><br/> **Automatické**: Doporučení disku je založeno na údajích o výkonu disků, což znamená vazníky a propustnost.<br/><br/>**Premium nebo Standard**: Hodnocení doporučuje disk skladovou položku v rámci vybraného typu úložiště.<br/><br/> Pokud chcete smlouvu o úrovni služeb virtuálních počítačů s jednou instancí (SLA) 99,9 %, zvažte použití disků spravovaných službou Premium. Toto použití zajišťuje, že všechny disky v hodnocení jsou doporučeny jako disky spravované službou Premium.<br/><br/> Azure Migrate podporuje jenom spravované disky pro vyhodnocení migrace.
**Instance rezervovaných virtuálních strojů Azure** | Určuje [rezervované instance](https://azure.microsoft.com/pricing/reserved-vm-instances/) tak, aby odhady nákladů v posouzení je vzaly v úvahu.<br/><br/> Azure Migrate aktuálně podporuje azure reserved vm instance jenom pro nabídky průběžných plateb.
**Kritéria pro velikost** | Používá se k rightsize virtuálního počítače Azure.<br/><br/> Použití velikosti podle velikosti nebo velikosti založené na výkonu.
**Historie výkonu** | Používá se s velikosti založené na výkonu. Historie výkonu určuje dobu trvání používanou při vyhodnocování dat o výkonu.
**Percentilové využití** | Používá se s velikosti založené na výkonu. Využití percentilu určuje hodnotu percentilu vzorku výkonu použitého pro rightsizing.
**Řada virtuálních počítačů** | Řada virtuálních počítačů Azure, které chcete zvážit pro rightsizing. Například pokud nemáte produkční prostředí, které potřebuje virtuální počítače řady A v Azure, můžete vyloučit řady A ze seznamu řad.
**Faktor komfortu** | Vyrovnávací paměť použitá během hodnocení. Používá se pro data procesoru, paměti RAM, disku a využití sítě pro virtuální počítače. To účty pro problémy, jako je sezónní využití, krátká historie výkonu a pravděpodobně zvyšuje budoucí využití.<br/><br/> Například 10jádrový virtuální virtuální ms s 20 % využití obvykle vede k virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu provozu se dvěma jádry. S faktorem pohodlí 2.0 je výsledkem virtuální virtuální virtuální měna se čtyřmi jádry.
**Nabídka** | [Nabídka Azure,](https://azure.microsoft.com/support/legal/offer-details/) ve které jste zaregistrovaní. Vyhodnocení serveru odhaduje náklady na tuto nabídku.
**Měna** | Fakturační měna pro váš účet.
**Sleva (%)** | Všechny slevy specifické pro předplatné, které obdržíte nad nabídku Azure. Výchozí nastavení je 0 %.
**Doba provozu virtuálního počítače** | Doba trvání ve dnech za měsíc a hodiny denně pro virtuální počítače Azure, které nebudou běžet nepřetržitě. Odhady nákladů jsou založeny na této době trvání.<br/><br/> Výchozí hodnoty jsou 31 dní v měsíci a 24 hodin denně.
**Zvýhodněné hybridní využití Azure** | Určuje, zda máte jistotu softwaru a máte nárok na [hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud má nastavení výchozí hodnotu Ano, ceny Azure pro jiné operační systémy než Windows se považují za virtuální počítače s Windows.

[Projděte si osvědčené postupy](best-practices-assessment.md) pro vytvoření hodnocení pomocí vyhodnocení serveru.

## <a name="calculate-readiness"></a>Vypočítat připravenost

Ne všechny počítače jsou vhodné pro spuštění v Azure. Vyhodnocení serveru vyhodnotí všechny místní počítače a přiřadí jim kategorii připravenosti.

- **Připraveno pro Azure**: Počítač lze migrovat jako je do Azure bez jakýchkoli změn. Spustí se v Azure s plnou podporou Azure.
- **Podmíněně připravený pro Azure**: Počítač se může spustit v Azure, ale nemusí mít plnou podporu Azure. Azure například nepodporuje počítač se starou verzí Windows Serveru. Před migrací těchto počítačů do Azure musíte být opatrní. Chcete-li vyřešit případné problémy s připraveností, postupujte podle pokynů pro nápravu, které navrhuje posouzení.
- **Není připraven pro Azure**: počítač se nespustí v Azure. Například pokud disk místního počítače ukládá více než 64 TB, Azure nemůže hostovat počítač. Chcete-li problém vyřešit před migrací, postupujte podle pokynů pro nápravu.
- **Připravenost neznámý**: Azure Migrate nelze určit připravenost počítače z důvodu nedostatečné metadata.

Chcete-li vypočítat připravenost, vyhodnocení serveru zkontroluje vlastnosti počítače a nastavení operačního systému shrnuté v následujících tabulkách.

### <a name="machine-properties"></a>Vlastnosti stroje

Vyhodnocení serveru zkontroluje následující vlastnosti místního virtuálního počítače k určení, zda může běžet v Azure.

Vlastnost | Podrobnosti | Stav připravenosti Azure
--- | --- | ---
**Typ spouštění** | Azure podporuje virtuální počítače s typem spouštění systému BIOS, nikoli uefi. | Podmíněně připraven, pokud je typ spouštění UEFI
**Cores** | Každý počítač nesmí mít více než 128 jader, což je maximální počet, který virtuální počítač Azure podporuje.<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využitá jádra pro porovnání. Pokud nastavení hodnocení určuje faktor komfortu, počet využitých jader se vynásobí faktorem komfortu.<br/><br/> Pokud neexistuje žádná historie výkonu, Azure Migrate používá přidělená jádra bez použití faktoru pohodlí. | Připraveno, pokud je počet jader v mezích
**Ram** | Každý počítač nesmí mít více než 3 892 GB paměti RAM, což&nbsp;je maximální velikost, kterou podporuje řada Azure M Standard_M128m<sup>2</sup> virtuálnípočítače. [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využitou paměť RAM pro porovnání. Pokud je specifikován faktor pohodlí, využitá paměť RAM se vynásobí faktorem komfortu.<br/><br/> Pokud neexistuje žádná historie, přidělená paměť RAM se používá bez použití faktoru pohodlí.<br/><br/> | Připraveno, pokud je velikost paměti RAM v mezích
**Disk úložiště** | Přidělená velikost disku nesmí být větší než 32 TB. Přestože Azure podporuje disky o velikosti disku o velikosti disku o velikosti disku o velikosti disku o velikosti disku o velikosti disku o velikosti disku, azure ssd disky, Azure Migrate: Server Assessment aktuálně kontroluje 32 TB jako limit velikosti disku, protože ještě nepodporuje Ultra SSD. <br/><br/> Počet disků připojených k počítači, včetně disku operačního systému, musí být 65 nebo méně. | Připraveno, pokud jsou velikost a číslo disku v mezích
**Sítě** | K počítači nesmí být připojeno více než 32 síťových rozhraní. | Připraveno, pokud je počet nic v mezích

### <a name="guest-operating-system"></a>Hostovaný operační systém

Spolu s kontrolou vlastností virtuálních počítače, vyhodnocení serveru se dívá na hostovaný operační systém počítače k určení, zda může běžet v Azure.

> [!NOTE]
> Pro zpracování analýzy hosta pro virtuální servery VMware používá vyhodnocení serveru operační systém určený pro virtuální ho virtuálního připojení na serveru vCenter. U virtuálních počítačů s Linuxem spuštěných ve službě VMware server Assessment aktuálně neidentifikuje verzi jádra hostovaného operačního systému.

Vyhodnocení serveru používá následující logiku k identifikaci připravenosti Azure na základě operačního systému:

**Operační systém** | **Podrobnosti** | **Stav připravenosti Azure**
--- | --- | ---
Windows Server 2016 a všechny sps | Azure poskytuje plnou podporu. | Připraveno pro Azure.
Windows Server 2012 R2 a všechny sp | Azure poskytuje plnou podporu. | Připraveno pro Azure.
Windows Server 2012 a všechny sps | Azure poskytuje plnou podporu. | Připraveno pro Azure.
Windows Server 2008 R2 se všemi sp | Azure poskytuje plnou podporu.| Připraveno pro Azure.
Windows Server 2008 (32bitový a 64bitový) | Azure poskytuje plnou podporu. | Připraveno pro Azure.
Systémy Windows Server 2003 a Windows Server 2003 R2 | Tyto operační systémy prošly datem ukončení podpory a potřebují [vlastní smlouvu o podpoře (CSA)](https://aka.ms/WSosstatement) pro podporu v Azure. | Podmíněně připraven pro Azure. Před migrací do Azure zvažte upgrade operačního systému.
Systémy Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3.1 a MS-DOS | Tyto operační systémy prošly datem ukončení podpory. Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního serveru. | Podmíněně připraven pro Azure. Doporučujeme upgradovat operační systém před migrací do Azure.
Windows 7, Windows 8 a Windows 10 | Azure poskytuje podporu jenom s [předplatným Visual Studia.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Podmíněně připraven pro Azure.
Windows 10 Pro | Azure poskytuje podporu s [víceklientských práv hostingu.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Podmíněně připraven pro Azure.
Systémy Windows Vista a Windows XP Professional | Tyto operační systémy prošly datem ukončení podpory. Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního serveru. | Podmíněně připraven pro Azure. Doporučujeme upgradovat operační systém před migrací do Azure.
Linux | Podívejte se na [operační systémy Linuxu,](../virtual-machines/linux/endorsed-distros.md) které Azure podporuje. Ostatní operační systémy Linuxu se mohou spustit v Azure. Ale doporučujeme upgradovat operační systém na schválenou verzi před migrací do Azure. | Připraveno pro Azure, pokud je verze schválena.<br/><br/>Podmíněně připraven, pokud verze není schválena.
Další operační systémy jako Oracle Solaris, Apple macOS a FreeBSD | Azure tyto operační systémy neschvaluje. Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního serveru. | Podmíněně připraven pro Azure. Doporučujeme nainstalovat podporovaný operační systém před migrací do Azure.  
Operační systém byl zadán jako **Jiný** na serveru vCenter | Azure Migrate nemůže v tomto případě identifikovat operační systém. | Neznámá připravenost. Ujistěte se, že Azure podporuje operační hod, který běží uvnitř virtuálního počítače.
32bitové operační systémy | Počítač se může spustit v Azure, ale Azure nemusí poskytovat plnou podporu. | Podmíněně připraven pro Azure. Před migrací do Azure zvažte upgrade na 64bitový operační systém.

## <a name="calculating-sizing"></a>Výpočet velikosti

Po zařízení je označen jako připravený pro Azure, vyhodnocení serveru umožňuje doporučení pro velikost. Tato doporučení identifikují virtuální počítač Azure a skladovou položku disku. Výpočty velikosti závisí na tom, zda používáte místní velikosti nebo velikost i na základě výkonu.

### <a name="calculate-sizing-as-is-on-premises"></a>Výpočet velikosti (místní)

 Pokud používáte jako místní velikosti, hodnocení serveru nebere v úvahu historii výkonu virtuálních počítačů a disků.

- **Velikost výpočetních prostředků**: Vyhodnocení serveru přiděluje skladovou jednotku virtuálního počítače Azure na základě velikosti přidělené místně.
- **Velikost úložiště a disku**: Vyhodnocení serveru se zabývá typem úložiště zadaným ve vlastnostech vyhodnocení a doporučuje příslušný typ disku. Možné typy úložišť jsou Standardní HDD, Standardní SSD a Premium. Výchozí typ úložiště je Premium.
- **Velikost sítě**: Vyhodnocení serveru bere v úvahu síťový adaptér v místním počítači.

### <a name="calculate-sizing-performance-based"></a>Vypočítat velikost (na základě výkonu)

Pokud používáte velikost na základě výkonu, vyhodnocení serveru provede doporučení pro měření velikosti následujícím způsobem:

- Vyhodnocení serveru bere v úvahu historii výkonu počítače k identifikaci velikosti virtuálního počítače a typu disku v Azure.
- Pokud importujete servery pomocí souboru CSV, budou použity zadané hodnoty. Tato metoda je užitečná zejména v případě, že jste přemístili místní počítač, využití je nízké a chcete velikost virtuálního počítače Azure správně ušetřit náklady.
- Pokud nechcete používat data o výkonu, obnovte kritéria velikosti tak, aby byla místní, jak je popsáno v předchozí části.

#### <a name="calculate-storage-sizing"></a>Výpočet velikosti úložiště

Pro velikost úložiště Azure Migrate se pokusí mapovat každý disk, který je připojený k počítači, na disk Azure. Dimenzování funguje takto:

1. Vyhodnocení serveru přidá čtení a zápis viop disku získat celkový požadovaný vipos. Podobně přidá hodnoty propustnosti pro čtení a zápisu, aby získal celkovou propustnost každého disku.
1. Pokud jste typ úložiště zadali jako automatický, vybraný typ je založen na efektivních hodnotách VOPS a propustnosti. Server Assessment určuje, zda se má disk v Azure namapovat na disk Standard HDD, Standard SSD nebo Premium. Pokud je typ úložiště nastaven na jeden z těchto typů disků, pokusí se vyhodnocení serveru najít skladovou položku disku v rámci vybraného typu úložiště.
1. Disky jsou vybrány takto:
    - Pokud server Assessment nemůže najít disk s požadovanými viopy a propustnost, označí počítač jako nevhodný pro Azure.
    - Pokud vyhodnocení serveru najde sadu vhodných disků, vybere disky, které podporují umístění zadané v nastavení hodnocení.
    - Pokud existuje více způsobilých disků, vyhodnocení serveru vybere disk s nejnižšími náklady.
    - Pokud nejsou k dispozici údaje o výkonu pro libovolný disk, velikost konfiguračního disku se používá k vyhledání standardního disku SSD v Azure.

#### <a name="calculate-network-sizing"></a>Vypočítat velikost sítě

Vyhodnocení serveru se pokusí najít virtuální počítač Azure, který podporuje počet a požadovaný výkon síťových adaptérů připojených k místnímu počítači.

- Chcete-li získat efektivní výkon sítě místního virtuálního počítače, vyhodnocení serveru agreguje přenosdat z počítače (síť out) přes všechny síťové adaptéry. To pak platí faktor pohodlí. Výsledná hodnota používá k vyhledání virtuálního počítače Azure, který podporuje požadovaný výkon sítě.
- Spolu s výkonem sítě, vyhodnocení serveru také zvažuje, zda virtuální počítač Azure podporuje požadovaný počet síťových adaptérů.
- Pokud data o výkonu sítě nejsou k dispozici, vyhodnocení serveru bere v úvahu pouze počet síťových adaptérů pro velikost virtuálního počítače.

#### <a name="calculate-compute-sizing"></a>Vypočítat velikost výpočetní hodnoty

Po výpočtu požadavků na úložiště a síť posouzení serveru považuje požadavky na procesor a RAM najít vhodnou velikost virtuálního počítače v Azure.

- Azure Migrate vyhledá efektivní využívaná jádra a paměť RAM a najde vhodnou velikost virtuálního počítače Azure.
- Pokud není nalezena žádná vhodná velikost, je počítač označen jako nevhodný pro Azure.
- Pokud je nalezena vhodná velikost, Azure Migrate použije výpočty úložiště a sítě. Potom použije nastavení umístění a cenové úrovně pro konečné doporučení velikosti virtuálního počítače.
- Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.

## <a name="confidence-ratings-performance-based"></a>Hodnocení spolehlivosti (založené na výkonu)

Každé hodnocení založené na výkonu v Azure Migrate je přidruženo k hodnocení spolehlivosti. Hodnocení se pohybuje od jedné (nejnižší) do pěti (nejvyšších) hvězd. Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení velikosti, která Azure Migrate poskytuje.

- Hodnocení spolehlivosti je přiřazeno k posouzení. Hodnocení je založeno na dostupnosti datových bodů, které jsou potřebné k výpočtu hodnocení.
- Pro velikost na základě výkonu, server hodnocení potřebuje:
    - Data využití paměti CPU a paměti RAM virtuálního počítače.
    - Data viopů disku a propustnost pro každý disk připojený k virtuálnímu počítače.
    - Vstupně-založené na síťovém vstupně-založené pro zpracování velikosti založené na výkonu pro každý síťový adaptér připojený k virtuálnímu virtuálnímu připojení.

Pokud některý z těchto čísel využití není k dispozici, velikost doporučení může být nespolehlivé.

> [!NOTE]
> Hodnocení spolehlivosti se nepřiřazuje pro servery hodnocené pomocí importovaného souboru CSV. Hodnocení se také nevztahují na místní hodnocení podle pohlaví.

### <a name="ratings"></a>Ratings

V této tabulce jsou uvedeny hodnocení spolehlivosti hodnocení, která závisí na procentu dostupných datových bodů:

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0-20% | 1 hvězdička
   21-40% | 2 hvězdičky
   41-60% | 3 hvězdičky
   61-80% | 4 hvězdičky
   81-100% | 5 hvězdiček

### <a name="low-confidence-ratings"></a>Nízké hodnocení spolehlivosti

Zde je několik důvodů, proč by hodnocení mohlo získat nízké hodnocení spolehlivosti:

- Neprofilovat své prostředí po dobu, po kterou vytváříte hodnocení. Pokud například vytvoříte hodnocení s dobou trvání výkonu nastavenou na jeden den, musíte počkat alespoň jeden den po spuštění zjišťování pro všechny datové body, které mají být shromážděny.
- Některé virtuální společnosti byly vypnuty během doby, pro kterou bylo vypočteno posouzení. Pokud jsou některé virtuální servery po určitou dobu vypnuté, hodnocení serveru nemůže shromažďovat údaje o výkonu za toto období.
- Některé virtuální společnosti byly vytvořeny během doby, pro kterou bylo vypočteno posouzení. Předpokládejme například, že jste vytvořili hodnocení historie výkonu za poslední měsíc, ale některé virtuální aplikace byly vytvořeny teprve před týdnem. Historie výkonu nových virtuálních discích nebude existovat po celou dobu trvání.

> [!NOTE]
> Pokud je hodnocení spolehlivosti jakéhokoli hodnocení menší než pět hvězdiček, doporučujeme počkat alespoň jeden den, než zařízení profiluje prostředí, a poté přepočítat hodnocení. V opačném případě může být změna velikosti založená na výkonu nespolehlivá. V takovém případě doporučujeme přepnout hodnocení na místní velikosti.

## <a name="calculate-monthly-costs"></a>Výpočet měsíčních nákladů

Po dokončení doporučení pro nastavení velikosti Azure Migrate vypočítá náklady na výpočetní prostředky a úložiště po migraci.

- **Náklady na výpočetní výkon**: Migrace Azure používá doporučenou velikost virtuálního počítače Azure a rozhraní API pro fakturaci Azure k výpočtu měsíčních nákladů na virtuální počítač.

    Výpočet bere v úvahu:
    - Operační systém
    - Zajištění softwaru
    - Rezervované instance
    - Doba provozu virtuálního počítače
    - Umístění
    - Nastavení měny

    Vyhodnocení serveru agreguje náklady napříč všemi počítači a vypočítá celkové měsíční náklady na výpočetní prostředky.

- **Náklady na úložiště**: Měsíční náklady na úložiště pro počítač se vypočítá agregací měsíčních nákladů na všechny disky, které jsou připojeny k počítači.

    Vyhodnocení serveru vypočítá celkové měsíční náklady na úložiště agregací nákladů na úložiště všech počítačů. V současné době výpočet nebere v úvahu nabídky zadané v nastavení hodnocení.

Náklady jsou zobrazeny v měně zadané v nastavení hodnocení.

## <a name="next-steps"></a>Další kroky

[Projděte si](best-practices-assessment.md) osvědčené postupy pro vytváření hodnocení. 

- Další informace o spuštění hodnocení pro [virtuální zařízení VMware](tutorial-prepare-vmware.md), [virtuální chod y Hyper-V](tutorial-prepare-hyper-v.md)a [fyzické servery](tutorial-prepare-physical.md).
- Informace o hodnocení serverů [importovaných pomocí souboru CSV](tutorial-assess-import.md).
- Informace o nastavení [vizualizace závislostí](concepts-dependency-visualization.md).
